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
172.16.10.3 master
172.16.10.4 worker01
172.16.10.10 worker02
This has to be done on all the hosts for you to use DNS names.
4)
