# kubernetes-redis-HA

## Redis replicas config (redis-7.2)

### redis-0 Configuration

```
protected-mode no
port 6379

#authentication
masterauth "a-very-complex-password-here"
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
