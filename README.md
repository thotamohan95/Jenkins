# Jenkins
1. Create an AWS EC2 Instance
Steps

Go to the AWS Console.

Navigate to EC2.

Select Instances (running).

Click Launch instances.

Configure and launch the EC2 instance.

Note: Make sure the instance has sufficient resources to run Jenkins and Docker.

2. Jenkins Installation Prerequisites

Before installing Jenkins, Java must be installed.

Install Java

Run:

sudo apt update
sudo apt install openjdk-17-jre

Verify Java Installation

Run:

java -version


You should see information about the installed Java version.

3. Install Jenkins
Add the Jenkins Repository Key
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

Add the Jenkins Repository
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

Update the Package List
sudo apt-get update

Install Jenkins
sudo apt-get install jenkins

4. Configure EC2 Security Group

By default, Jenkins runs on port 8080.

AWS EC2 inbound traffic restrictions may prevent Jenkins from being accessed externally.

Open Port 8080

Navigate to:

EC2 → Instances → Select your instance → Security → Security Groups

Edit the inbound rules and add a rule allowing:

Setting	Value
Type	Custom TCP
Port	8080
Protocol	TCP
Source	Your IP address

Security Recommendation: For better security, restrict the source to your IP address where possible instead of allowing traffic from everywhere.

5. Access Jenkins

Once Jenkins is installed and port 8080 is accessible, open:

http://<EC2-PUBLIC-IP>:8080


Replace <EC2-PUBLIC-IP> with the public IP address of your EC2 instance.

6. Get the Jenkins Administrator Password

Jenkins provides an initial administrator password during the first setup.

Run:

sudo cat /var/lib/jenkins/secrets/initialAdminPassword


Copy the password and enter it on the Jenkins setup page.

7. Install Suggested Plugins

After entering the administrator password:

Click Install suggested plugins.

Wait for Jenkins to install the required plugins.

Continue after the installation completes.

8. Create the First Admin User

Jenkins will ask you to create an administrator user.

You can:

Create a new admin user.

Skip the step.

For a Jenkins instance that will be used for future projects, creating a dedicated admin user is recommended.

9. Jenkins Installation Complete

Once the setup is complete, Jenkins is ready to use.

You can access Jenkins at:

http://<EC2-PUBLIC-IP>:8080

10. Install Docker Pipeline Plugin

The Docker Pipeline plugin allows Jenkins pipelines to work with Docker.

Steps

Log in to Jenkins.

Go to Manage Jenkins.

Select Manage Plugins.

Open the Available tab.

Search for:

Docker Pipeline


Select the plugin.

Click Install.

Restart Jenkins after the installation completes.

11. Install Docker

Connect to the EC2 instance and run:

sudo apt update

Install Docker
sudo apt install docker.io

Verify Docker
docker --version

12. Grant Docker Permissions to Jenkins

The Jenkins user needs permission to communicate with the Docker daemon.

Switch to a Root Shell
sudo su -

Add Jenkins User to Docker Group
usermod -aG docker jenkins

Add Ubuntu User to Docker Group
usermod -aG docker ubuntu

Restart Docker
systemctl restart docker

13. Restart Jenkins

After configuring Docker permissions, restart Jenkins.

Option 1: Restart Using Jenkins URL

Open:

http://<EC2-PUBLIC-IP>:8080/restart

Option 2: Restart Using System Service

Run:

sudo systemctl restart jenkins

14. Docker Agent Configuration

After completing the previous steps:

Docker is installed.

Jenkins has permission to access Docker.

The Docker Pipeline plugin is installed.

Jenkins has been restarted.

The Docker agent configuration is now ready.

Jenkins Setup Checklist

Use this checklist to verify the installation:

 AWS EC2 instance created

 Java 17 installed

 Java version verified

 Jenkins repository configured

 Jenkins installed

 EC2 port 8080 configured

 Jenkins accessible through browser

 Initial administrator password retrieved

 Suggested plugins installed

 First admin user created

 Docker Pipeline plugin installed

 Docker installed

 Jenkins added to Docker group

 Ubuntu user added to Docker group

 Docker service restarted

 Jenkins restarted

 Docker agent configuration verified

Important Security Notes

Avoid opening All Traffic to the EC2 instance unless there is a specific reason to do so.

For Jenkins, the minimum required inbound rule for basic access is generally:

Setting	Value
Protocol	TCP
Port	8080
Source	Your IP address

For production environments, consider additional security controls such as:

Restricting access through a VPN or private network.

Using HTTPS.

Using a reverse proxy.

Restricting Jenkins access to trusted IP addresses.

Applying appropriate AWS Security Group rules.

Keeping Jenkins and its plugins updated.

Using separate credentials and secrets instead of hardcoding them in pipelines.

What's Next?

After completing the Jenkins installation and Docker configuration, the next steps can include:

Creating Jenkins jobs.

Creating Jenkins Pipeline jobs.

Writing Jenkinsfile pipelines.

Integrating Jenkins with Git/GitHub.

Building Docker images from Jenkins.

Pushing images to a container registry.

Deploying applications to Kubernetes.

Creating complete CI/CD pipelines.

Adding automated testing.

Managing credentials and secrets securely.

Jenkins Zero to Hero

The overall learning path can be summarized as:

AWS EC2
   ↓
Install Java
   ↓
Install Jenkins
   ↓
Configure Security Group
   ↓
Access Jenkins
   ↓
Install Plugins
   ↓
Install Docker
   ↓
Configure Docker Permissions
   ↓
Configure Docker Agent
   ↓
Create CI/CD Pipeline
   ↓
Build Application
   ↓
Build Docker Image
   ↓
Push Image
   ↓
Deploy to Kubernetes
   ↓
End-to-End CI/CD

Completion Status

Jenkins installation and Docker agent configuration completed successfully.
