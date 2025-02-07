# 🚀 DevOps QR Code Project – Self-Hosted K3s & CI/CD

This project showcases a **fully automated DevOps pipeline** where a simple **QR Code web app** is deployed inside a **self-hosted Kubernetes (K3s) cluster** running on a **Debian VM (hosted on Proxmox)**. The deployment process is **completely automated** via **GitHub Actions running directly inside the VM**, eliminating the need for external SSH access.

---

## **📌 Project Overview**
✅ **Self-Hosted Kubernetes (K3s)** – Runs inside a Proxmox-managed Debian VM  
✅ **GitHub Actions (Self-Hosted Runner)** – Automates deployment **inside the VM**  
✅ **Dockerized Web App** – A simple Nginx-based **QR Code generator**  
✅ **CI/CD Pipeline** – Code updates trigger **automatic deployments**  
✅ **Port Forwarding for Local Access** – No need for cloud LoadBalancers  

📌 **Tech Stack Used:**
- 🐳 Docker
- ☸️ Kubernetes (K3s)
- 🔄 GitHub Actions (Self-Hosted Runner)
- 🖥️ Proxmox Virtualization
- 🕵️ Nginx Web Server  

---

📌 Project Architecture
(Insert a diagram showing the Proxmox VM, GitHub Actions runner, Docker container, and K3s deployment)  

---

## **🛠️ Setup Guide – Deploying the Project**

### **1️⃣ Step 1: Setting Up the Proxmox Debian VM**
1. **Install Debian on a Proxmox VM**.
2. **Assign a Static IP (e.g., ``)**.
3. **Enable SSH & Install Dependencies**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo apt install curl git nano docker.io -y
