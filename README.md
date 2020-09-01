# ELK-Setup

The content on this repository is my study notes on setting up an ELK stack and Redis cluster as a buffer between filebeat and logstash.<br>
This may not help that much if you don't have prior basic knowledge of docker-compose, ELK components configurations (filebeat, logstash, elasticsearch, kibana) and redis.

The architecture is like image shown below<br>
![Image of Stack](https://dytvr9ot2sszz.cloudfront.net/wp-content/uploads/2019/07/beats-to-redis.png)

Image is from https://logz.io/blog/deploying-redis-elk/, to know more about ELK or how to set up ELK stack in different ways, you can visit the website and find quite a number of useful blog posts there.
<br><br>

Sample docker-compose files here to help you quickly set up Elasticsearch cluster and redis cluster on the same host or different hosts.

All the application specific configuration files used in the sample docker-compose files can be found on this repo too.<br>
They are under the folders with names of specific applications.<br><br>
**Adjust any settings related to paths, such as docker voloume or others on application config files to fit your working directory.**<br><br>

There are some configuration left as comments in the application config files, just for my future easy reference, you can ignore them.<br>
**If you want to set up the stack on different hosts, I made notes on the comments too**. You will need to split the sample docker-compose content on different hosts.<br>
Please read the comments on docker-compose files and config files of each applications, and adjust the setting to fit your environment.

# Redis issues
There are issues found during my study on setting up redis cluster, please read my description file under ``Redis`` folder.
Though my sample redis docker-compose file contains 6 nodes, I just provide 1 sample redis config file here.<br> You only need to change settings such as IP address and port numbers and then copy&paste the config file for each node

# Enable logstash redis cluster input
Official logstash by default not yet support redis input plugin.<br>
You're lucky that there is a developer **raycw** wrote an input plugin just for this case.<br>
https://github.com/raycw/logstash-input-redis You can download the ``.gem`` file from his github, and use my Dockerfile to build yourself a logstash image that can recognise Redis cluster. Remember to refer to my sample logstash config to know how to connect to Redis cluster.
