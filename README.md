# 🚀 Jenkins with Docker Agents on AWS EC2

## 📌 Project Overview

This project demonstrates how to set up **Jenkins on an AWS EC2 instance** and use **Docker containers as Jenkins Agent nodes** for executing CI/CD jobs.

Instead of creating multiple EC2 instances as Jenkins worker nodes, Docker containers are used as temporary and isolated Jenkins agents. This approach helps reduce infrastructure requirements and makes it easier to create and manage build environments.

💡 Why Docker Agents?

In a traditional Jenkins setup, multiple worker nodes can be created using separate EC2 instances.

For example:

```text
Jenkins Controller
       |
       +---- EC2 Worker 1
       |
       +---- EC2 Worker 2
       |
       +---- EC2 Worker 3
```
However, creating a separate EC2 instance for every worker can increase infrastructure and maintenance overhead.

Instead, Docker containers can be used as Jenkins agents:

```text
Jenkins Controller
       |
       v
     Docker
       |
       +---- 🐳 Jenkins Agent
       |
       +---- 🐳 Jenkins Agent
       |
       +---- 🐳 Jenkins Agent
```
Docker containers are lightweight, isolated, and can be created when required. One of the major advantages of using Docker as a Jenkins Agent is that the agents can be **created and removed dynamically based on the workload**.

In a traditional Jenkins architecture, if separate EC2 instances are used as worker nodes, the worker instances may remain running even when there are no jobs to execute.

📌 Steps
## 1️⃣ Create Ubuntu EC2 Instance

Create a simple Ubuntu EC2 instance on AWS.

<img width="1547" height="262" alt="image" src="https://github.com/user-attachments/assets/144a77e3-92cd-42a7-93d4-593dd634d9fb" />

Once the instance is running, connect to it using SSH.

```text
ssh -i <key.pem> ubuntu@<EC2-PUBLIC-IP>

```

## 2️⃣ Update the System
Before installing Jenkins, Docker, and other required tools, update the packages on the EC2 instance.

### Update Package List

```bash
sudo apt update
```

This command refreshes the local package information and checks for the latest available versions of packages.

### Upgrade Installed Packages

```bash
sudo apt upgrade -y
```
This command upgrades the currently installed packages to their latest available versions.

## 3️⃣ Install Java ☕

Jenkins is a Java-based application, so Java needs to be installed before installing Jenkins.

Install Java:

```bash
sudo apt install fontconfig openjdk-21-jre -y
```
### Verify Java Installation

```bash
java -version
```
This confirms that Java has been installed successfully and displays the installed Java version.
Update Packages

<img width="912" height="105" alt="image" src="https://github.com/user-attachments/assets/f249455d-38f5-4def-a489-29805508ba5d" />

Now, you can proceed with installing Jenkins

## 4️⃣ Install Jenkins 🔧

Add the Jenkins repository key:

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
```
This downloads the Jenkins repository signing key so that Ubuntu can securely verify packages coming from the Jenkins repository.

### Add Jenkins Repository

```bash
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian-stable binary/ | \
sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```
This adds the Jenkins LTS repository to the EC2 instance.

### Update Package Information

```bash
sudo apt update
```

<img width="1062" height="512" alt="image" src="https://github.com/user-attachments/assets/f09e8357-8fbb-4b15-824f-a06d2f83568c" />

This refreshes the package list after adding the Jenkins repository.

### Install Jenkins

```bash
sudo apt install jenkins -y
```

This installs Jenkins on the EC2 instance.

## 5️⃣ Start & Enable Jenkins

Start the Jenkins service:

```bash
sudo systemctl start jenkins
```

This starts Jenkins immediately.

### Enable Jenkins at Boot

```bash
sudo systemctl enable jenkins
```

This ensures Jenkins automatically starts whenever the EC2 instance is restarted.

### Check Jenkins Status

```bash
sudo systemctl status jenkins
```

This verifies whether the Jenkins service is running correctly.

The status should show:

```text
active (running)
```

## Check Jenkins Port 🔌

```text
sudo ss -tulpn | grep 8080
```
<img width="1095" height="102" alt="image" src="https://github.com/user-attachments/assets/0f9c4fd8-8ba4-4d07-95dc-a11e6943abc1" />

By default, Jenkins runs on port 8080.

Check whether Jenkins is listening on port 8080:

Jenkins uses port 8080 by default for its web interface. Therefore, browser requests need to reach port 8080 on the EC2 instance.
**Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.

1. EC2 > Instances > Click on
2. In the bottom tabs -> Click on Security
3. Security groups
4. Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed All traffic).

<img width="1892" height="591" alt="image" src="https://github.com/user-attachments/assets/9c6b3b8a-c936-44de-92f6-884be58efdb8" />

💡 Why is this required?

AWS Security Groups act as a virtual firewall for EC2 instances. Allowing port 8080 enables traffic from the browser to reach the Jenkins service running on the EC2 instance.

## 6️⃣ Access Jenkins 🌐

After allowing port 8080 in the Security Group, open Jenkins in a web browser.

Use the following URL:

```text
http://<EC2-PUBLIC-IP>:8080
```
🎉 The Jenkins setup page should now be displayed.

<img width="1462" height="897" alt="image" src="https://github.com/user-attachments/assets/5412e7a6-e73e-4d5f-9278-37fa90825ead" />

### Get Initial Jenkins Admin Password 
When Jenkins is installed for the first time, it generates an initial administrator password.

Retrieve the password using:

```text
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

<img width="876" height="55" alt="image" src="https://github.com/user-attachments/assets/4ae974c5-c105-4d7e-bb25-fe6d9f047875" />

Copy the password from the terminal and enter it on the Jenkins setup page.

💡 Why do we need this password?

Jenkins uses this temporary password to securely unlock the Jenkins dashboard during the first-time setup.

<img width="1276" height="830" alt="image" src="https://github.com/user-attachments/assets/07393301-5223-40a6-ba67-2ff4a1927c64" />

### Complete Jenkins Setup

Click on Install suggested plugins. Jenkins will install the commonly used plugins required for a basic Jenkins setup.

<img width="1247" height="875" alt="image" src="https://github.com/user-attachments/assets/e0216ba3-c3c6-4247-b94c-b4a7bb5d3947" />

Wait for the Jenkins to Install suggested plugins

### Create Admin User

Create the first Jenkins administrator account by providing:

Username
Password
Full Name
Email Address

<img width="1321" height="868" alt="image" src="https://github.com/user-attachments/assets/63851572-4de6-4dc3-ba6f-c0b8565a0a93" />

✅ Complete Setup

After creating the admin user, complete the setup process. Once the setup is completed, the Jenkins Dashboard will be available.

Jenkins is now successfully installed and configured on an Ubuntu EC2 instance. Jenkins can now be accessed using:

```text
http://35.174.115.40:8080/
```
<img width="730" height="452" alt="image" src="https://github.com/user-attachments/assets/7e07661b-8159-4cd8-913a-74166e89ffcd" />

Jenkins Installation is Successful. You can now starting using the Jenkins

<img width="1827" height="796" alt="image" src="https://github.com/user-attachments/assets/22579eb5-ea82-4c03-9ba1-42227f6f5f31" />


## 7️⃣ Install Docker(Docker Slave Configuration) 🐳

Run the below command to Install Docker:

```text
 sudo apt update
 sudo apt install docker.io -y
```
This installs Docker on the same EC2 instance where Jenkins is running.

### Verify Docker

```bash
docker --version
```

This confirms that Docker has been installed successfully.

<img width="997" height="502" alt="image" src="https://github.com/user-attachments/assets/ed4b583b-cc12-4ab4-b2aa-17a94734b415" />

## 8️⃣ Give Jenkins & Ubuntu Docker Permissions 🔐

Jenkins needs permission to execute Docker commands because the Jenkins pipeline will build Docker images and run Docker containers. Jenkins is automating the process of using Docker.
This means:

  "Allow the Jenkins user to use Docker. So here we are adding jenkins user to the docker group"

Add Jenkins to the Docker group:

```bash
sudo usermod -aG docker jenkins
```
The `jenkins` user runs the Jenkins service, so this allows Jenkins to use Docker without requiring `sudo` for every Docker command.

### Give Ubuntu User Docker Permission

```bash
sudo usermod -aG docker ubuntu
```

This allows the `ubuntu` user to run Docker commands without using `sudo`.

For example:

```bash
docker ps
```

instead of:

```bash
sudo docker ps
```
### Restart Jenkins

```bash
sudo systemctl restart jenkins
```

This restarts Jenkins so that the updated Docker group permissions are applied to the Jenkins service.

### Optional: Switch to Root and Run All Commands Together

The same configuration can also be performed using:

```bash
sudo su -
```

Then:

```bash
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart jenkins
```

<img width="646" height="165" alt="image" src="https://github.com/user-attachments/assets/46aefdce-ab1e-4d49-8ffa-a0b78b209fc2" />

Now jenkin user is able to create or run container.

<img width="933" height="528" alt="image" src="https://github.com/user-attachments/assets/55442fc0-08ee-49fd-b74d-8173d292c3d7" />

### 🔄 Restart Jenkins

Sometimes Jenkins may not immediately pick up configuration or plugin changes. 
As a good practice, restart Jenkins once the setup is complete.

### Restart using the browser

Open:

```text
http://<EC2-PUBLIC-IP>:8080/restart
```

<img width="1487" height="812" alt="image" src="https://github.com/user-attachments/assets/08ff6e55-ec2f-45e2-91ef-0fe97b1d5b2a" />

## 9️⃣ Install Docker Pipeline Plugin 🔌

1. Log in to Jenkins.
2. Go to Manage Jenkins > Manage Plugins.
3. In the Available tab, search for "Docker Pipeline".
4. Select the plugin and click the Install button.
5. Restart Jenkins after the plugin is installed.

<img width="1915" height="853" alt="image" src="https://github.com/user-attachments/assets/5a1e3074-edf5-48ce-8f8b-0a5552bff260" />

NOTE: For old version you will see "plugin" only 
<img width="1415" height="422" alt="image" src="https://github.com/user-attachments/assets/f80d5540-75a3-4707-a1b8-f50de4b77fd5" />

Three plugins are installed: 
<img width="1232" height="675" alt="image" src="https://github.com/user-attachments/assets/cc8953e3-8d68-48fc-a467-8f131ad2e630" />

After installation, restart Jenkins if required, otherwise changes will not be reflected. 

### Why Do We Need This Plugin?

When a Jenkins Pipeline specifies a Docker environment or Docker-related configuration in the `Jenkinsfile`, Jenkins needs the Docker Pipeline plugin to process that configuration.

The flow will be:

```text
Jenkins
   │
   ▼
Jenkinsfile
   │
   ▼
Docker Pipeline Plugin
   │
   ▼
Docker
   │
   ▼
Docker Agent / Container
```

### 🔟 Now we will create our first pipeline: 


<img width="1152" height="852" alt="image" src="https://github.com/user-attachments/assets/f3fbb434-995b-45f2-96a8-2ec69a96f418" />
In this "pipiline" option you can write your pipeline in code.

<img width="1271" height="711" alt="image" src="https://github.com/user-attachments/assets/b26593d6-3712-47cf-8bf3-df4a1b42a608" />


<img width="1387" height="811" alt="image" src="https://github.com/user-attachments/assets/adda26bf-8328-4eb4-81c5-cb4babbf87a5" />


A simple jenkins pipeline to verify if the docker slave configuration is working as expected.

```text

pipeline {
  agent {
    docker { image 'node:16-alpine' }
  }
  stages {
    stage('Test') {
      steps {
        sh 'node --version'
      }
    }
  }
}
```







