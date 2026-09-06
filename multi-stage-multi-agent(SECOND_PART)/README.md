## Project PART 2: Multi-Stage Jenkins Pipeline for a Three-Tier Application

This project demonstrates a multi-stage Jenkins CI/CD pipeline using Docker-based agents for different application environments.

The application consists of three layers:

- **Frontend** – User interface
- **Backend** – Application logic and APIs
- **Database** – Stores application data

Managing different application environments manually can be time-consuming and may lead to dependency conflicts, configuration issues, and inconsistent environments.

## Solution

Jenkins can automate the CI/CD process by using different Docker-based agents for different pipeline stages.

Each stage uses a Docker image containing the tools and dependencies required for that stage.

```text
Backend  → Maven + Java 11 Docker Container
Frontend → Node.js 16 Docker Container
Database → MySQL 8.0 Docker Container
```

## Implementing the Multi-Stage Pipeline

### 1. Create the Jenkinsfile

Create a folder named:

```text
multi-stage-multi-agent(SECOND_PART)
```

Inside this folder, create:

```text
Jenkinsfile
```

The project structure should look like:

```text
my-repository/
└── multi-stage-multi-agent(SECOND_PART)/
    ├── Jenkinsfile
    └── Readme.md
```

### 1.1 Add the Pipeline

Add the following pipeline to the `Jenkinsfile`:

```groovy
pipeline {
    agent none

    stages {

        stage('Back-end') {
            agent {
                docker {
                    image 'maven:3.8.1-adoptopenjdk-11'
                }
            }
            steps {
                sh 'mvn --version'
            }
        }

        stage('Front-end') {
            agent {
                docker {
                    image 'node:16-alpine'
                }
            }
            steps {
                sh 'node --version'
            }
        }

        stage('Database') {
            agent {
                docker {
                    image 'mysql:8.0'
                }
            }
            steps {
                sh 'mysql --version'
            }
        }
    }
}
```

## 2. How This Pipeline Works

The pipeline uses:

```text
agent none
```

This means Jenkins does not use one common agent for the entire pipeline. Each stage defines its own Docker-based agent.

### Back-end Stage

```groovy
stage('Back-end') {
    agent {
        docker {
            image 'maven:3.8.1-adoptopenjdk-11'
        }
    }
    steps {
        sh 'mvn --version'
    }
}
```

Jenkins creates a temporary Docker container using the Maven image.

The image provides:

```text
Maven
Java 11
```

The following command verifies the environment:

```bash
mvn --version
```

### Front-end Stage

```groovy
stage('Front-end') {
    agent {
        docker {
            image 'node:16-alpine'
        }
    }
    steps {
        sh 'node --version'
    }
}
```

Jenkins creates a separate Docker container using the Node.js image.

The image provides:

```text
Node.js 16
```

The following command verifies the environment:

```bash
node --version
```

### Database Stage

```groovy
stage('Database') {
    agent {
        docker {
            image 'mysql:8.0'
        }
    }
    steps {
        sh 'mysql --version'
    }
}
```

Jenkins creates another Docker container using the MySQL image.

The image provides:

```text
MySQL 8.0
```

The following command verifies the MySQL environment:

```bash
mysql --version
```

## 3. Pipeline Flow

```text
                         Jenkins
                            ↓
                        agent none
                            ↓
              ┌─────────────┼─────────────┐
              ↓             ↓             ↓
         Back-end       Front-end      Database
              ↓             ↓             ↓
       Maven + Java       Node.js        MySQL
        Docker           Docker         Docker
       Container         Container      Container
              ↓             ↓             ↓
       mvn --version   node --version  mysql --version
```

Each stage runs in its own Docker container with the environment required for that stage.

After the stage finishes, the temporary container is removed, while the Docker image remains available for future builds.

## 4. Create a Pipeline Job in Jenkins

1. Open the **Jenkins Dashboard**.
2. Click **New Item**.
3. Enter the pipeline name:

```text
multi-stage-three-tier-pipeline
```

4. Select **Pipeline**.
5. Click **OK**.

### 4.1 Connect the GitHub Repository

In the **Pipeline** section, select:

```text
Definition → Pipeline script from SCM
SCM → Git
```

Enter your GitHub repository URL.

Set the branch:

```text
*/main
```

Set the Script Path according to the Jenkinsfile location:

```text
multi-stage-multi-agent(SECOND_PART)/Jenkinsfile
```

Click **Save**.

## 5. Run the Pipeline

1. Click **Build Now** to execute the pipeline.
2. Open:

```text
Build Number → Console Output
```

You should see output similar to:

```text
[Pipeline] Start of Pipeline

[Pipeline] stage
[Pipeline] { (Back-end)

Apache Maven 3.8.1
Maven home: /usr/share/maven
Java version: 11.x.x
Java home: /opt/java/openjdk

[Pipeline] }

[Pipeline] stage
[Pipeline] { (Front-end)

v16.x.x

[Pipeline] }

[Pipeline] stage
[Pipeline] { (Database)

mysql  Ver 8.0.x for Linux on x86_64

[Pipeline] }

[Pipeline] End of Pipeline

Finished: SUCCESS
```

A successful build confirms that Jenkins can run different pipeline stages using separate Docker-based agents.

## 🔑 Key Benefit

Jenkins provides each stage with its required environment through a Docker image, reducing the need to manually install and maintain different dependencies on the Jenkins agent.

## 🚀 What I Implemented

- 🔹 Created a **multi-stage Jenkins pipeline**
- 🐳 Configured **Docker-based agents** for each stage
- ☕ Used **Maven + Java 11** for Back-end
- ⚛️ Used **Node.js 16** for Front-end
- 🗄️ Used **MySQL 8.0** for Database
- ✅ Executed and verified the pipeline successfully

### 🎯 Key Takeaway

Learned how **Jenkins + Docker** work together to provide isolated, consistent, and reproducible environments for different CI/CD stages.
