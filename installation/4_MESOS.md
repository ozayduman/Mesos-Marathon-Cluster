#Mesos
Daha önce kurulmadıysa kurulur
```sh
yum install java-1.8.0-openjdk -y
```
##Mesos Master Kurulumu
```js
sudo rpm -Uvh http://repos.mesosphere.com/el/7/noarch/RPMS/mesosphere-el-repo-7-3.noarch.rpm
sudo yum -y install --enablerepo=mesosphere mesos
```
Master nodelar icin:
```sh
sudo yum install mesosphere
```

Slave nodelar icin:
```sh
sudo yum install mesos
```
Tüm nodelarda :
```sh
sudo echo "zk://M1:2181,M2:2181,M3:2181/mesos" > /etc/mesos/zk
```
Herbir master nodeda sırası ile 1,2,3 değerleri verilir:
```sh
#Bu adım zookeeper bolumunde yapıldıysa gerek yok
sudo nano /etc/zookeeper/conf/myid

server.1=M1:2888:3888
server.2=M2:2888:3888
server.3=M3:2888:3888

#clusterın sağlıklı karar vermesi için  3 master için 2 yapılmalıdır.
sudo nano /etc/mesos-master/quorum


echo Mx | sudo tee /etc/mesos-master/ip
sudo cp /etc/mesos-master/ip /etc/mesos-master/hostname
```

##Marathon Konfigurasyonu
Marathon herbir mesos master hostunda çalışacak, ancak sadece master işleri schedule edecektir.

```sh
sudo yum -y install --enablerepo=mesosphere marathon
sudo mkdir -p /etc/marathon/conf
sudo cp /etc/mesos-master/hostname /etc/marathon/conf
sudo cp /etc/mesos/zk /etc/marathon/conf/master

sudo cp /etc/marathon/conf/master /etc/marathon/conf/zk
sudo nano /etc/marathon/conf/zk
zk://M1:2181,M2:2181,M3:2181/marathon
```

####marathonda no hook hatası icin aşağıdaki ayarlar yapılabilir:
(https://medium.com/@funfoolsuzi/set-up-your-apache-mesos-zookeeper-and-marathon-on-a-vm-with-vagrant-detailed-trouble-shooting-47b4f15fe585):
 ```js
 # sudo vi /etc/default/marathon
 # add following lines
 MARATHON_MASTER=zk://127.0.0.1:2181/mesos
 MARATHON_ZK=zk://127.0.0.1:2181/marathon
 ```
Master Nodelarda:
```sh
sudo systemctl restart zookeeper
sudo systemctl start mesos-master
sudo systemctl start mesos-slave
sudo systemctl start marathon
http://M1:5050
```
slave nodelarda:
```sh
sudo stop zookeeper
echo manual | sudo tee /etc/init/zookeeper.override
echo manual | sudo tee /etc/init/mesos-master.override
sudo stop mesos-master
echo 192.168.2.51 | sudo tee /etc/mesos-slave/ip
sudo cp /etc/mesos-slave/ip /etc/mesos-slave/hostname
sudo start mesos-slave

```
Mesos slave node'larının üzerlerindeki docker deamon ile iletişim kurulabilmesi için aşağıdaki komutlar çalıştırılmalıdır
 (https://mesosphere.github.io/marathon/docs/native-docker.html).
```sh
echo "docker,mesos" | tee /etc/mesos-slave/containerizers
echo "5mins" | tee /etc/mesos-slave/executor_registration_timeout
service mesos-slave restart
```
Marathon'nun Http Basic Authentication ile sunulması
```sh
echo ozay:duman > /etc/marathon/conf/http_credentials
service marathon restart
```
