# iVolve Labs

A collection of 30 hands-on DevOps labs built while progressing through the iVolve DevOps track — covering build tools, containerization, CI/CD, Kubernetes, GitOps, and configuration management, in that progression.

Each lab lives in its own folder with a dedicated `README.md` documenting the objective, step-by-step walkthrough, and configuration files used, along with screenshots verifying the result.

## Repository Structure

```
ivolve-labs/
├── buildtools/   # Java build tooling (Gradle, Maven)
├── docker/       # Containerization, images, volumes, networks, Compose
├── jenkins/      # CI/CD pipelines, security, shared libraries
├── k8s/          # Kubernetes workloads, storage, networking, RBAC
├── argocd/       # GitOps deployment workflow
└── ansible/      # Configuration management and automation
```

## Skills Demonstrated

- **CI/CD**: Declarative Jenkins pipelines, role-based Jenkins security, dedicated build agents, shared pipeline libraries, GitOps handoff to ArgoCD
- **Containerization**: Multi-stage builds, environment variable management, custom networks, volumes/bind mounts, Docker Compose multi-service stacks
- **Kubernetes**: Deployments, StatefulSets, DaemonSets, Services (ClusterIP/Headless), Ingress, ConfigMaps/Secrets, PV/PVC, Init Containers, taints/tolerations, resource requests/limits, NetworkPolicy, RBAC/ServiceAccounts
- **Configuration Management**: Ansible ad-hoc commands, playbooks, roles, Vault-encrypted secrets, AWS dynamic inventory
- **Build Tools**: Gradle and Maven for Java application builds

## Labs

### Build Tools

| Lab | Topic | What it covers |
|-----|-------|-----------------|
| [Lab 1](buildtools/lab1) | Java App with Gradle | Uses Gradle to manage dependencies, run unit tests, and package a Java app into a build artifact. |
| [Lab 2](buildtools/lab2) | Java App with Maven | Same workflow as Lab 1 using Maven, for comparison between the two build tools. |

### Docker

| Lab | Topic | What it covers |
|-----|-------|-----------------|
| [Lab 3](docker/lab3) | Containerize a Spring Boot App | Clones a Java Spring Boot app, writes a Dockerfile, builds an image, and runs/tests it in a container. |
| [Lab 4](docker/lab4) | Alternate Spring Boot Containerization | Rebuilds the same app using a Java 17 base image and copying in a pre-built JAR, as a second containerization approach. |
| [Lab 5](docker/lab5) | Multi-Stage Build (Node.js) | Builds a smaller, more efficient image with a two-stage Dockerfile — one stage builds the app, the second copies only the final artifact into a lean runtime image. |
| [Lab 6](docker/lab6) | Docker Environment Variables | Compares three ways to inject env vars into a container: `docker run -e`, an `--env-file`, and hardcoding in the Dockerfile. |
| [Lab 7](docker/lab7) | Volumes and Bind Mounts (Nginx) | Uses a named volume to persist Nginx logs and a bind mount to serve a local HTML file from the host inside the container. |
| [Lab 8](docker/lab8) | Custom Docker Network | Creates a user-defined bridge network so a frontend and backend container in a microservices setup can resolve and reach each other by name. |
| [Lab 9](docker/lab9) | Docker Compose Stack | Runs a Node.js app and a MySQL database together with Docker Compose, wiring the app's DB env vars to the `ivolve` database. |

### Kubernetes

| Lab | Topic | What it covers |
|-----|-------|-----------------|
| [Lab 10](k8s/lab10) | Node Taints | Taints a worker node with `NoSchedule` so pods are excluded from it unless they carry a matching toleration; verifies scheduling behavior. |
| [Lab 11](k8s/lab11) | Namespaces & ResourceQuota | Creates an `ivolve` namespace and applies a ResourceQuota capping it at two pods, then confirms the quota is enforced. |
| [Lab 12](k8s/lab12) | ConfigMaps & Secrets | Stores non-sensitive MySQL config in a ConfigMap and sensitive credentials (Base64-encoded) in a Secret, both consumed by a workload. |
| [Lab 13](k8s/lab13) | Persistent Storage (PV/PVC) | Provisions a `hostPath` PersistentVolume and binds it with a PersistentVolumeClaim to persist application logs on the node's filesystem. |
| [Lab 14](k8s/lab14) | StatefulSet & Headless Service | Deploys MySQL as a StatefulSet pulling credentials from a Secret and storage from a PVC, tolerating the tainted node, and exposed via a Headless Service for stable pod identity. |
| [Lab 15](k8s/lab15) | Node.js Deployment & ClusterIP | Deploys the Node.js app via a Deployment, wired to the ConfigMap/Secret and PVC from earlier labs, exposed internally through a ClusterIP Service. |
| [Lab 16](k8s/lab16) | Init Containers | Adds an Init Container that connects to MySQL, creates the `ivolve` database and app user, and grants privileges — gating the main app container until setup succeeds. |
| [Lab 17](k8s/lab17) | Resource Requests & Limits | Sets CPU/memory requests and limits on the Node.js Deployment and validates actual usage with `kubectl top`. |
| [Lab 18](k8s/lab18) | NetworkPolicy | Locks down MySQL pod access with a NetworkPolicy so only the Node.js app pods can reach port 3306 — all other pod-to-pod traffic to MySQL is blocked. |
| [Lab 19](k8s/lab19) | DaemonSet | Deploys Prometheus `node-exporter` as a DaemonSet with tolerations for all taints, ensuring a metrics-collecting pod runs on every node including the tainted one. |
| [Lab 20](k8s/lab20) | RBAC & Service Accounts | Creates a `jenkins-sa` ServiceAccount, a read-only Role for Pods, and a RoleBinding, then validates the generated token's permissions. |

### Jenkins

| Lab | Topic | What it covers |
|-----|-------|-----------------|
| [Lab 21](jenkins/lab21) | Role-Based Authorization | Secures Jenkins with the Role-based Authorization Strategy plugin, setting up an admin user with full access and a read-only user with restricted permissions. |
| [Lab 22](jenkins/lab22) | Declarative CI/CD Pipeline | Builds a full pipeline: clone from GitHub → run tests → build the app → build & push a Docker image → clean up locally → update the Kubernetes `deployment.yaml`. |
| [Lab 23](jenkins/lab23) | Jenkins Agents & Shared Libraries | Extends Lab 22 by offloading pipeline runs to a dedicated build agent (instead of the controller) and extracting reusable pipeline steps (`BuildApp`, `BuildImage`, `DeployOnK8s`) into a versioned shared library. |

### ArgoCD

| Lab | Topic | What it covers |
|-----|-------|-----------------|
| [Lab 25](argocd/lab25) | GitOps with Jenkins + ArgoCD | Shifts from Jenkins directly running `kubectl apply` to Jenkins only committing the updated manifest to Git, while ArgoCD watches the repo and reconciles the cluster automatically — separating CI from CD and keeping deploy credentials out of Jenkins. |

### Ansible

| Lab | Topic | What it covers |
|-----|-------|-----------------|
| [Lab 26](ansible/lab26) | Initial Setup & Ad-Hoc Commands | Installs Ansible on a control node, sets up passwordless SSH to a managed node, defines a static inventory, and runs an ad-hoc disk-space check to confirm connectivity. |
| [Lab 27](ansible/lab27) | Web Server Playbook | Writes a repeatable Ansible Playbook that installs Nginx, deploys a custom index page, and ensures the service is enabled and running. |
| [Lab 28](ansible/lab28) | Ansible Roles | Refactors automation into three reusable roles (Docker, kubectl, Jenkins) and runs them all from a single playbook against the managed node. |
| [Lab 29](ansible/lab29) | Ansible Vault | Installs MySQL, creates a database and dedicated user via a playbook that pulls the user's password from an Ansible Vault-encrypted file instead of plaintext. |
| [Lab 30](ansible/lab30) | AWS Dynamic Inventory | Replaces the static inventory with the `amazon.aws` dynamic inventory plugin, auto-discovering EC2 hosts by tag and running the MySQL role from Lab 29 against them without hardcoding IPs. |

## How to Use This Repo

Each lab folder is self-contained:
- A `README.md` explains the objective, walkthrough, and key concepts.
- Configuration files (Dockerfiles, YAML manifests, playbooks, etc.) are included alongside the README.
- Screenshots document the verification/output steps where applicable.

Browse into any lab folder and start with its `README.md`.

## About

Built while progressing through the iVolve DevOps Internship, moving from build tooling and containerization fundamentals through Kubernetes orchestration, Jenkins CI/CD, GitOps with ArgoCD, and Ansible-based configuration management All with DevOps Best Practices.