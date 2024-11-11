
# Kubernetes_Lab0

## Overview
This document describes the steps taken to complete the Kubernetes lab exercises, covering installation, configuration, deployment, scaling, service setup, and testing for a basic Kubernetes environment with Minikube.

## 1. Setting Up Minikube and Docker

Download and install Minikube:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
```

![Minikube Version](https://github.com/user-attachments/assets/cfadee60-2a80-467d-a742-887ce0e8529e)

Set up Docker permissions:

```bash
sudo usermod -aG docker $USER
newgrp docker
docker info
```

![Docker Info](https://github.com/user-attachments/assets/62fa8192-90db-440f-90fa-cfe33f35e918)

Start Minikube:

```bash
minikube start
```

![Minikube Start](https://github.com/user-attachments/assets/c331fbb3-d855-47f0-8656-5a24d65e7d5a)

Download and set up `kubectl`:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
kubectl cluster-info
```

![Kubectl Info](https://github.com/user-attachments/assets/6264a97e-a110-4375-b316-a276a5653d74)

## 2. Deploying an Application

Create the `deployment.yaml` file for an Nginx deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

Apply the deployment configuration and verify the deployment:

```bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods
```

![Deployment Status](https://github.com/user-attachments/assets/9678695b-5d5e-41cf-a101-01542c8f2877)

## 3. Scaling the Application

Scale the Nginx deployment to multiple replicas:

```bash
kubectl scale deployment nginx-deployment --replicas=3
kubectl get deployments
kubectl get pods
```

![Scaling Status](https://github.com/user-attachments/assets/7a55256c-cfc1-4fe2-9714-01118fae24c8)

## 4. Creating a Kubernetes Service

Create the `service.yaml` file to expose the Nginx application internally:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

Apply the service configuration and check the service status:

```bash
kubectl apply -f service.yaml
kubectl get services
```

![image](https://github.com/user-attachments/assets/7cfba163-306d-48a6-8934-d043169c1720)

### Verifying Service Accessibility

Launch a temporary `curl` pod to confirm the service is accessible within the cluster:

```bash
kubectl run curlpod --image=curlimages/curl:latest --restart=Never -i --tty -- /bin/sh
curl http://nginx-service
exit
kubectl delete pod curlpod
```

![Service Accessibility](https://github.com/user-attachments/assets/d6e837ff-128c-4cff-81ac-8bae233cdea3)

## 5. Additional Configuration: Docker and Minikube

Modify the Service Type to LoadBalancer

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

Apply this updated configuration:

```bash
kubectl apply -f service.yaml
```  

Run Minikube in tunnel mode (useful for external access testing):

```bash
sudo -E minikube tunnel
```

Check the Minikube profile and services:

```bash
minikube profile list
kubectl get services
```

Verify Accessibility:

```bash
curl http://<EXTERNAL-IP>
```

![image](https://github.com/user-attachments/assets/0e3ed862-ee6d-4763-a7ab-b48ff86cc161)


## 6. Updating the Deployment

Update `deployment.yaml` to use a different image version:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

Apply the update:

```bash
kubectl apply -f deployment.yaml
```

Monitor the pod update:

```bash
kubectl get pods -w
```

Verify the updated image version:

```bash
kubectl describe pods | grep "Image"
```

![Updated Image](https://github.com/user-attachments/assets/e449e4a7-ee28-499c-a142-b47b48bc6d65)

## 7. Monitoring with Prometheus

Prometheus is a popular monitoring tool for Kubernetes. Here’s how to install and integrate it into the cluster.

Deploy Prometheus using Helm:

If Helm is not installed, install it first:

```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

Add the Prometheus Helm repository:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

Install Prometheus:

```bash
helm install prometheus prometheus-community/prometheus
```

Verify that Prometheus is deployed:

```bash
kubectl get pods
```

![Prometheus Status](https://github.com/user-attachments/assets/d9c73f21-6ef8-4160-8ed0-e6634a7df28b)

Access the Prometheus UI:

```bash
kubectl port-forward deploy/prometheus-server 9090
```

![image](https://github.com/user-attachments/assets/d22f7a05-08ae-4b6b-b827-114d0d22ce13)

Navigate to [http://localhost:9090](http://localhost:9090) to see the Prometheus interface.

![Prometheus UI](https://github.com/user-attachments/assets/d2eedf7e-cd52-4d10-afec-1b4456e35c8d)

In "Status" -> "Targets"

![Capture d’écran (1952)](https://github.com/user-attachments/assets/0b6a4f5f-9b5a-42db-997a-ea102a8dcdb3)

## 8. Logging with Fluentd

Fluentd collects and manages logs from Kubernetes pods.

Install Fluentd using Helm:

```bash
helm repo add fluent https://fluent.github.io/helm-charts
helm repo update
```

Install Fluentd in the cluster:

```bash
helm install fluentd fluent/fluentd
```

Verify application logs:

```bash
kubectl logs -l app=nginx
```

![Fluentd Logs](https://github.com/user-attachments/assets/c9dcdc3e-bc21-4aa6-8dc3-ebe134b4f552)
