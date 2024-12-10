# Deployment Steps

## Define Variables for Azure VM Creation
- **Resource group:** Artiom_RG  
- **VM name:** MyVmtop  
- **Location:** westeurope  
- **OS type:** Ubuntu2204  
- **Size:** Standard_D4s_v3

## Create the VM Using Azure CLI
```bash
az vm create \
  --name $vmName --resource-group $resourceGroup \
  --admin-username <username> --admin-password <password> \
  --image $osType --location $location --size $size \
  --security-type TrustedLaunch --ultra-ssd-enabled false
```

## Open Port 8080
Configure the Network Security Group (NSG) of the VM to allow traffic on port 8080.

---

# Post-deployment Setup

## SSH into the VM and Install Java
```bash
sudo apt update
sudo apt install openjdk-17-jre
```

## Verify Java Installation
```bash
java -version
```

## Install Jenkins
```bash
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

## Access Jenkins
Visit Jenkins at:  
`http://<ec2-instance-public-ip>:8080`

Log in using the initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

# Jenkins Configuration

## Install Docker Pipeline Plugin
1. Navigate to **Manage Jenkins > Manage Plugins**.
2. Search for **Docker Pipeline**.
3. Select and install the plugin.
4. Restart Jenkins.

## Configure Docker
```bash
sudo apt update
sudo apt install docker.io
sudo systemctl restart docker
```

### Add Jenkins User to Docker Group
```bash
sudo usermod -aG docker jenkins
```

---

# Create and Deploy Application

## Create Files on the VM
Navigate to your project directory and create the necessary files:

### Create `Main.java`
```bash
cat <<EOF > Main.java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello, CI/CD with Docker and Jenkins!");
    }
}
EOF
```

### Create `Dockerfile`
```bash
cat <<EOF > Dockerfile
FROM openjdk:11-jdk-slim
WORKDIR /app
COPY . .
RUN javac Main.java
CMD ["java", "Main"]
EOF
```

## Initialize and Push to Git Repository
1. Initialize a Git repository:
   ```bash
   git init
   ```
2. Add a remote repository:
   ```bash
   git remote add origin https://github.com/Avenosh1/myproject
   ```
3. Set the branch to `main`:
   ```bash
   git branch -M main
   ```
4. Stage and commit changes:
   ```bash
   git add .
   git commit -m "Initial commit"
   ```
5. Push to the remote repository:
   ```bash
   git push -u origin main
   ```

## Create `Jenkinsfile`
Create a pipeline definition for Jenkins:
```bash
cat <<EOF > Jenkinsfile
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Avenosh1/myproject.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t java-app .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh 'docker run java-app'
                }
            }
        }
    }
}
EOF
```

---

# Git Configuration

## Generate a Personal Access Token (PAT) on GitHub

## Update Git Remote URL with PAT
```bash
git remote set-url origin https://<your_username>:<your_personal_access_token>@github.com/Avenosh1/myproject.git
```

## Push Changes to GitHub Repository
```bash
git push origin main
```

---

This setup provides a foundation for:
- Deploying an **Ubuntu Server 2022** on Azure.
- Installing **Jenkins**.
- Setting up **Docker**.
- Configuring a **CI/CD pipeline** for building Java applications in Docker containers.
