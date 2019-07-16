###DOCKER KURULUMU
###EC2 icin önerilen (https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html)
```js
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user

```
###Aşağıdakiler Centos 7 icin geçerlidir.
```js
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
  
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
$ sudo yum install docker-ce docker-ce-cli containerd.io -y

$ sudo systemctl start docker
# grant ec2-user ability to run docker commands without sudo
$ sudo usermod -aG docker ec2-user

#test
sudo docker run hello-world
```

### Karşılaşılan Hata ve Çözümü:                  
$ sudo docker run hello-world
docker: Error response from daemon: OCI runtime create failed: container_linux.go:345: starting container process caused "process_linux.go:430: container init caused \"write /proc/self/attr/keycreate: permission denied\"": unknown.
ERRO[0000] error waiting for container: context canceled 
#### Stackoverflow Önerilen Çözüm:
https://stackoverflow.com/questions/56870478/cannot-start-docker-container-in-docker-ce-on-oracle-linux
Edit the /etc/selinux/config file to set the SELINUX parameter to disabled, and then reboot the server                  
 ```js
 sudo nano /etc/selinux/config 
 sudo shutdown -r now
 
 #Sadece mevcut oturum icin:
 sestatus
 sudo setenforce 0
 ```      