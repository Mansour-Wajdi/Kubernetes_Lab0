
# tpKubernetes

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
