# Lab 1: Building a Java Application with Maven

## Build Tools Overview
This lab introduces the basic workflow of using Maven as a build tool for a Java project. Maven helps you:
- manage project dependencies
- run unit tests
- package the application into an artifact
- build and run the application in a repeatable way


## Prerequisites
Before starting, make sure you have:
- Java installed on your system
- Git installed on your system

## Step 1: Install Maven
On Linux, you can install Maven with the following commands:

```bash
sudo apt update
sudo apt install maven -y
```

Verify the installation:

```bash
mvn -version
```

## Step 2: Clone the Source Code
Clone the repository:

```bash
git clone https://github.com/Ibrahim-Adel15/calculator-maven.git
cd calculator-maven
```

![Screenshot: Capture the Git clone output and the project directory.
](<screenshots/Screenshot 2026-07-06 163157.png>)
## Step 3: Run Unit Tests
Run the unit tests using Maven:

```bash
mvn test
```

This command compiles the project and runs the test cases defined in the repository.

![Screenshot: Capture the test execution output showing the tests running successfully.
](<screenshots/Screenshot 2026-07-06 164746.png>)
## Step 4: Build the Application
Build the project and generate the artifact:

```bash
mvn clean package
```

The built artifact will be generated in:

```bash
target/calculator.jar
```

![Screenshot: Capture the build output and the generated JAR file in the target folder.](<screenshots/Screenshot 2026-07-06 164813.png>)

## Step 5: Run the Application
Run the built application:

```bash
java -jar target/calculator.jar
```



## Step 6: Verify the Application Is Working
Test the application by using it as intended. For example, if the calculator supports basic arithmetic, try a sample calculation and confirm that the output is correct.

You can also verify that the application started successfully by checking for:
- expected output in the terminal
- no runtime errors
- correct behavior for a sample input

![Screenshot: Capture the successful output from a sample run.](<screenshots/Screenshot 2026-07-06 164839.png>)

## Notes
- If Maven reports dependency or plugin issues, make sure Java is installed correctly and same version as the build.
- The artifact name may vary depending on the project configuration, but the goal is to generate the runnable JAR file in the target directory.

