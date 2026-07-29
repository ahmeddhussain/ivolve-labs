# Lab 25: GitOps Workflow with Jenkins and ArgoCD

## Overview

This lab introduces the **GitOps** pattern — instead of Jenkins deploying directly to the Kubernetes cluster (as in Lab 22/23), Jenkins now only builds, pushes the image, and updates the `deployment.yaml` manifest **in Git**. **ArgoCD** watches that Git repository and automatically syncs any change to the cluster.

This is the key shift from earlier labs:

| Before (Lab 22/23) | Now (Lab 25) |
|---|---|
| Jenkins runs `kubectl apply` directly | Jenkins only commits the updated manifest to Git |
| Cluster state is pushed to by CI | Cluster state is pulled/reconciled by ArgoCD |
| Deploy credentials live in Jenkins | Deploy credentials live only in ArgoCD |

The full flow:

1. ArgoCD is installed in the Kubernetes cluster
2. Jenkins builds the app, builds/pushes the Docker image, deletes the local image
3. Jenkins updates `deployment.yaml` with the new image tag and **pushes that change back to GitHub**
4. ArgoCD detects the Git change and automatically deploys it — no direct cluster access from Jenkins at all

<br>

---

## Prerequisites

- A working Kubernetes cluster (Minikube, as used in prior labs)
- Jenkins with the pipeline from Lab 22/23 as a base (Docker Hub credentials, Maven, Docker CLI)
- A GitHub repository Jenkins can push to (a fork of `Jenkins_App`, or a separate manifests repo)
- A GitHub Personal Access Token (PAT) with repo write access, stored in Jenkins credentials

<br>

---

## Part 1: Install ArgoCD in the Cluster

### Step 1: Create the ArgoCD namespace

```bash
kubectl create namespace argocd
```

### Step 2: Install ArgoCD

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```



### Step 3: Wait for all ArgoCD pods to be running

```bash
kubectl get pods -n argocd -w
```

Wait until every pod shows `Running` and `1/1` or `2/2` ready before continuing (press Ctrl+C to stop watching).

<![alt text](image.png)

### Step 4: Expose the ArgoCD API/UI

For a lab environment, the simplest approach is `port-forward`:

```bash
kubectl port-forward svc/argocd-server -n argocd 8081:443
```

Or, on Minikube, expose it as a NodePort service:

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
minikube service argocd-server -n argocd --url
```
![alt text](image-1.png)
### Step 5: Retrieve the initial admin password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```

### Step 6: Log in to the ArgoCD UI

Open  the Minikube URL from Step 4 in a browser, accept the self-signed cert warning, and log in:

- **Username**: `admin`
- **Password**: the value from Step 5

![alt text](image-2.png)
---

## Part 2: Create the ArgoCD Application

Point ArgoCD at the Git repository holding `deployment.yaml` so it can watch and sync it automatically.

#### Via the UI, connect the repo:

- Click Settings → Conect Repositry
- Choose `VIA HTTP/HTTPS`
- Name: `jenkins-app`
- Project: `default`
- Repository URL: https://github.com/ahmeddhussain/ivolve-jenkins-1.git
- Optional: Configure username and password
- Select Connect

#### Via the UI, create new app:
- Click *+ New App*
- **Application Name**: `jenkins-app`
- **Project**: `default`
- **Sync Policy**: `Automatic` (enables auto-sync so ArgoCD deploys new commits without manual intervention)
- **Repository URL**: `https://github.com/ahmeddhussain/ivolve-jenkins-1.git`
- **Path**: `.` (or the subfolder containing `deployment.yaml`)
- **Cluster URL**: `https://kubernetes.default.svc` (in-cluster)
- **Namespace**: `ivolve`
- Click **Create**

![alt text](image-3.png)


---

## Part 3: Configure the Jenkins Pipeline (GitOps Style)

### Step 1: Add GitHub credentials to Jenkins

```text
Manage Jenkins → Credentials → Add Credentials
```

- **Kind**: Username with password (use your GitHub username + Personal Access Token as the password)
- **ID**: `git-hub`

### Step 2: The Jenkinsfile

```groovy
@Library('jenkins-shared-lib') _
pipeline {
    agent { any }
    tools {
        maven 'Maven3'
    }

    environment {
        IMAGE_NAME = "ahmedkhater2611/jenkins-app"
        IMAGE_TAG  = "${BUILD_NUMBER}"
        NAMESPACE  = "ivolve"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ahmeddhussain/ivolve-jenkins-1.git'
            }
        }
        stage('Build App') {
            steps {
                buildApp()
            }
        }
        stage('Build & Push Image') {
            steps {
                buildImage(
                    imageName: "${IMAGE_NAME}",
                    imageTag:  "${IMAGE_TAG}",
                    credsId:   'docker'
                )
            }
        }
        stage('Update deployment.yaml') {
            steps {
                sh "sed -i 's|image:.*|image: ${IMAGE_NAME}:${IMAGE_TAG}|' deployment.yaml"
            }
        }
        stage('Push Update to GitHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'git-hub',
                    usernameVariable: 'GIT_USER',
                    passwordVariable: 'GIT_TOKEN'
                )]) {
                    sh """
                        git config user.name "jenkins-ci"
                        git config user.email "jenkins-ci@example.com"
                        git add deployment.yaml
                        git commit -m "Update image to ${IMAGE_NAME}:${IMAGE_TAG}"
                        git push https://\$GIT_USER:\$GIT_TOKEN@github.com/ahmeddhussain/ivolve-jenkins-1.git HEAD:main
                    """
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Manifest updated and pushed — ArgoCD will sync the new deployment automatically.'
        }
        failure {
            echo 'Pipeline failed — check logs above.'
        }
    }
}
```

**Note the key difference from Lab 22/23**: there is no `Deploy to K8s Cluster` stage, no `kubectl apply`, and no kubeconfig/ServiceAccount token credential needed at all. Jenkins' job ends at pushing the updated manifest to Git — deployment is entirely ArgoCD's responsibility from there, but still using the shared lib.

![alt text](image-4.png)

---

## Part 4: Validate ArgoCD Deploys the New App

### Step 1: Watch the ArgoCD UI

After the pipeline pushes the commit, ArgoCD should detect the change within its polling interval (default ~3 minutes, or immediately if using a Git webhook). The Application card should transition:

- **Sync Status**: `OutOfSync` → `Synced`
- **Health Status**: `Progressing` → `Healthy`

![alt text](image-5.png)

### Step 2: Confirm the new image is running in the cluster

```bash
kubectl get pods -n ivolve
kubectl get deployment jenkins-app -n ivolve -o jsonpath='{.spec.template.spec.containers[0].image}'
```

The output should match the exact `IMAGE_NAME:IMAGE_TAG` that was just pushed and committed by the pipeline.

![alt text](image-6.png)



---

## Notes

- **GitOps** treats Git as the single source of truth for cluster state — the cluster is never modified directly; it only ever converges to whatever is committed in the repository.
- ArgoCD's **auto-sync** (`selfHeal: true`) also means manual `kubectl edit`/`kubectl apply` changes made directly to the cluster get automatically reverted back to match Git — this is a feature, not a bug, and enforces that Git truly is the only path to changing the deployed state.



- If ArgoCD isn't picking up changes quickly enough during testing, configuring a **GitHub webhook** pointing at ArgoCD's `/api/webhook` endpoint triggers instant syncs instead of waiting for the default polling interval.

- Using GitHub Action is the best practice for GitOps.