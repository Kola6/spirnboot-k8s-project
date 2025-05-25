📦 Prerequisites
AWS EC2 (Amazon Linux 2, t2.medium)
Docker
Minikube
kubectl
Java 17 & Maven
Git & Conntrack

🛠️ Setup Guide

1️⃣ Launch EC2 and Install Essentials
yum update -y
amazon-linux-extras install docker
yum install conntrack git maven -y

systemctl start docker
systemctl enable docker

2️⃣ Install Minikube & kubectl
# Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
3️⃣ Start Minikube

minikube start --force --driver=docker
4️⃣ Clone the Project Repo

cd /opt
git clone https://github.com/DEVOPS-WITH-WEB-DEV/springboot-k8s.git
cd springboot-k8s
5️⃣ Deploy MySQL on Kubernetes

kubectl create -f db-deployment.yaml
kubectl get pods
🖼️ MySQL Pod Setup

6️⃣ Access MySQL Pod and Verify DB

kubectl exec -it <mysql-pod-name> -- /bin/bash
mysql -u root -p

show databases;
use singamlabs;
show tables;
select * from orders_tbl;
🖼️ MySQL Query Output
mysql> show tables;
Empty set (0.00 sec)

7️⃣ Build & Push Docker Image

docker build -t <your-dockerhub-username>/springboot-crud-k8s:1.0 .
docker login
docker push <your-dockerhub-username>/springboot-crud-k8s:1.0

8️⃣ Deploy Spring Boot App

kubectl apply -f app-deployment.yaml
kubectl get pods
kubectl get svc

9️⃣ Port Forward to Access the App

kubectl port-forward --address 0.0.0.0 svc/springboot-crud-svc 8080:8080

🔟 Send API Request via Postman
POST URL:


http://<EC2-IP>:8080/orders
Body (JSON):
{
  "name": "shoes",
  "qty": 5,
  "price": 6999
}
🖼️ Postman POST Screenshot

![image](https://github.com/user-attachments/assets/601febd6-f765-4ea0-ab7b-6c3f12e938be)

1️⃣1️⃣ Verify Entry in MySQL
Query:

select * from orders_tbl;
+----+-------+-------+-----+
| id | name  | price | qty |
+----+-------+-------+-----+
|  1 | shoes |  6999 |   5 |
+----+-------+-------+-----+
1 row in set (0.00 sec)

🖼️ Result:

1️⃣2️⃣ Kubernetes Dashboard

# Terminal 1
kubectl proxy --address='0.0.0.0' --accept-hosts='^*$'

# Terminal 2
minikube dashboard
Access in browser:


[http://<EC2-IP>:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/](http://3.72.251.198:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/#/pod?namespace=default)
🖼️ Kubernetes Dashboard View


