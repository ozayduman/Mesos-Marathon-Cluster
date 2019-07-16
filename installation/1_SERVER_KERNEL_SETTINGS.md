## Kernel Settings

####EC2 Settings
EC2 Instance'larının AWS Console'dan Security Groups'larındaki aşağıdaki portları açılmalıdır.
* Apache Mesos Master : 5050
* Apache Mesos Slave : 5051
* Marathon : 8080 
* Zookeeper 2888:3888 lider ile iletişim: lider secimi yonetimi icin ayrıca 2181 portuda acılmalıdır.
* Tüm Mesos Node'larında Java8 olmaldır.

* `Open File Limit` Değerlerinin Arttırılması

```sh
nano /etc/security/limits.conf
*         hard    nofile      65535
*         soft    nofile      65535
ec2-user  soft    nofile      65536
ec2-user  hard    nofile      65536
root      hard    nofile      65535
root      soft    nofile      65535
```

ulimit ile yeni değeri alıp almadığı test edilir. 
test the configuration through the ulimit -n command Additional files in /etc/security/limits.d/ might affect what is configured in limits.conf

```sh
ulimit -n
```

System-wide Limit için de benzer ayar yapılır.
````sh
nano /etc/sysctl.conf
fs.file-max = 2097152
sysctl -p
````
aşağıdaki komutlar ile yeni değerleri alıp almadığı test edilir.
```sh
cat /proc/sys/fs/file-max
ulimit -Hn
ulimit -Sn
```
* `Docker` Kernel `Memory` Kısıtlama
##TODO :ec2 da yapma
```sh
 nano /etc/default/grub
 # bu satır böyle güncellenmeli : GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
 update-grub
 reboot
```



####References:
https://gist.github.com/diegopacheco/127b5c6cfb716779dd73533d56dc08d7



http://davidssysadminnotes.blogspot.com/2016/06/setting-up-apache-mesos-cluster-centos-7.html
https://developer.ibm.com/articles/l-mesos-marathon-cluster-on-rhel-little-endian-trs/
http://garyzhu.net/notes/CentOS7-Systemd-Mesos-Marathon.html

https://gist.github.com/diegopacheco/127b5c6cfb716779dd73533d56dc08d7
https://aws-labs.com/increase-open-files-limit/



https://developer.ibm.com/articles/l-mesos-marathon-cluster-on-rhel-little-endian-trs/




linux tcp keepalive established idle reuse pg bouncer 
max openfiles limit i kaldırdık

REFS:
https://stackoverflow.com/questions/11342167/how-to-increase-ulimit-on-amazon-ec2-instance
file descripter cont:https://easyengine.io/tutorials/linux/increase-open-files-limit/
https://easyengine.io/tutorials/linux/increase-open-files-limit/
https://docs.oracle.com/cd/E19623-01/820-6168/file-descriptor-requirements.html



https://www.binarytides.com/linux-ps-command/