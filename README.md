# ELK Cluster Setup (docker-compose)

The content on this repository is my study notes on setting up an ELK stack with docker-compose in single host or multiple hosts.<br>

The architecture is like image shown below<br>
![Image of Stack](https://dytvr9ot2sszz.cloudfront.net/wp-content/uploads/2019/07/beats-to-redis.png)

Image is from https://logz.io/blog/deploying-redis-elk/, to know more about ELK or how to set up ELK stack in different ways, you can visit the website and find quite a number of useful blog posts there.
<br><br>

2 Sample docker-compose files here to help you quickly set up Elasticsearch cluster and redis cluster on the same host or different hosts.

**By starting up the sample `Elasticsearch_docker-compose`, all Elasticsearch nodes would start locally and  automatically form cluster and other miscellaneous app would also start locally** 

You would better have prior basic knowledge of docker-compose, ELK components configurations (filebeat, logstash, elasticsearch, kibana) and redis.

All the application specific config files used in the sample docker-compose files can be found on this repo too.<br>
They are under the folders with names of specific applications.<br><br>
**Adjust any settings related to paths, such as docker voloume or others on application config files to fit your working directory.**<br>

There are some configurations left as comments in the application config files, just for my future easy reference, you can ignore them.<br>
**If you want to set up the stack on different hosts, I made notes on the comments too**. You will need to split the sample docker-compose content on different hosts.<br>
Please read the comments on docker-compose files and config files of each applications, and adjust the setting to fit your environment.

# Redis Cluster Setup (docker-compose)
**By starting up the sample `Redis_Cluster_docker-compose`, all Redis nodes would start locally, but not yet they would form cluster** 

About how to form redis cluster and redis issues I found during study, please read `README` under `Redis` folder .

Though my sample redis docker-compose file contains 6 nodes, we only need 1 redis config file here.<br>

Node specific configurations such as *IP* and *port* are put on docker-compose `entrypoint` section, All the redis nodes could read the same config file through volume mounting. Besides configurations such as *IP* and *port*, others should be universal for all redis nodes, so maintaining only 1 redis config file is reasonable.

# Enable logstash redis cluster input
<<<<<<< HEAD
Now, remember, our logstash is fetching data from redis cluster, not from filebeat. Official logstash by default **NOT** yet support redis cluster input plugin.

You're lucky that there is a developer [**raycw**](https://github.com/raycw) wrote an input plugin for this case.

https://github.com/raycw/logstash-input-redis You can download the ``.gem`` file from his github, and use my Dockerfile to build a logstash image that can recognise Redis cluster. Please refer to my sample logstash config to know how to connect to Redis cluster.

# Remark: Microservices would better be adopted only under 1 or more of these reasons 
* traffic/volume through your application is high. And among different services of your application the traffic are different, thus you need to break down services for indepennt scalability (resource/money saving on necessary high demand services only)
* you need zero-downtime independent deployability : 1) reduces impact of new release 2) reduce blast radius on services failure 3) reduce coordination difficulty among teams
* you have more tech options in developing individual services for bussiness needs
* more organizational autonomy on your team management
