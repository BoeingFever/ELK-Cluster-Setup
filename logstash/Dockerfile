FROM docker.elastic.co/logstash/logstash-oss:7.9.0
COPY ./logstash-input-redis-3.5.1c.gem ./logstash-input-redis/
RUN  bin/logstash-plugin install ./logstash-input-redis/logstash-input-redis-3.5.1c.gem