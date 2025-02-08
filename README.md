
# 🚀 DevOps Kubernetes Deployment with CI/CD

This guide walks you through deploying a web app using **Kubernetes**, integrating **GitHub Actions for CI/CD**, and ensuring that the deployment process is automated.
---

## **1️⃣ Prerequisites**
Before starting, make sure you have the following:
- A **Linux machine (or VM)** with:
  - At least **4GB of RAM**
  - **Docker** installed
  - **Kubernetes (`k3s` or `k8s`)**
  - `kubectl` installed
- A **GitHub repository** to store your project
- A **Docker Hub account** to store the containerized app

**Example of Kubernetes Node Status:**  
![image](https://github.com/user-attachments/assets/8bdc965f-a7ec-4396-a054-1703e95b7878)

---

## **2️⃣ Step 1: Install Dependencies**
### **🔹 Install Docker**
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io
sudo systemctl enable --now docker
```
**Screenshot of Docker Installation:**  
![image (1)](https://github.com/user-attachments/assets/954fc7a6-c1f6-4079-aa8e-a8bd0b55dc57)


### **🔹 Install Kubernetes (`k3s`)**
```bash
curl -sfL https://get.k3s.io | sh -
```
Verify Kubernetes installation:
```bash
kubectl get nodes
```
**Example Output:**  
![image (2)](https://github.com/user-attachments/assets/60f26ca8-5e5b-4fc6-afcd-0a9040f16c57)


---

## **3️⃣ Step 2: Prepare the Kubernetes Deployment**
1. **Create the Kubernetes Deployment and Service configuration**
   ```bash
   sudo nano k8s/deployment.yaml
   ```
   Paste the following configuration:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: web-app
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: web-app
     template:
       metadata:
         labels:
           app: web-app
       spec:
         containers:
         - name: web-app
           image: your-dockerhub-username/web-app:latest
           ports:
           - containerPort: 8080

   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: web-app-service
   spec:
     type: NodePort
     ports:
       - port: 80
         targetPort: 8080
         nodePort: 30080
     selector:
       app: web-app
   ```

2. **Apply the configuration to Kubernetes**
   ```bash
   kubectl apply -f k8s/deployment.yaml
   ```

3. **Verify that the deployment is running**
   ```bash
   kubectl get pods
   kubectl get svc
   ```
**Example Output:**  
![image](https://github.com/user-attachments/assets/ef050350-703c-4102-af5b-67b8922e1a8a)


---

## **4️⃣ Step 3: Containerize the Web App**
1. **Create a Dockerfile for the app**
   ```bash
   sudo nano Dockerfile
   ```
   Paste the following:
   ```dockerfile
   FROM node:16
   WORKDIR /app
   COPY . .
   RUN npm install
   CMD ["npm", "start"]
   EXPOSE 8080
   ```

2. **Build the Docker image**
   ```bash
   docker build -t your-dockerhub-username/web-app:latest .
   ```

3. **Log in to Docker Hub**
   ```bash
   docker login
   ```

4. **Push the image to Docker Hub**
   ```bash
   docker push your-dockerhub-username/web-app:latest
   ```
**Example of Docker Image Push:**  
![image](https://github.com/user-attachments/assets/aaa61d0c-ddf4-4f5b-998e-d7014d90ad82)


---

## **5️⃣ Step 4: Automate Deployment with GitHub Actions**
1. **Create a GitHub Actions workflow file**
   ```bash
   mkdir -p .github/workflows
   sudo nano .github/workflows/ci-cd.yml
   ```

2. **Paste the following configuration:**
   ```yaml
   name: CI/CD Pipeline

   on:
     push:
       branches:
         - main

   jobs:
     build-and-deploy:
       runs-on: ubuntu-latest

       steps:
       - name: Checkout Code
         uses: actions/checkout@v3

       - name: Log in to Docker Hub
         run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

       - name: Build and Push Docker Image
         run: |
           docker build -t your-dockerhub-username/web-app:latest .
           docker push your-dockerhub-username/web-app:latest

       - name: Deploy to Kubernetes
         env:
           KUBECONFIG: ${{ secrets.KUBECONFIG }}
         run: |
           echo "${{ secrets.KUBECONFIG }}" > kubeconfig
           export KUBECONFIG=kubeconfig
           kubectl apply -f k8s/deployment.yaml
   ```

3. **Add GitHub Secrets:**
   - **DOCKER_USERNAME** → Your Docker Hub username
   - **DOCKER_PASSWORD** → Your Docker Hub password
   - **KUBECONFIG** → Run this on your Kubernetes machine and paste the result:
     ```bash
     cat ~/.kube/config | base64
     ```
     Copy the output and save it as a secret in GitHub.

**Example of GitHub Actions Workflow Execution:**  
![workflow](https://github.com/user-attachments/assets/e40f4680-471a-4b33-9b9d-343a05106697)

---

## **6️⃣ Step 5: Test the Setup**
1. **Push code to GitHub**:
   ```bash
   git add .
   git commit -m "Initial Kubernetes deployment"
   git push origin main
   ```

2. **Watch GitHub Actions for CI/CD execution**
   - Go to `GitHub → Actions` and monitor the workflow.

3. **Check if the app is running in Kubernetes**
   ```bash
   kubectl get pods
   kubectl get svc
   ```

4. **Access the app via the NodePort**
   ```bash
   curl http://<your-server-ip>:30080
   ```

**Example of App Running in Kubernetes:**  
![image](https://github.com/user-attachments/assets/6a329050-57bb-47b0-b415-7f2951c16258)


---

## **✅ Project Complete!**
Your web app is now automatically deployed to **Kubernetes** whenever you push changes to GitHub this could obviously be applied for a host of other projects, however some configuration changes will have to be made for your deployment file. 🎉

**Next Steps:**
- Implement monitoring (Prometheus & Grafana).
- Use **Terraform** to automate infrastructure provisioning.
---
