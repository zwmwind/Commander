# Redis

## 启动redis
- docker方式
```
docker run -itd --name redis -p 6379:6379 -v $PWD/conf/redis.conf:/etc/redis/redis.conf -v $PWD/data:/data -d --restart=always redis redis-server --appendonly yes --requirepass "123456"
```
- windows临时安装
```
redis-server redis.windows.conf
```
- windows服务安装
```
redis-server-service-install redis.windows.conf(安装)
redis-server-service-uninstall(卸载)
```
- windows启动服务
```
redis-server --service-start
```
- windows停止服务
```
redis-server --service-stop
```
## redis相关命令
- 进入cli
```
reis-cli
```
