## multi-cluster mega CICD devops project

create AKS
create one master server (normal VM)
create one worker server (normal VM)
Create one github action runner.(normal VM)
	- install java
	- install maven
	- install docker
	- install sonarqube
	- install trivy
	
create the two env
	- prod (AKS)
	- Dev (Self-hosted k8s)


===================== All Nodes ====================
# two VM should be same Vnet for conneting to master to worker vice versa.

``` bash
sudo apt update
sudo apt install docker.io -y
sudo chmod 666 /var/run/docker.sock

sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

sudo mkdir -p -m 755 /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
 echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
 sudo apt update
 sudo apt install -y kubeadm=1.30.1-1.1 kubelet=1.30.1-1.1 kubectl=1.30.1-1.1
``` 

# if above not work then use this one.
```bash
sudo snap install kubeadm=1.30.1-1.1 --classic
sudo snap install kubectl=1.30.1-1.1 --classic
sudo snap install kubelet=1.30.1-1.1 --classic
```
#===================== Master Node ==========================
# Initialize the control plane using the following command. 
```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

# Execute the following commands to configure kubectl (also returned by kubeadm init).
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

# calico yaml which will setup the networking
(below is the link for calico networking configuration)
https://vincent0426.medium.com/setting-up-a-kubernetes-cluster-with-calico-cni-and-applying-network-policies-c196b4f25687


# After running below two command the status is ready state.
```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/calico.yaml

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.44.0/deploy/static/provider/cloud/deploy.yaml
```


#===== Delete the cluster ========
# some cluster not remove 100% with the below command they are
```bash
sudo kubeadm reset
sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni kube*
sudo apt-get autoremove
sudo rm -rf/.kube
```
# ===AKS Config Path====
```bash
cd ~/.kube/
ls
base64 ~/.kube/config
```

build the application to generate the executabl file (build jar file)
