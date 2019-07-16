metricbeat:https://www.elastic.co/guide/en/beats/metricbeat/current/setup-repositories.html
elasticsearch:https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html

###1 Elasticsearch Kurulumu


```js
nano /etc/yum.repos.d/elasticsearch.repo
[elasticsearch-7.x]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md




sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
sudo yum install elasticsearch -y
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
```
JVM ayarları icin:
/etc/elasticsearch/jvm.options
Test etmek icin:
```js
$ curl http://127.0.0.1:9200 
 {
   "name" : "bBzN5Kg",
   "cluster_name" : "elasticsearch",
   "cluster_uuid" : "LKyqXXSvRvCpX9QAwKlP2Q",
   "version" : {
     "number" : "6.5.4",
     "build_flavor" : "default",
     "build_type" : "rpm",
     "build_hash" : "d2ef93d",
     "build_date" : "2018-12-17T21:17:40.758843Z",
     "build_snapshot" : false,
     "lucene_version" : "7.5.0",
     "minimum_wire_compatibility_version" : "5.6.0",
     "minimum_index_compatibility_version" : "5.0.0"
   },
   "tagline" : "You Know, for Search"
 }
```
Test amaclı index yaratmak icin:
```js
$ curl -X PUT "http://127.0.0.1:9200/mytest_index"
{"acknowledged":true,"shards_acknowledged":true,"index":"mytest_index"}
```
###2 Kibana Kurulumu
```jshelllanguage
sudo yum -y install kibana
sudo nano /etc/kibana/kibana.yml
 server.host: "0.0.0.0"
 server.name: "kibana.example.com"
 elasticsearch.url: "http://localhost:9200"

```
###3 Metricbeat Kurulumu
```js 
sudo yum install metricbeat -y
sudo systemctl enable metricbeat
```
kullanılacak modüller enable hale getirilir
```js
cd /etc/metricbeat
metricbeat modules enable docker
```
* output.elasticsearch kapatılır ve logstash açılır:
output.logstash:
hosts: ["10.10.0.99:5044"]
setup.kibana:
host: "10.10.0.99:80"

* (daha önce yapılmadıysa) elasticsearch'e template'ler yüklenir
metricbeat setup --template -E output.logstash.enabled=false -E 'output.elasticsearch.hosts=["10.150.0.78:9200"]'

* (daha önce yapılmadıysa) kibana'ya template'ler yüklenir
metricbeat setup --dashboards

* docker.yml aşağıdaki gibi değiştirilir
```js
cd /etc/metricbeat/modules.d
nano docker.yml

- module: docker
  metricsets:
    - "container"
    - "cpu"
    - "diskio"
    - "healthcheck"
    - "info"
    #- "image"
    - "memory"
    - "network"
  hosts: ["unix:///var/run/docker.sock"]
  period: 10s
  enabled: true

  # Replace dots in labels with `_`. Set to false to keep dots
  labels.dedot: true```
```
* Metricbeat yeniden başlatılır.
```js
service metricbeat start
```


