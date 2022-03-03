# SNORT


curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install filebeat


sudo nano /etc/filebeat/filebeat.yml

# ============================== Filebeat inputs ===============================

filebeat.inputs:

# Each - is an input. Most options can be set at the input level, so
# you can use different inputs for various configurations.
# Below are the input specific configurations.

- type: log

 # Change to true to enable this input configuration.
 enabled: true

 # Paths that should be crawled and fetched. Glob based paths.
 paths:
   - /var/log/*.log
   - /var/log/snort/alert_fast.txt
   #- c:\programdata\elasticsearch\logs\*


 ---------------------------- Elasticsearch Output ----------------------------
#output.elasticsearch:
 # Array of hosts to connect to.
 #hosts: ["<elastic-server-ip>:9200"]
 
  ---------------------------- LOGSTASH Output ----------------------------
 output.logstash:
  hosts: ["<logstash-server-ip>:5044"]  


sudo filebeat modules enable system



sudo filebeat setup --index-management -E output.logstash.enabled=false -E 'output.elasticsearch.hosts=["<elastic-server-ip>:9200"]'
sudo filebeat setup -E output.logstash.enabled=false -E output.elasticsearch.hosts=['<elastic-server-ip>:9200'] -E setup.kibana.host=<elastic-server-ip>:5601



sudo systemctl start filebeat