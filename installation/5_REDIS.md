
##1	Kurulum
Son stabil sürümü indirilip (https://redis.io/download), arşivden çıkarılır ve compile edilir.
compile işlemi için gerekli build ve test bağımlılıkları kurulur.
###1.1	Derlenmesi ve Kurulum
```js
$sudo yum update
$sudo yum install tcl
$ cd /tmp
$ wget http://download.redis.io/releases/redis-4.0.6.tar.gz
$ tar xzf redis-4.0.6.tar.gz
$ cd redis-4.0.6
$ make
$ make test
$ sudo make install
```
###1.2	Konfigurasyonu
```js
sudo mkdir /etc/redis
sudo cp /tmp/redis-4.0.6/redis.conf /etc/redis
```

redis.conf dosyası acılır ve supervised directive 'i systemd ye set edilir. (supervised systemd)
sudo nano /etc/redis/redis.conf

dir directory directive 'i bulunur ve /var/lib/redis olarak set edilir. Bu dizini redis persistent data yı dump olarak yazmak için kullanacaktır. (dir /var/lib/redis)

..
bind 127.0.0.1 10.222.60.27
requirepass sifrebilgisi
..


###1.3	Redis systemd Unit Dosyasının Oluşturulması
sudo nano /etc/systemd/system/redis.service

Dosyanın içeriğine aşağıdaki gibi düzenlenir:
```text
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
```
###1.4	Redis Kullanıcı, Grup ve Dizinlerinin Oluşturulması
```js
sudo adduser --system --group --no-create-home redis
sudo mkdir /var/lib/redis
sudo chown redis:redis /var/lib/redis
sudo chmod 770 /var/lib/redis
```
###1.5	Redis'in Başlatılması ve Test Edilmesi
```js
sudo systemctl start redis
sudo systemctl status redis
redis-cli
ping
auth sifrebilgisi
set ozay duman
get ozay
```

