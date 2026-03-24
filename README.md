# 🚀 CI/CD Pipeline with Jenkins, Ansible & NGINX (AWS EC2)

## 📌 Project Overview

This project demonstrates a **complete CI/CD pipeline** using **Jenkins**, **Ansible**, and **AWS EC2** to automate the deployment of a static eCommerce website on an NGINX web server.

The pipeline ensures that whenever changes are pushed to the Git repository, the application is automatically deployed to a remote server.

---

## 🏗️ Architecture

```
Developer → GitHub → Jenkins (Master EC2) → Ansible → Worker EC2 → NGINX → Website
```

---

## 🛠️ Tech Stack

* **CI/CD Tool:** Jenkins
* **Configuration Management:** Ansible
* **Web Server:** NGINX
* **Version Control:** Git & GitHub
* **Cloud Platform:** AWS EC2
* **OS:** Linux (Amazon Linux / Ubuntu)

---

## 📁 Project Structure

```
project/
├── Jenkinsfile
├── ansible/
│   ├── inventory
│   ├── playbook.yml
│   └── roles/
│       └── nginx/
│           ├── tasks/
│           │   └── main.yml
│           ├── files/
│           │   └── index.html
│           └── handlers/
│               └── main.yml
```

---

## ⚙️ Setup & Installation

### 1️⃣ Launch EC2 Instances

* **Master EC2:** Jenkins + Ansible
* **Worker EC2:** NGINX server

Ensure security groups allow:

* Port **22 (SSH)**
* Port **80 (HTTP)**
* Port **8080 (Jenkins UI)**

---

### 2️⃣ Install Required Tools (Master EC2)

```bash
# Install Java
sudo yum install java-17-amazon-corretto -y

# Install Jenkins
sudo wget -O /etc/yum.repos.d/jenkins.repo \
https://pkg.jenkins.io/redhat-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum install jenkins -y

# Start Jenkins
sudo systemctl enable jenkins
sudo systemctl start jenkins

# Install Git & Ansible
sudo yum install git -y
sudo amazon-linux-extras enable ansible2
sudo yum install ansible -y
```

---

### 3️⃣ Configure SSH Access

```bash
ssh-keygen -t rsa
```

Copy public key to worker:

```bash
ssh-copy-id ec2-user@<WORKER-IP>
```

Or use `.pem` key in inventory file.

---

### 4️⃣ Configure Ansible Inventory

```ini
[web]
<WORKER-IP> ansible_user=ec2-user ansible_ssh_private_key_file=/path/to/key.pem
```

---

### 5️⃣ Run Ansible Manually (Test)

```bash
ansible all -i inventory -m ping
ansible-playbook -i inventory playbook.yml
```

---

## 🔄 Jenkins Pipeline

### Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        ANSIBLE_HOST_KEY_CHECKING = "False"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/<your-username>/<repo>.git'
            }
        }

        stage('Deploy via Ansible') {
            steps {
                sh '''
                cd ansible
                ansible-playbook -i inventory playbook.yml
                '''
            }
        }
    }
}
```

---

## 🚀 CI/CD Workflow

1. Developer pushes code to GitHub
2. Jenkins pulls latest code
3. Jenkins executes Ansible playbook
4. Ansible connects to Worker EC2 via SSH
5. NGINX is configured and website is deployed

---

## 🌐 Access Application

```
http://<PUBLIC-IP>
```

---

## ⚠️ Troubleshooting

### 🔹 Permission Denied (SSH)

* Check key permissions:

```bash
chmod 400 key.pem
```

### 🔹 Ansible UNREACHABLE

* Verify security group (port 22 open)

### 🔹 NGINX Default Page

* Ensure `index.html` is inside:

```
roles/nginx/files/
```

---

## 💡 Key Learnings

* Implemented **end-to-end CI/CD pipeline**
* Automated deployment using **Ansible roles**
* Managed secure communication using **SSH keys**
* Debugged real-world issues (SSH, Git, Jenkins pipeline)

---

## 🔥 Future Enhancements

* GitHub Webhooks (auto trigger pipeline)
* Multi-server deployment
* Load Balancer integration
* Docker & Kubernetes deployment

---

## 👨‍💻 Author

**Soumya Ranjan Nayak**

---

## ⭐ If you like this project

Give it a ⭐ on GitHub and feel free to fork it!
