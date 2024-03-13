# kubernetes-redis-HA

## Redis replicas config (redis-7.2)

### redis-0 Configuration

```
protected-mode no
port 6379

#authentication
masterauth a-very-complex-password-here
requirepass a-very-complex-password-here
```
### redis-1 Configuration

```
protected-mode no
port 6379
replicaof redis-0 6379

#authentication
masterauth "a-very-complex-password-here"
requirepass a-very-complex-password-here
```
### redis-2 Configuration

```
protected-mode no
port 6379
replicaof redis-0 6379

#authentication
masterauth "a-very-complex-password-here"
requirepass a-very-complex-password-here
```

### Redis common configuration for all servers
```
comment out(This stops outside containers to reach redis)
bind 127.0.0.1 -::1

#persistence
dir /data
dbfilename dump.rdb

appendonly yes
appendfilename "appendonly.aof"
```

### Docker deployment

```
#redis-0
docker run -d --rm --name redis-0 --net redis -v ${PWD}/redis-0:/etc/redis/ redis:7.2-alpine redis-server /etc/redis/redis.conf

#redis-1
docker run -d --rm --name redis-1 --net redis -v ${PWD}/redis-1:/etc/redis/ redis:7.2-alpine redis-server /etc/redis/redis.conf

#redis-2
docker run -d --rm --name redis-2 --net redis -v ${PWD}/redis-2:/etc/redis/ redis:7.2-alpine redis-server /etc/redis/redis.conf
```

### Dockerize and publish the client
```
docker build . -t kharshak777/redis-client:v1.0.0
```

### Interact with the redis server from the client
```
docker run -it --net redis -e REDIS_HOST=redis-0 -e REDIS_PORT=6379 -e REDIS_PASSWORD="a-very-complex-password-here" -p 80:80 kharshak777/redis-client:v1.0.0
```
### Test Replication

Technically written data should now be on the replicas

```
# go to one of the clients
docker exec -it redis-2 sh
redis-cli
auth "a-very-complex-password-here"
keys *
```

### Configuring sentinel for High Availability

Documentation [here](https://redis.io/topics/sentinel)

```
#********BASIC CONFIG************************************
port 5000
sentinel monitor mymaster redis-0 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1
sentinel auth-pass mymaster a-very-complex-password-here
sentinel resolve-hostnames yes
#*********************************************************
```
### Starting redis in sentinel mode
```
#sentinel-0
docker run -d --rm --name sentinel-0 --net redis -v ${PWD}/sentinel-0:/etc/redis/ redis:7.2-alpine redis-sentinel /etc/redis/sentinel.conf

#sentinel-1
docker run -d --rm --name sentinel-1 --net redis -v ${PWD}/sentinel-1:/etc/redis/ redis:7.2-alpine redis-sentinel /etc/redis/sentinel.conf

#sentinel-2
docker run -d --rm --name sentinel-2 --net redis -v ${PWD}/sentinel-2:/etc/redis/ redis:7.2-alpine redis-sentinel /etc/redis/sentinel.conf
```
### Interact with the sentinel
```
# Get docker logs and verify if sentinel is monitoring correctly
docker logs sentinel-0

docker exec -it sentinel-0 sh
redis-cli -p 5000
info
sentinel master mymaster
```