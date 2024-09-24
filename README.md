## Helm Chart Deployment and Management Guide

### Overview
This guide covers how to:
- Create Helm charts for a sample application (e.g., a web server) with configurable values.
- Deploy the application to a Kubernetes cluster using Helm.
- Upgrade the application by modifying Helm chart values.
- Implement a namespace strategy for organizing applications in Kubernetes.
- Rollback the application to a previous version using Helm.

---

### Prerequisites
Before starting, ensure you have:
- A **Kubernetes Cluster** (minikube, GKE, EKS, etc.).
- **kubectl** installed and configured.
- **Helm** installed. You can install Helm using:
   ```bash
   curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
   ```
- A basic **sample application** (e.g., Nginx or your own web server).

---

### 1. Create Helm Charts for a Sample Application

#### Step 1: Create a New Helm Chart
Use the following command to create a new Helm chart (e.g., for `my-web-app`):
```bash
helm create my-web-app
```
This command generates a folder structure with the default Helm files.

#### Step 2: Modify `values.yaml` for Configurable Values
In the `my-web-app/values.yaml` file, specify configurable values like the image and replica count:
```yaml
replicaCount: 2
image:
  repository: nginx
  tag: "1.21"
  pullPolicy: IfNotPresent
service:
  type: ClusterIP
  port: 80
```

#### Step 3: Update `deployment.yaml` to Use Configurable Values
In `my-web-app/templates/deployment.yaml`, use Helm template syntax to reference the values from `values.yaml`:
```yaml
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
        - name: web
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: {{ .Values.service.port }}
```

---

### 2. Deploy the Application Using Helm

#### Step 1: Create a Namespace (Optional)
You can create a namespace for the deployment, such as `development`:
```bash
kubectl create namespace development
```

#### Step 2: Install the Helm Chart
Deploy the application to the Kubernetes cluster using Helm:
```bash
helm install my-web-app ./my-web-app --namespace development
```

#### Step 3: Verify the Deployment
After deploying, check the application status in the `development` namespace:
```bash
kubectl get all -n development
```

---

### 3. Upgrade the Application by Modifying Helm Chart Values

#### Step 1: Edit `values.yaml` to Change Configuration
Update `values.yaml` to change some parameters (e.g., the image tag or replica count):
```yaml
replicaCount: 3
image:
  repository: nginx
  tag: "1.23"
```

#### Step 2: Perform the Helm Upgrade
Run the Helm upgrade command to apply the updated configuration:
```bash
helm upgrade my-web-app ./my-web-app --namespace development
```

#### Step 3: Verify the Upgrade
Ensure the application has been updated successfully:
```bash
kubectl get all -n development
```

---

### 4. Implement Namespace Strategy for Kubernetes Cluster

#### Step 1: Create Separate Namespaces for Different Environments
Create namespaces for different environments like `development`, `staging`, and `production`:
```bash
kubectl create namespace development
kubectl create namespace staging
kubectl create namespace production
```

#### Step 2: Deploy to Different Environments
Use Helm to deploy your application to different namespaces:
```bash
helm install my-web-app ./my-web-app --namespace staging
helm install my-web-app ./my-web-app --namespace production
```

---

### 5. Rollback the Application Using Helm Rollback

#### Step 1: Check Helm Release History
List all Helm releases and their revision history:
```bash
helm list --namespace development
```

#### Step 2: Rollback to a Previous Version
To rollback the application to a previous version (e.g., revision 1):
```bash
helm rollback my-web-app 1 --namespace development
```

#### Step 3: Verify the Rollback
Confirm the rollback was successful by checking the status of the deployment:
```bash
kubectl get all -n development
```

---

### Additional Command

- **View Helm Release History:**
  ```bash
  helm history my-web-app --namespace development
  ```
![image](https://github.com/user-attachments/assets/b41e13c0-4087-4859-9cef-d1382054e061)

