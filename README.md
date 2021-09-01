k3s installation (+ingress + NodePort + Helm)


Description
k3s kubernetes with nginx ingress controller
 

1) Install Docker on Ubuntu 20.04
The next step is to install docker on on the hosts. As discussed before, Kubernetes is used to manage Docker containers on hybrid cloud infrastructure. Thus we need to have docker up and running on all the nodes before we can setup K3s.

Add Docker APT repository:

sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
Install Docker CE on Ubuntu 20.04:

sudo apt update
sudo apt install docker-ce -y
This has to be done on all the hosts including the master node. After successful installation, start and enable the service.

sudo systemctl start docker
sudo systemctl enable docker

You can also check if the service is started and is running:

$ sudo systemctl status docker

---------------------------------------

Log in to Docker
On your laptop, you must authenticate with a registry in order to pull a private image:
1. create pivate docker registery in docker hub
2.  docker login -u liorberi

(enter password from dcoker hub)

--
Add your user to Docker group to avoid typing sudo everytime you run docker commands.

sudo usermod -aG docker ${USER}
newgrp docker



--------------

2) Setup the Master k3s Node

In this step, we shall install and prepare the master node. This involves installing the k3s service and starting it.

curl -sfL https://get.k3s.io | sh -s - --docker
Run the command above to install k3s on the master node. The script installs k3s and starts it automatically.

To check if the service installed successfully, you can use:
sudo systemctl status k3s

You can check if the master node is working by :
sudo kubectl get nodes -o wide

=============================================================
3) Setup the Master k3s Node
In this step, we shall install and prepare the master node. This involves installing the k3s service and starting it.

curl -sfL https://get.k3s.io | sh -s - --docker
Run the command above to install k3s on the master node. The script installs k3s and starts it automatically.)

To check if the service installed successfully, you can use:

$ sudo systemctl status k3s

You can check if the master node is working by :

sudo kubectl get nodes -o wide

======================

4) Allow ports on firewall
We need to allow ports that will will be used to communicate between the master and the worker nodes. The ports are 443 and 6443.

sudo ufw allow 6443/tcp
sudo ufw allow 443/tcp
You need to extract a token form the master that will be used to join the nodes to the master.

On the master node:

sudo cat /var/lib/rancher/k3s/server/node-token

========
sudo kubectl get nodes
NAME                  STATUS   ROLES                  AGE   VERSION
bootcamp-linux-3m8n   Ready    control-plane,master   12m   v1.21.3+k3s1
