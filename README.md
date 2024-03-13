# kubernetes-redis-HA

## Redis replicas config

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