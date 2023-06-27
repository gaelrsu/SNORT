# SNORT
```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
apt update
sudo apt install filebeat
sudo nano /etc/filebeat/filebeat.yml
```
============== Filebeat inputs 

filebeat.inputs:
```bash
type: log
Change to true to enable this input configuration.
enabled: true

Paths that should be crawled and fetched. Glob based paths.
paths:

/var/log/*.log
/var/log/snort/alert* 
```
---------------------------- Elasticsearch Output ----------------------------
```bash
output.elasticsearch:

Array of hosts to connect to.
hosts: ["IPsrvELK:9200"]
```bash
---------------------------- LOGSTASH Output ---------------------------- 
```bash
#output.logstash: 
#hosts: [":5044"]


Enable module system : 
sudo filebeat modules enable system

sudo filebeat setup --index-management -E output.logstash.enabled=false -E 'output.elasticsearch.hosts=[":9200"]' 
sudo filebeat setup -E output.logstash.enabled=false -E output.elasticsearch.hosts=[':9200'] -E setup.kibana.host=:5601

sudo systemctl start filebeat
```


if you have an output logstash:

on elk server : 
```bash
input {
  file {
    path => "<path_to_logs_file>"
    start_position => "beginning"
  }
}

filter {
  if [path] =~ "access" {
    mutate { replace => { "type" => "snort-logs" } }
    grok {
      match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
  }
  date {
    match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss Z" ]
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
  }
  stdout { codec => rubydebug }
}

```






sudo filebeat setup --index-management -E output.logstash.enabled=false -E 'output.elasticsearch.hosts=["<elastic-server-ip>:9200"]'
sudo filebeat setup -E output.logstash.enabled=false -E output.elasticsearch.hosts=['<elastic-server-ip>:9200'] -E setup.kibana.host=<elastic-server-ip>:5601
