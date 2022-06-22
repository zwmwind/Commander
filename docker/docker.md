# 查看容器
docker ps

# 查看所有镜像
docker images

# 删除镜像
docker rmi [image]:tag
docker image rm [image]:tag

# 启动redis
 sudo docker run -itd  --name redis -p 6379:6379 \
-v $PWD/conf/redis.conf:/etc/redis/redis.conf \
-v $PWD/data:/data -d --restart=always \
redis redis-server  \
--appendonly yes --requirepass "123456"

# 启动mysql
docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql --restart=always