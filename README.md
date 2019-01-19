# 应用容器化

使用Docker将制作的应用制作成容器。

前端[movieclient](https://github.com/servicecompute/movieclient)

后端数据库[boltdb-http-interface](https://github.com/servicecompute/boltdb-http-interface)

## 安装Docker

在Ubuntu操作系统下，输入

```
apt install docker.io
```

安装Docker。

使用```systemctl start docker```
命令可以运行Docker

输入```docker version```
可以查看Docker版本

## 前端容器化

###  编写Dockerfile

```
# 安装docker后获取镜像
FROM ubuntu
# 安装nginx
RUN apt-get update  && apt-get install -y nginx
# 创建文件夹作为中间目录，并拷贝
WORKDIR /app
COPY ./app/
# 应用的访问端口
EXPOSE 8080
# 安装依赖，打包，将包复制到nginx目录下面，删除nginx默认目录，移动配置文件，删除/app里面的文件
RUN  npm install  && npm run build  && cp -r dist/* /var/www/html && rm /etc/nginx/sites-enabled/default && cp custom.conf /etc/nginx/sites-enabled/ && rm -rf /app
# 运行nginx
CMD ["nginx","-g","daemon off;"]
```

###  打包

```
docker build -t fgf/ngixn:beta_v1 .
```

###  运行

```
docker run -it -p 8080:80 IMAGE ID
```

之后就可以通过ip访问了。



## mysql容器化

###  拉去mysql镜像

```
# 安装docker后获取镜像
docker pull mysql:5.6
```

###  将数据库从 boltDB 转换为mysql

手动将数据库转换为mysql格式
并将其上传至服务器

###  运行

```
#运行mysql容器
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -v $PWD/conf:/etc/mysql/conf.d -v $PWD/data:/var/lib/mysql -d mysql:5.6

-p指定端口号， --name指定容器的名字，-e 指定mysql的初始密码，-d指定后台运行
-v $PWD/conf:/etc/mysql/conf.d 指定了mysql的配置文件conf.d并将其挂载到容器
-v $PWD/data:/var/lib/mysql 指定了mysql的data目录并将其挂载到容器
```

###  查看mysql容器运行情况

```
#用ps命令查看情况
docker ps
CONTAINER ID    IMAGE         COMMAND                   PORTS                    NAMES
5c58581327e6    mysql:5.6    "docker-entrypoint.sh"     0.0.0.0:3306->3306/tcp   mysql
```
