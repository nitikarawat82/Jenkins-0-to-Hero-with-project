# 🚀 Jenkins Setup on Ubuntu EC2

This project demonstrates how to install and configure **Jenkins on an Ubuntu EC2 instance** and access the Jenkins dashboard through port `8080`.


📌 Steps
1️⃣ Create Ubuntu EC2 Instance

Create a simple Ubuntu EC2 instance on AWS.

<img width="1547" height="262" alt="image" src="https://github.com/user-attachments/assets/144a77e3-92cd-42a7-93d4-593dd634d9fb" />

Once the instance is running, connect to it using SSH.

```text
ssh -i <key.pem> ubuntu@<EC2-PUBLIC-IP>

```

2️⃣ Install Java ☕

Jenkins is a Java-based application, so Java needs to be installed before installing Jenkins.

Update Packages

First, update the Ubuntu package repository to get the latest package information.

```text
sudo apt update

```

Install Java 17

Install OpenJDK 17, which is required to run Jenkins.
```text
sudo apt install openjdk-21-jre
```
Verify Java Installation

Check whether Java has been installed successfully.
```text
java -version

```
<img width="912" height="105" alt="image" src="https://github.com/user-attachments/assets/f249455d-38f5-4def-a489-29805508ba5d" />

Now, you can proceed with installing Jenkins

```text
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

Check Jenkins Port 🔌
<img width="1095" height="102" alt="image" src="https://github.com/user-attachments/assets/0f9c4fd8-8ba4-4d07-95dc-a11e6943abc1" />

By default, Jenkins runs on port 8080.

Check whether Jenkins is listening on port 8080:

Jenkins uses port 8080 by default for its web interface. Therefore, browser requests need to reach port 8080 on the EC2 instance.
**Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.

1. EC2 > Instances > Click on
2. In the bottom tabs -> Click on Security
3. Security groups
4. Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed All traffic).

5. 

<img width="1892" height="591" alt="image" src="https://github.com/user-attachments/assets/9c6b3b8a-c936-44de-92f6-884be58efdb8" />

💡 Why is this required?

AWS Security Groups act as a virtual firewall for EC2 instances.

Allowing port 8080 enables traffic from the browser to reach the Jenkins service running on the EC2 instance.

7️⃣ Access Jenkins 🌐

After allowing port 8080 in the Security Group, open Jenkins in a web browser.

Use the following URL:

```text
http://<EC2-PUBLIC-IP>:8080
```
🎉 The Jenkins setup page should now be displayed.

<img width="1462" height="897" alt="image" src="https://github.com/user-attachments/assets/5412e7a6-e73e-4d5f-9278-37fa90825ead" />

8️⃣ Get Initial Jenkins Admin Password 🔐

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

Click on Install suggested plugins

<img width="1247" height="875" alt="image" src="https://github.com/user-attachments/assets/e0216ba3-c3c6-4247-b94c-b4a7bb5d3947" />

Wait for the Jenkins to Install suggested plugins











