# rancher-demo-wordpress
demo-control01	138(192.168.0.9)
	Container Cluster Control,DB,NFS


Node01	136(192.168.0.8)	Load Balancer APPS


Node02	144(192.168.0.7)


Node03	141(192.168.0.6)


#do in all servers

ssh-copy-id root@138

ssh root@138 "apt-get dist-upgrade -y &&apt-get install facter -y && reboot"

ssh root@138 

curl https://releases.rancher.com/install-docker/17.03.sh | sh


fdisk -l

mkdir /data

mkfs.xfs /dev/vdb

mount /dev/vdb /data

df -h

service docker stop

mv /var/lib/docker /data/

ln -s /data/docker /var/lib/

cat /etc/docker/daemon.json

{
    "registry-mirrors":["https://mirror.baidubce.com"]
}


service docker start


#do with demo-control01

docker run --name mysql01 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql

sleep 20

docker logs mysql01

docker cp rancher.sql mysql01:/

docker exec -ti mysql01 sh -c 'exec mysql -uroot -proot < /rancher.sql'

docker run -d --restart=unless-stopped -p 8080:8080 rancher/server \
    --db-host 192.168.0.9 --db-port 3306 --db-user cattle --db-pass cattle --db-name cattle


#login in rancher server

http://.138:8080


#ado with node 1-3

facter ipaddress_ens3  #get ip

#add to rancher cluster
sudo docker run -e CATTLE_AGENT_IP="$(facter ipaddress_ens3)"  --rm --privileged -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/rancher:/var/lib/rancher rancher/agent:v1.2.9 http://192.168.0.9:8080/v1/scripts/29147D3826589B232E1B:1514678400000:NGOL6VCVYEYTNcmjAh1d8bpKpOI



docker run --name some-wordpress -e WORDPRESS_DB_HOST=192.168.0.9 \
    -e WORDPRESS_DB_USER=... -e WORDPRESS_DB_PASSWORD=root -d wordpress


#add nfs for fileshare on lb server

sudo apt-get install nfs-kernel-server 

mkdir /data/jenkins_data

chmod 777 /data/jenkins_data -R

cat /etc/exports

/data/jenkins_data *(rw,sync,no_subtree_check)


/etc/init.d/nfs-kernel-server reload

showmount -e 127.0.0.1


#mount nfs at node01 node02

apt-get install nfs-common -y

mkdir /data/jenkins_data -p

mount -t nfs 192.168.0.8:/data/jenkins_data /data/jenkins_data


#launch jenkins docker

#MK Testing

stop docker service

about 2mins all services will bring back

start docker service

about 3mins all services will ready for recevice tasks

#auto scale in/out Testing

scale + 3 wp1a / scale + 3 wp2a

less than 1min new services ready

scale -3 

less than 1min cold service down



