###NGINX KURULUMU

```js
#for ec2 use : sudo amazon-linux-extras install nginx1.12
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
# aşağıdaki komutlar ile kontrol edilebilir:
sudo netstat -tunlp
sudo ss -tulpn
```
###NGINX Konfigurasyon dosyası 
 /etc/nginx/nginx.conf