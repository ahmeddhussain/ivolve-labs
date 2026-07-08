# Lab 1: Building a Java Application with Gradle

## Build Tools Overview
This lab introduces the basic workflow of using Gradle as a build tool for a Java project. Gradle helps you:
- manage project dependencies
- run unit tests
- package the application into an artifact
- build and run the application in a repeatable way


## Prerequisites
Before starting, make sure you have:
- Java installed on your system
- Git installed on your system

## Step 1: Install Gradle
On Linux, you can install Gradle with the following commands:

```bash
sudo apt update
sudo apt install gradle -y
```

Verify the installation:

```bash
gradle -v
```

## Step 2: Clone the Source Code
Clone the repository:

```bash
git clone https://github.com/Ibrahim-Adel15/calculator-gradle.git
cd calculator-gradle
```

![Screenshot: Capture the Git clone output and the project directory.](<screenshots/Screenshot 2026-07-06 163157.png>)

## Step 3: Run Unit Tests
Run the unit tests using Gradle:

```bash
gradle test
```

This command compiles the project and runs the test cases defined in the repository.

![!\[Screenshot: Capture the test execution output showing the tests running successfully.\](<screenshots/Screenshot 2026-07-06 164746.png>)](<screenshots/Screenshot 2026-07-06 163116.png>)

## Step 4: Build the Application
Build the project and generate the artifact:

```bash
gradle clean build
```

The built artifact will be generated in:

```bash
build/libs/calculator.jar
```

![!\[Screenshot: Capture the build output and the generated JAR file in the build/libs folder.\](<screenshots/Screenshot 2026-07-06 164813.png>)](<screenshots/Screenshot 2026-07-06 163131.png>)
![alt text](<screenshots/Screenshot 2026-07-06 163149.png>)

## Step 5: Run the Application
Run the built application:

```bash
java -jar build/libs/calculator.jar
```


## Step 6: Verify the Application Is Working
Test the application by using it as intended. For example, if the calculator supports basic arithmetic, try a sample calculation and confirm that the output is correct.

You can also verify that the application started successfully by checking for:
- expected output in the terminal
- no runtime errors
- correct behavior for a sample input

![!\[Screenshot: Capture the successful output from a sample run.\](<screenshots/Screenshot 2026-07-06 164839.png>)
](<screenshots/Screenshot 2026-07-06 164839.png>)
## Notes
- If Gradle reports dependency or plugin issues, make sure Java is installed correctly and matches the project's required version.
- Many projects include a Gradle wrapper. If present, prefer using the wrapper (`./gradlew test`, `./gradlew build`) instead of the system Gradle installation.
