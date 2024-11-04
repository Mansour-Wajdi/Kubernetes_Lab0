# tpKubernetes


curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

minikube version

sudo usermod -aG docker $USER

newgrp docker

minikube start


curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl

sudo mv kubectl /usr/local/bin/

kubectl version --client

kubectl cluster-info

nano deployment.yaml


"""
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
"""

kubectl apply -f deployment.yaml


kubectl get deployments
kubectl get pods



3//

kubectl scale deployment nginx-deployment --replicas=3

kubectl get deployments
kubectl get pods


4///

sudo nano service.yaml

"""
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
"""


kubectl apply -f service.yaml
kubectl get services


////Vérifiez que le service est accessible à partir du cluster Kubernetes //////

kubectl run curlpod --image=curlimages/curl:latest --restart=Never -i --tty -- /bin/sh

    curl http://nginx-service


exit

kubectl delete pod curlpod



//////////// 5 


