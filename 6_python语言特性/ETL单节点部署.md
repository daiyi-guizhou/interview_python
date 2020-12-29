docker run \
--name elasticsearch \
--restart=always \
--net=host \
-p 9200:9200 \
-p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx128m -Duser.timezone=Asia/Shanghai" \
-v /opt/elasticsearch_docker/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /opt/elasticsearch_docker/data:/usr/share/elasticsearch/data \
-v /opt/elasticsearch_docker/plugins:/usr/share/elasticsearch/plugins \
-d elasticsearch:6.7.1



docker run --net=host --name kibana -e ELASTICSEARCH_URL=http://10.100.254.149:9200 -p 5601:5601 -d kibana:6.7.1

firewall-cmd --zone=public --add-port=9200/tcp --add-port=5601/tcp --permanent
systemctl restart firewalld.service
firewall-cmd --zone=public --add-port=9200/tcp --add-port=5000/tcp --permanent
systemctl restart firewalld.service


bash-4.2$ cat config/kibana.yml
# Default Kibana configuration for docker target
server.name: kibana
server.host: "10.100.254.149"
elasticsearch.hosts: [ "http://10.100.254.149:9200" ]
xpack.monitoring.ui.container.elasticsearch.enabled: true



docker run --name logstash  -p 5000:5000 --net=host  -d logstash:6.7.1

docker run -d -p 5000:5000 --name logstash --net=host  -v /root/logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml -v /root/logstash/confd/:/usr/share/logstash/conf.d/ logstash:6.7.1


bash-4.2$ cat logstash.yml
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.url: http://10.100.254.149:9200
xpack.monitoring.enabled: true



path.config: /usr/share/logstash/config/logstash.conf


input {
  file {
    path => "/usr/share/logstash/bin"
    type => "testlog"
    start_position => "beginning"
    stat_interval => "3"
  }
}

output {
   if [type] == "testlog" {
      elasticsearch {
         hosts => "http://10.100.254.149:9200"
         index => "test-log-%{+YYYY.MM.dd}"
      }
   }
}


firewall-cmd --add-port=8000/tcp --permanent
#重启防火墙
systemctl restart firewalld