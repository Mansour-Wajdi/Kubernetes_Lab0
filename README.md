# tpKubernetes

1///
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

minikube version


![image](https://github.com/user-attachments/assets/cfadee60-2a80-467d-a742-887ce0e8529e)


sudo usermod -aG docker $USER

newgrp docker

docker info

![image](https://github.com/user-attachments/assets/62fa8192-90db-440f-90fa-cfe33f35e918)

minikube start

![image](https://github.com/user-attachments/assets/c331fbb3-d855-47f0-8656-5a24d65e7d5a)



curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl

sudo mv kubectl /usr/local/bin/

kubectl version --client

kubectl cluster-info

![image](https://github.com/user-attachments/assets/6264a97e-a110-4375-b316-a276a5653d74)


2//////

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

![image](https://github.com/user-attachments/assets/9678695b-5d5e-41cf-a101-01542c8f2877)


3//

kubectl scale deployment nginx-deployment --replicas=3

kubectl get deployments
kubectl get pods

![image](https://github.com/user-attachments/assets/7a55256c-cfc1-4fe2-9714-01118fae24c8)

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

![image](https://github.com/user-attachments/assets/560e0285-764b-4a60-a12d-20e8ff3ddb19)

////Vérifiez que le service est accessible à partir du cluster Kubernetes //////

kubectl run curlpod --image=curlimages/curl:latest --restart=Never -i --tty -- /bin/sh

    curl http://nginx-service


exit

kubectl delete pod curlpod


![image](https://github.com/user-attachments/assets/d6e837ff-128c-4cff-81ac-8bae233cdea3)

//////////// 5 
Ajouter votre utilisateur au groupe docker :

Exécutez la commande suivante pour ajouter votre utilisateur au groupe docker :


sudo usermod -aG docker $USER

Redémarrer la session :

Pour que les modifications prennent effet, vous devez redémarrer votre session. Vous pouvez soit fermer la session actuelle et vous reconnecter, soit redémarrer la machine :


newgrp docker


Redémarrer Minikube :

Maintenant, essayez de démarrer Minikube :


minikube start
Puis vérifiez son statut :


minikube status





sudo -E minikube tunnel




minikube profile list



kubectl get services
![image](https://github.com/user-attachments/assets/db75e523-b95b-4d9d-aa7e-07af2ee1d778)



