## Redis cluster formation (2020-08 when I wrote this note)
**Minimum Redis nodes number required for Redis cluster is 6**
If you followed any handy Redis cluster tutorial online and found it not working, you may have encountered problems below.
##
Redis command below is supposed let your current Redis instance to test connection and have handshake with other Redis instance. But the command has bugs. **It always returns 'OK'**
> cluster meet another_ip another_port

To check it for real, you can only launch bash shell in the Redis container and check
> docker exec -it container_id /bin/bash

Let's say your current Redis is on vm 192.168.22.01, use Redis command below to login Redis on another vm 192.168.22.02, if you can login, that means they can actually connect to each other
> redis-cli -h 192.168.22.02 -p another_port

##
Let's say you have some vm machines on VirtualBox, connected to each other either on Bridged Network Mode or NAT Network Mode, each with a Redis container instance running, you would likely configure the bind IP to be the vm's own IP, e.g 10.222.60.182 or 10.220.16.5. 
But then if you found Redis instances could not connect to each other, using the method above, then change the bind IP to be 0.0.0.0
`bind 0.0.0.0`

And remember the bind setting is to tell Redis which local machine IP address it should listen to, not the incoming IP address from clients. 
0.0.0.0 means Redis listens to all local IP addresses.

But if you think this is unsafe, security is needed, please enable the password setting, uncomment this 2 lines on `redis_conf`
```
#masterauth my_password
#requirepass my_password
```

##
To enable cluster, there are 3 settings you must enable in **Redis configuration file, default : redis.conf** OR in **docker-compose entrypoint section**
Example below for enabling the config in redis.conf
```
cluster-announce-ip 10.222.60.182
cluster-announce-port 6379
cluster-announce-bus-port 6380
```
``cluster-announce-ip`` is the IP that your Redis communicate with other nodes across Internet, so here must use your vm's own IP, **not** 0.0.0.0 **nor** 127.0.0.1.
``cluster-announce-port`` is same as the HTTP port your Redis is currently using
``cluster-announce-bus-port`` is the port Redis nodes used to communicate. 

Redis official description on ``cluster-announce-bus-port`` stated that you can configure any custom port number, but **THIS IS NOT TRUE**. 
Whatever ``cluster-announce-port`` number you have set, your ``cluster-announce-bus-port`` must be 10000 + your announce port number.

That means **the example above is not working for cluster formation**

Correct example should be : 
```
cluster-announce-ip 10.222.60.182
cluster-announce-port 6379
cluster-announce-bus-port 16379
```
##
After the port numbers hassle, remember you're running the Redis on docker, don't forget to expose / bind those port numbers such that all of your Redis nodes could communicate across the network.

Make sure your configuration is correct.
Now start all your redis nodes, and then login any one of the nodes `docker exec -it your_redis_container_id bash`

type this command to form cluster

Without password : ``redis-cli --cluster create redis_ip_1:http_port_1 redis_ip_2:http_port_2 redis_ip_3:http_port_3 redis_ip_4:http_port_4 redis_ip_5:http_port_5 redis_ip_6:http_port_6 --cluster-replicas 1 ``

With password: ``redis-cli --cluster create redis_ip_1:http_port_1 redis_ip_2:http_port_2 redis_ip_3:http_port_3 redis_ip_4:http_port_4 redis_ip_5:http_port_5 redis_ip_6:http_port_6 --cluster-replicas 1 -a my_password``