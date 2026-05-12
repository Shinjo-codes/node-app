# 🚀 Production-Grade Node.js DevOps Deployment on Azure

![Architecture](docs/architecture.svg)

## 📌 Project Overview

This project demonstrates how to deploy a production-ready Node.js application on Microsoft Azure using modern DevOps practices and cloud infrastructure components.

The project covers:

* Node.js application deployment
* Azure Virtual Machine provisioning
* Linux server administration
* NGINX reverse proxy configuration
* PM2 process management
* Git & GitHub workflows
* CI/CD automation using GitHub Actions
* Custom domain integration
* HTTPS/SSL configuration using Certbot
* Production deployment troubleshooting

This repository is designed to serve both as:

1. A practical DevOps learning resource for beginner engineers
2. A production-style portfolio project for technical interviews

---

# 🏗️ Architecture Diagram

<img src="docs/architecture.svg" width="800"/>

---

# 🧱 Technology Stack

| Layer             | Technology              |
| ----------------- | ----------------------- |
| Cloud Provider    | Microsoft Azure         |
| Operating System  | Ubuntu 24.04 LTS        |
| Runtime           | Node.js                 |
| Web Framework     | Express.js              |
| Reverse Proxy     | NGINX                   |
| Process Manager   | PM2                     |
| CI/CD             | GitHub Actions          |
| Source Control    | Git & GitHub            |
| SSL               | Certbot + Let's Encrypt |
| Domain Management | DNS Provider            |

---

# 🎯 Learning Objectives

By completing this project, engineers will learn how to:

* Deploy applications to cloud infrastructure
* Configure Linux servers
* Reverse proxy traffic using NGINX
* Automate deployments using CI/CD pipelines
* Manage Node.js processes using PM2
* Configure DNS and custom domains
* Enable HTTPS using SSL certificates
* Troubleshoot deployment and networking issues
* Understand production deployment architecture

---

# 📁 Project Structure

```bash
node-app/
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── docs/
│   ├── architecture.svg
│   ├── flow.svg
│   └── layers.svg
│
├── index.js
├── package.json
├── package-lock.json
├── .gitignore
└── README.md
```

---

# 🌍 Deployment Architecture

## Request Flow

<img src="docs/flow.svg" width="700"/>

---

# 🧠 Architecture Explanation

## 1. Client Request

Users access the application through:

```text
https://www.shinjocloud.com.ng
```

The request is routed through DNS to the Azure Virtual Machine public IP.

---

## 2. NGINX Reverse Proxy

NGINX listens on:

```text
Port 80 (HTTP)
Port 443 (HTTPS)
```

Responsibilities:

* Reverse proxying
* SSL termination
* Traffic forwarding
* Security layer
* Domain handling

---

## 3. Node.js Application

The Express.js application runs internally on:

```text
localhost:3000
```

The application is not directly exposed to the internet.

---

## 4. PM2 Process Manager

PM2:

* Keeps the application alive
* Restarts crashed processes
* Persists application state after VM reboot
* Manages logs and monitoring

---

# ⚙️ PHASE 1 — Create the Node.js Application

## Step 1 — Create Project Folder

```bash
mkdir node-app
cd node-app
```

---

## Step 2 — Initialize Node.js Project

```bash
npm init -y
```

This generates:

```text
package.json
```

---

## Step 3 — Install Express.js

```bash
npm install express
```

---

## Step 4 — Create index.js

```javascript
const express = require('express');

const app = express();

const PORT = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send('🚀Shinjo\'s Third DevOps Capstone App is Running!');
});

app.get('/health', (req, res) => {
  res.json({ status: 'OK' });
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

## Step 5 — Update package.json

Add:

```json
"scripts": {
  "start": "node index.js"
}
```

---

## Step 6 — Test Locally

```bash
npm start
```

Visit:

```text
http://localhost:3000
```

---

# 🔧 PHASE 2 — Initialize Git & Push to GitHub

## Create .gitignore

```gitignore
node_modules/
.env
.DS_Store
npm-debug.log
```

---

## Initialize Git

```bash
git init
git add .
git commit -m "initial commit"
```

---

## Create GitHub Repository

Create a repository on GitHub named:

```text
node-app
```

---

## Push to GitHub

```bash
git remote add origin https://github.com/YOUR-USERNAME/node-app.git

git push origin master
```

---

# ☁️ PHASE 3 — Create Azure Virtual Machine

## Azure VM Configuration

| Setting        | Value            |
| -------------- | ---------------- |
| Resource Group | nodeRG           |
| VM Name        | node-app-vm      |
| OS             | Ubuntu 24.04 LTS |
| Authentication | SSH Key          |
| Username       | azureuser        |
| Open Ports     | 22, 80, 443      |

---

# 🔐 Connect to Azure VM

```bash
ssh -i <private-key-path> azureuser@YOUR_PUBLIC_IP
```

---

# 🖥️ PHASE 4 — Configure Linux Server

## Update Packages

```bash
sudo apt update
sudo apt upgrade -y
```

---

## Install Node.js

### Node.js v20

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
```

Install Node.js:

```bash
sudo apt install -y nodejs
```

---

## Install NGINX

```bash
sudo apt install nginx -y
```

Enable NGINX:

```bash
sudo systemctl enable nginx
```

Start NGINX:

```bash
sudo systemctl start nginx
```

Check status:

```bash
sudo systemctl status nginx
```

---

## Install PM2

```bash
sudo npm install -g pm2
```

---

# 🚀 PHASE 5 — Deploy Application

## Clone Repository

```bash
git clone https://github.com/YOUR-USERNAME/node-app.git

cd node-app
```

---

## Install Dependencies

```bash
npm install
```

---

## Start Application with PM2

```bash
pm2 start index.js --name my-app
```

Save PM2 processes:

```bash
pm2 save
```

Enable PM2 startup:

```bash
pm2 startup
```

---

# 🌐 PHASE 6 — Configure NGINX Reverse Proxy

## Edit NGINX Config

```bash
sudo nano /etc/nginx/sites-available/default
```

Replace contents with:

```nginx
server {
    listen 80;

    location / {
        proxy_pass http://localhost:3000;

        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

---

## Test NGINX Config

```bash
sudo nginx -t
```

Expected:

```text
syntax is ok
syntax is successful
```

---

## Restart NGINX

```bash
sudo systemctl restart nginx
```

---

## Test Public IP

Visit:

```text
http://YOUR_PUBLIC_IP
```

Your Node.js application should now load.

---

# 🔄 PHASE 7 — Configure CI/CD Pipeline

## Create GitHub Secrets

Navigate to:

```text
GitHub Repository → Settings → Secrets and Variables → Actions
```

Add:

| Secret          | Value              |
| --------------- | ------------------ |
| VM_IP           | Azure VM Public IP |
| VM_USER         | azureuser          |
| SSH_PRIVATE_KEY | Private SSH Key    |

---

# ⚡ Create GitHub Actions Workflow

Create:

```text
.github/workflows/deploy.yml
```

---

## deploy.yml

```yaml
name: Deploy to Azure VM

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy application to Azure VM
        uses: appleboy/ssh-action@v0.1.6

        with:
          host: ${{ secrets.VM_IP }}
          username: ${{ secrets.VM_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            cd /home/azureuser/node-app

            echo "Pulling latest code..."
            git pull origin master

            echo "Installing dependencies..."
            npm install

            echo "Restarting application..."
            pm2 restart my-app || pm2 start index.js --name my-app

            echo "Saving PM2 process list..."
            pm2 save
```

---

# 🧪 Test CI/CD Pipeline

Make changes to:

```text
index.js
```

Then push changes:

```bash
git add .
git commit -m "Updated app"
git push origin master
```

---

## Verify Deployment

Open:

```text
GitHub Repository → Actions
```

Confirm the workflow completes successfully.

---

# 🌍 PHASE 8 — Connect Custom Domain

## Add DNS Records

At your domain registrar:

### Root Domain

| Type | Name | Value      |
| ---- | ---- | ---------- |
| A    | @    | YOUR_VM_IP |

---

### WWW Domain

| Type | Name | Value      |
| ---- | ---- | ---------- |
| A    | www  | YOUR_VM_IP |

---

## Wait for DNS Propagation

Propagation may take:

* 5 minutes
* several hours
* up to 48 hours

---

# 🌐 PHASE 9 — Configure Domain in NGINX

Edit config:

```bash
sudo nano /etc/nginx/sites-available/default
```

---

## Updated NGINX Config

```nginx
server {
    listen 80;

    server_name shinjocloud.com.ng www.shinjocloud.com.ng;

    location / {
        proxy_pass http://localhost:3000;

        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

---

## Restart NGINX

```bash
sudo nginx -t
sudo systemctl restart nginx
```

---

## Test Domain

Visit:

```text
http://www.shinjocloud.com.ng
```

---

# 🔒 PHASE 10 — Enable HTTPS with SSL

## Install Certbot

```bash
sudo apt install certbot python3-certbot-nginx -y
```

---

## Configure SSL

```bash
sudo certbot --nginx -d shinjocloud.com.ng -d www.shinjocloud.com.ng
```

Follow the prompts.

---

## Verify HTTPS

Visit:

```text
https://www.shinjocloud.com.ng
```

The site should now:

* use HTTPS
* display a secure lock icon
* redirect HTTP to HTTPS automatically

---

# 📊 Layered Architecture

<img src="docs/layers.svg" width="800"/>

---

# 🔍 Monitoring & Verification Commands

## Check PM2 Status

```bash
pm2 list
```

---

## Check NGINX Status

```bash
sudo systemctl status nginx
```

---

## Check Application Logs

```bash
pm2 logs
```

---

## Check NGINX Config

```bash
sudo nginx -t
```

---

## Check Listening Ports

```bash
sudo lsof -i -P -n
```

---

# 🚨 Common Errors & Troubleshooting

## ❌ 1. NGINX Welcome Page Appears

### Cause

NGINX default site is still active.

### Fix

Update:

```bash
/etc/nginx/sites-available/default
```

Configure reverse proxy properly.

---

# ❌ 2. 502 Bad Gateway

### Cause

NGINX cannot communicate with Node.js app.

### Fix

Verify PM2:

```bash
pm2 list
```

Restart app:

```bash
pm2 restart my-app
```

---

# ❌ 3. ERR_CONNECTION_REFUSED

### Cause

* Port 80 blocked
* DNS issue
* NGINX stopped

### Fix

Verify:

```bash
sudo systemctl status nginx
```

Check Azure NSG rules.

---

# ❌ 4. SSL Certbot NXDOMAIN Error

### Cause

Invalid DNS records.

### Fix

Ensure domain resolves to:

```text
YOUR_VM_IP
```

---

# ❌ 5. Git Push Rejected (non-fast-forward)

### Fix

```bash
git pull origin master --rebase
git push origin master
```

---

# 🧠 DevOps Concepts Demonstrated

This project demonstrates practical understanding of:

* Infrastructure provisioning
* Linux server administration
* Reverse proxy architecture
* Cloud networking
* DNS management
* SSL/TLS encryption
* CI/CD automation
* Git workflows
* Process management
* Production deployment practices

---

# 🚀 Future Improvements

Potential enhancements include:

* Docker containerization
* Kubernetes deployment
* Terraform Infrastructure as Code
* Azure Load Balancer
* Azure Front Door
* Azure Key Vault
* Observability stack
* Monitoring dashboards
* Auto-scaling
* Blue-Green deployments

---

# 📚 Key DevOps Lessons Learned

During this project, several real-world deployment issues were encountered and resolved, including:

* NGINX reverse proxy misconfiguration
* DNS propagation delays
* CI/CD pipeline debugging
* Git authentication issues
* PM2 process persistence
* SSL certificate configuration
* Azure networking and firewall rules

These troubleshooting experiences significantly improved understanding of production deployment workflows.

---

# 👨‍💻 Author

## Shina Ojo

DevOps & Cloud Engineering Enthusiast

Passionate about:

* Cloud infrastructure
* Automation
* CI/CD systems
* Linux administration
* Azure architecture
* Production-grade deployments

---

# ⭐ Acknowledgements

This project was inspired by hands-on DevOps learning exercises and expanded into a production-style deployment implementation focused on practical engineering experience.

---

# 📄 License

This project is open-source and available for educational and portfolio purposes.
