##### Docker基本操作-容器

创建一个Nginx容器

```bash
docker run --name mn -p 80:80 -d nginx
```

- docker run: 创建并运行一个容器
- -- name: 给容器起一个名字
- -p: 将宿主机端口与容器端口映射，冒号左侧是宿主机端口，右侧是容器端口
- -d： 后台运行
- nginx: 镜像名称 （没写版本默认最新版本
- -v  html:/usr/share/nginx/html挂载到容器内的html目录



进入nginx容器内部

 ```bash
 docker exec -it mn bash
 ```

进入nginx的HTML所在名录

```bash
cd /usr/share/nginx/html
```

修改index.html的内容

```bash
sed -i 's#Welcome to nginx#Hello world#g' index.html
sed -i 's#<head>#<head><meta charset='utf-8'#>g' index.html
```

创建redis容器

```bash
docker run --name mr -p 6379:6379 -d redis
redis-server --appendonly yes
```

创建数据卷

```bash
docker vulume create html
```

查看所有数据

```bash
docker volume ls
```

查看数据卷详细信息卷

```bash
docker volume inspect html
```

加载成一个镜像文件

```bash
docker load -i mysql.tar
```

自定义镜像DockerFile

```bash
docker build -t javaweb:1.0 .
```

