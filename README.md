# Jenkins
Jenkins Zero to Hero — AWS EC2, Docker, and Jenkins Setup
This guide explains how to install and configure Jenkins on an AWS EC2 instance, install Docker, configure Jenkins to communicate with Docker, and prepare the environment for CI/CD pipelines.

1. Create an AWS EC2 Instance
Go to the AWS Management Console.

Navigate to EC2.

Select Instances.

Click Launch instances.

Configure and launch the EC2 instance.

Note: Make sure the EC2 instance has sufficient CPU, memory, and storage resources to run Jenkins and Docker.

2. Jenkins Installation Prerequisites
Before installing Jenkins, Java must be installed.

Install Java 17
Run:

sudo apt update
sudo apt install -y openjdk-17-jre

Verify Java Installation
Run:

java -version

You should see information about the installed Java version.

3. Install Jenkins
Add the Jenkins Repository Key
Run:

curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

Add the Jenkins Repository
Run:

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

Update the Package List
sudo apt-get update

Install Jenkins
sudo apt-get install -y jenkins

Check Jenkins Service Status
sudo systemctl status jenkins

If Jenkins is not running, start it with:

sudo systemctl start jenkins

Enable Jenkins to start automatically after a system reboot:

sudo systemctl enable jenkins

4. Configure the EC2 Security Group
By default, Jenkins runs on port 8080.

AWS EC2 inbound traffic restrictions may prevent Jenkins from being accessed externally.

Open Port 8080
Navigate to:

EC2 → Instances → Select your instance → Security → Security Groups

Edit the inbound rules and add:

Setting	Value
Type	Custom TCP
Protocol	TCP
Port	8080
Source	Your IP address

Security Recommendation: Restrict the source to your IP address where possible instead of allowing traffic from everywhere (0.0.0.0/0).

5. Access Jenkins
Once Jenkins is installed and port 8080 is accessible, open the following URL in your browser:

http://<EC2-PUBLIC-IP>:8080

Replace <EC2-PUBLIC-IP> with the public IP address or DNS name of your EC2 instance.

Example:

http://54.123.45.67:8080

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

Access Jenkins at:

http://<EC2-PUBLIC-IP>:8080

10. Install Docker Pipeline Plugin
The Docker Pipeline plugin allows Jenkins Pipeline jobs to work with Docker.

Steps
Log in to Jenkins.

Go to Manage Jenkins.

Select Plugins.

Open the Available plugins tab.

Search for:

Docker Pipeline

Select the plugin.

Click Install.

Restart Jenkins if required.

11. Install Docker
Connect to the EC2 instance and update the package list:

sudo apt update

Install Docker:

sudo apt install -y docker.io

Verify Docker Installation
Run:

docker --version

You should see the installed Docker version.

Check Docker Service
sudo systemctl status docker

If Docker is not running:

sudo systemctl start docker

Enable Docker to start automatically after a reboot:

sudo systemctl enable docker

12. Grant Docker Permissions to Jenkins
The Jenkins user needs permission to communicate with the Docker daemon.

Add Jenkins User to the Docker Group
Run:

sudo usermod -aG docker jenkins

Add Ubuntu User to the Docker Group
Run:

sudo usermod -aG docker ubuntu

Restart Docker
sudo systemctl restart docker

Important: Group membership changes may require the user session or service to be restarted before they take effect.

13. Restart Jenkins
After configuring Docker permissions, restart Jenkins.

Option 1: Restart Using Jenkins URL
Open:

http://<EC2-PUBLIC-IP>:8080/restart

Confirm the restart when prompted.

Option 2: Restart Using the System Service
Run:

sudo systemctl restart jenkins

Verify Jenkins:

sudo systemctl status jenkins

14. Verify Jenkins Can Access Docker
Before configuring Docker-based Jenkins pipelines, verify that the Jenkins user can communicate with Docker.

Run:

sudo -u jenkins docker --version

You can also test Docker access with:

sudo -u jenkins docker ps

If these commands work without a permission error, Jenkins has access to Docker.

Note: Adding Jenkins to the docker group grants Jenkins access to the Docker daemon. Docker daemon access effectively provides high-level control over the host system, so this configuration should be treated as a privileged access decision.

15. Docker Agent Configuration
After completing the previous steps:

Docker is installed.

Jenkins has permission to access Docker.

The Docker Pipeline plugin is installed.

Jenkins has been restarted.

Jenkins can communicate with Docker.

The environment is now ready for Docker-based Jenkins pipelines.

Jenkins Setup Checklist
Use this checklist to verify the installation:

 AWS EC2 instance created

 Java 17 installed

 Java version verified

 Jenkins repository configured

 Jenkins installed

 Jenkins service enabled

 EC2 port 8080 configured

 Jenkins accessible through browser

 Initial administrator password retrieved

 Suggested plugins installed

 First admin user created

 Docker Pipeline plugin installed

 Docker installed

 Docker service enabled

 Jenkins added to Docker group

 Ubuntu user added to Docker group

 Docker service restarted

 Jenkins restarted

 Jenkins Docker access verified

 Docker agent configuration verified

Important Security Notes
Avoid opening All Traffic to the EC2 instance unless there is a specific reason to do so.

For basic Jenkins access, the inbound security group rule can be restricted to your IP address:

Setting	Value
Type	Custom TCP
Protocol	TCP
Port	8080
Source	Your IP address

For production environments, consider additional security controls such as:

Restricting Jenkins access through a VPN or private network.

Using HTTPS.

Using a reverse proxy.

Restricting Jenkins access to trusted IP addresses.

Applying appropriate AWS Security Group rules.

Keeping Jenkins and its plugins updated.

Using separate credentials and secrets instead of hardcoding them in pipelines.

Following the principle of least privilege.

Backing up Jenkins configuration and important build data.

Monitoring Jenkins and the underlying EC2 instance.

What's Next?
After completing the Jenkins installation and Docker configuration, the next steps can include:

Creating Jenkins jobs.

Creating Jenkins Pipeline jobs.

Writing Jenkinsfile pipelines.

Integrating Jenkins with Git/GitHub.

Building Docker images from Jenkins.

Pushing Docker images to a container registry.

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
Verify Jenkins Docker Access
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
