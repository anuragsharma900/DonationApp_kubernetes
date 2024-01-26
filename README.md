# DonationApp_kubernetes as per AWS EKS Cluster Machine
### Note- i have stored images in AWS ECR repository and pulling it from there Or can get the images from Docker Hub
Prerequisite to run eks:
1. install docker
2. install kubectl
3. aws configure
4. Download and extract the latest release of eksctl with the following command.
   
## 1. To install Docker

Install Docker Engine
Install Docker Engine, containerd, and Docker Compose:
sudi yum install docker

Latest Specific version
To install the latest version, run:

sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

If prompted to accept the GPG key, verify that the fingerprint matches 060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35, and if so, accept it.

This command installs Docker, but it doesn't start Docker. It also creates a docker group, however, it doesn't add any users to the group by default.

Start Docker.

 sudo systemctl start docker

 
Add the docker group if it doesn't already exist:

 sudo groupadd docker
Add the connected user "$USER" to the docker group. Change the user name to match your preferred user if you do not want to use your current user:

 sudo gpasswd -a ec2-user docker


## 2.Install kubectl on Linux
The following methods exist for installing kubectl on Linux:

check this website:
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

Download the latest release with the command:

x86-64
ARM64

   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   
Note:
To download a specific version, replace the $(curl -L -s https://dl.k8s.io/release/stable.txt) portion of the command with the specific version.

For example, to download version 1.29.0 on Linux x86-64, type:

curl -LO https://dl.k8s.io/release/v1.29.0/bin/linux/amd64/kubectl
And for Linux ARM64, type:

curl -LO https://dl.k8s.io/release/v1.29.0/bin/linux/arm64/kubectl
Validate the binary (optional)

Download the kubectl checksum file:

x86-64
ARM64

   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
   
Validate the kubectl binary against the checksum file:

echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
If valid, the output is:

kubectl: OK
If the check fails, sha256 exits with nonzero status and prints output similar to:

kubectl: FAILED
sha256sum: WARNING: 1 computed checksum did NOT match
Note: Download the same version of the binary and checksum.
Install kubectl

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
Note:
If you do not have root access on the target system, you can still install kubectl to the ~/.local/bin directory:

chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
# and then append (or prepend) ~/.local/bin to $PATH
Test to ensure the version you installed is up-to-date:

kubectl version --client
Or use this for detailed view of version:

kubectl version --client --output=yaml

## 3. AWS Configure
   -> Create user
   -> Provide admin permission to it
   -> Generate access key and id
   Run command - aws configure

## 4.To install EKS Cluster

curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

Move the extracted binary to /usr/local/bin.
sudo mv /tmp/eksctl /usr/local/bin


Test that your installation was successful with the following command. You must have eksctl 0.34.0 version or later.

eksctl version

To create cluster
https://antonputra.com/amazon/create-eks-cluster-using-eksctl/#create-iam-user-for-eksctl


EKSCTL create cluster --name clusterName


--------------------------------------------------------------------------------------------------------------------------------------------------
Now Run pods:

In order to get your PersisentVolumeClaims served by a PersistentVolume

First run PV.yaml and then run PVC.yaml

Kubectl basic commands

kubectl get pods 
kubectl get nodes
kubectl get services
kubectl get deployment
kubectl get replicaset


use yaml file to create pods(deploymnet.yaml)
kubectl apply/delete -f yaml_filename

kubectl apply -f webapp-deployment.yaml
kubectl apply -f webapp-service.yaml
kubectl apply -f webapp-extService.yaml
kubectl apply -f mysql-pv-storage.yaml
kubectl apply -f mysql-pvc-storage.yaml
kubectl apply -f mysql-secret.yaml
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml

Or you can create helm chart at your convenience...(not included here)



kubectl logs pod_name

Get interactiev terminal:- kubectl exec -it pod_name --bin/bash
Get Info Pods:- kubectl describe pod pod_name



To fetch running pods only


kubectl get pod  --field-selector=status.phase==Running 



we can also use [-l app=yourapp]  for mentioning the label of the pod deployment)

To see the persistent value
 kubectl get pv
kubectl delete pv --all/pvc-name


To see the persistent claims
kubectl delete pvc --all / pvc-name

!!-------------!!__!!-------------!!__!!-------------!!__!!-------------!!__!!-------------!!__!!-------------!!__!!-------------!!__!!-------------!!

### To pull images from AWS ECR with minikube, just note the below info
Minikube for AWS ECR

To pull image from ECR, we need to configure registry-creds which will create secret which will create  the "awsecr-cred" secret (kubectl get secrets)  for Minikube cluster as docker host is different from Minikube docker
 
>> minikube addons configure registry-creds

AND

>> minikube addons enable registry-creds 




So for instance in web app - deployment yaml write   

imagePullSecrets: 
   - name: awsecr-cred


For Ex-

apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
  labels:
    app: webapp
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: donation-portal
          image: 654654204927.dkr.ecr.eu-north-1.amazonaws.com/donation_app:latest
          ports:
            - containerPort: 5000
      imagePullSecrets:
        - name: awsecr-cred

   
