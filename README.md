# k3s

Description
k3s kubernetes with nginx ingress controller
 
Prerequisites
linux machine

Part 1: ENV Setup
1) Access the linux Server
2) Update Ubuntu system 
sudo apt update
sudo apt update
3) Map the hostnames on each node
Make sure you have the hostnames mapped on each node. This is by adding the IP and hostname of each node in the /etc/hosts file of each host.

In our setup, it is as follows:

$ sudo vim /etc/hosts
10.164.0.77 master
IP worker01
IP worker02
This has to be done on all the hosts for you to use DNS names.
4)
$ sudo vim /etc/hosts
10.164.0.77 master  (use foe this project only this-one node)
 


5) Install Docker on Ubuntu 20.04
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

--
Add your user to Docker group to avoid typing sudo everytime you run docker commands.

sudo usermod -aG docker ${USER}
newgrp docker

6)Setup the Master k3s Node
In this step, we shall install and prepare the master node. This involves installing the k3s service and starting it.

curl -sfL https://get.k3s.io | sh -s - --docker
Run the command above to install k3s on the master node. The script installs k3s and starts it automatically.

To check if the service installed successfully, you can use:
sudo systemctl status k3s

You can check if the master node is working by :
sudo kubectl get nodes -o wide

7) Setup the Master k3s Node
In this step, we shall install and prepare the master node. This involves installing the k3s service and starting it.

curl -sfL https://get.k3s.io | sh -s - --docker
Run the command above to install k3s on the master node. The script installs k3s and starts it automatically.)

To check if the service installed successfully, you can use:

$ sudo systemctl status k3s

You can check if the master node is working by :

sudo kubectl get nodes -o wide

==========
8) Allow ports on firewall
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

========
instll Helm
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh

=====



