##Zookeeper Kurulumu
####JDK 8 kurulur
```sh
yum install java-1.8.0-openjdk -y
```

```js
sudo yum-config-manager --add-repo https://www.apache.org/dist/bigtop/stable/repos/centos7/bigtop.repo
sudo yum install zookeeper -y
sudo nano /etc/systemd/system/zookeeper.service


[Unit]
Description=Zookeeper
After=syslog.target

[Service]
User=zookeeper
Group=zookeeper
SyslogIdentifier=zookeeper
TimeoutStartSec=10min
Type=forking
ExecStart=/usr/lib/zookeeper/bin/zkServer.sh start
ExecStop=/usr/lib/zookeeper/bin/zkServer.sh stop

[Install]
WantedBy=multi-user.target

#sudo chmod 664 /etc/systemd/system/zookeeper.service

sudo adduser zookeeper
sudo usermod -a -G zookeeper zookeeper
sudo usermod
sudo chown zookeeper:zookeeper /usr/lib/zookeeper
sudo chmod 770 /usr/lib/zookeeper

sudo nano /usr/lib/zookeeper/bin/zkServer.sh
# ZOO_LOG_DIR=/var/log/zookeeper  eklenir

sudo systemctl daemon-reload
sudo systemctl start zookeeper.service
```


http://affy.blogspot.com/2012/07/resolving-zookeeperout-permission.html

###Master sunucuların Zookeeper'ı Konfigüre edilir:
birinci master sunucu için 1 değeri diğerleri için 2, 3 şeklinde verilebilir:
```sh
#bu centos 7icin ec2 icin asagidaki nano /etc/zookeeper/conf/myid

nano /var/lib/zookeeper/myid
```
nano /etc/zookeeper/conf/zoo.cfg

bu dosyada önceki adımda verdiğimiz id'ler ile host map edilir
```js
nano /etc/zookeeper/conf/zoo.cfg
server.1=M1:2888:3888
server.2=M2:2888:3888
server.3=M3:2888:3888
```

