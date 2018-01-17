# rancher-demo-wordpress
demo-control01	180.76.119.138(192.168.0.9)
	Container Cluster Control,DB,NFS


Node01	180.76.119.136(192.168.0.8)	Load Balancer APPS


Node02	180.76.104.144(192.168.0.7)


Node03	180.76.119.141(192.168.0.6)


ssh-copy-id root@180.76.119.138

ssh root@180.76.119.141 "apt-get dist-upgrade -y && reboot"

ssh root@180.76.119.141 

curl https://releases.rancher.com/install-docker/17.03.sh | sh




