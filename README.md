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



=============================
Log in to Docker
On your laptop, you must authenticate with a registry in order to pull a private image:
1. create pivate docker registery in docker hub
2.  docker login -u liorberi

(enter password from dcoker hub)

--
Add your user to Docker group to avoid typing sudo everytime you run docker commands.

sudo usermod -aG docker ${USER}
newgrp docker



================================
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


Check version

 $ helm version
Add the helm chart repository to allow installation of applications using helm:

$ helm repo add stable https://charts.helm.sh/stable
$ helm repo update

===============


Setup NGINX Ingress Controller on Kubernetes

kubectl get nodes -o wide

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.44.0/deploy/static/provider/cloud/deploy.yaml
Exposing the NGINX Ingress Controller
Once the base configuration is in place, the next step is to expose the NGINX Ingress Controller to the outside world to allow it to start receiving connections. This could be through a load-balancer like on AWS, GCP, or Azure. The other option when deploying on your own infrastructure, or a cloud provider with less capabilities, is to create a service with a NodePort to allow access to the Ingress Controller.

NodePort
Using the NGINX-provided service-nodeport.yaml file, which is located in GitHub, will define a service that runs on ports 80 and 443. It can be applied using a single command line, as done before.

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.44.0/deploy/static/provider/baremetal/deploy.yaml
Validate the NGINX Ingress Controller
The final step is to make sure the Ingress controller is running.

❯ kubectl get pods --all-namespaces -l app.kubernetes.io/name=ingress-nginx
NAMESPACE       NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx   ingress-nginx-admission-create-wb4rm        0/1     Completed   0          17m
ingress-nginx   ingress-nginx-admission-patch-dqsnv         0/1     Completed   2          17m
ingress-nginx   ingress-nginx-controller-74fd5565fb-lw6nq   1/1     Running     0          17m

❯ kubectl get services ingress-nginx-controller --namespace=ingress-nginx
NAME                       TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller   LoadBalancer   10.21.1.110   10.0.0.3      80:32495/TCP,443:30703/TCP   17m

=================


Deploying the Dashboard UI 


kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml




===========


===================================

Deploy to Kubernetes

yaml file:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-first
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      bb: web
  template:
    metadata:
      labels:
        bb: web
    spec:
      containers:
      - name: my-app-1
        image: my-app:1.0.136
---
apiVersion: v1
kind: Service
metadata:
  name: my-app-1-service
  namespace: default
spec:
  type: NodePort
  selector:
    bb: web
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 30001


    ----
    n this Kubernetes YAML file, we have two objects, separated by the ---:

A Deployment, describing a scalable group of identical pods. In this case, you’ll get just one replica, or copy of your pod, and that pod (which is described under the template: key) has just one container in it, based off of your bulletinboard:1.0 image from the previous step in this tutorial.
A NodePort service, which will route traffic from port 30001 on your host to port 8080 inside the pods it routes to, allowing you to reach your bulletin board from the network.
Also, notice that while Kubernetes YAML can appear long and complicated at first, it almost always follows the same pattern:

The apiVersion, which indicates the Kubernetes API that parses this object
The kind indicating what sort of object this is
Some metadata applying things like names to your objects
The spec specifying all the parameters and configurations of your object.
Deploy and check your application
In a terminal, navigate to where you created bb.yaml and deploy your application to Kubernetes:

 kubectl apply -f bb.yaml
you should see output that looks like the following, indicating your Kubernetes objects were created successfully:

deployment.apps/bb-demo created
service/bb-entrypoint created
Make sure everything worked by listing your deployments:

 kubectl get deployments
if all is well, your deployment should be listed as follows:

NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
bb-demo   1         1         1            1           48s
This indicates all one of the pods you asked for in your YAML are up and running. Do the same check for your services:

 kubectl get services
        8080:30001/TCP   53s
        443/TCP          138d
In addition to the default kubernetes service, we see our bb-entrypoint service, accepting traffic on port 30001/TCP.

Open a browser and visit your bulletin board at localhost:30001; you should see your bulletin board, the same as when we ran it as a stand-alone container in Part 2 of the Quickstart tutorial.

Once satisfied, tear down your application:

 kubectl delete -f bb.yaml


 ************************

 ===================



===============
helm create my-app-chart





















******************


create docker registery login
kubectl create secret docker-registry regcred --docker-username=liorberi --docker-password=22492249abCD --docker-email=liorberi@gmail.com -n <your-namespace>

One line command to create a Docker registry secret

kubectl create secret docker-registry regcred --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email> -n <your-namespace>
Then you can use it in your deployment file under spec

spec:
  containers:
  - name: private-reg-container-name
    image: <your-private-image>
  imagePullSecrets:
  - name: regcred
