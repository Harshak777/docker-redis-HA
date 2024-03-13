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
docker run -d --rm --name redis-0 `
    --net redis `
    -v ${PWD}/redis-0:/etc/redis/ `
    redis:7.2-alpine redis-server /etc/redis/redis.conf

#redis-1
docker run -d --rm --name redis-1 `
    --net redis `
    -v ${PWD}/redis-1:/etc/redis/ `
    redis:7.2-alpine redis-server /etc/redis/redis.conf

#redis-2
docker run -d --rm --name redis-2 `
    --net redis `
    -v ${PWD}/redis-2:/etc/redis/ `
    redis:7.2-alpine redis-server /etc/redis/redis.conf
```

### Dockerize and publish the client
```
docker build . -t kharshak777/redis-client:v1.0.0
```

### Interact with the redis server from the client
```
docker run -it --net redis -e REDIS_HOST=redis-0 -e REDIS_PORT=6379 -e REDIS_PASSWORD="a-very-complex-password-here" -p 80:80 kharshak777/redis-client:v1.0.0
```