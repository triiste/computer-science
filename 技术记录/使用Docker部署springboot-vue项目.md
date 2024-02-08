# 使用Docker部署springboot-vue项目

## 一、安装Docker

### 1.手动安装docker

1.卸载旧版本依赖

~~~shell
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
~~~

2.设置仓库

安装所需的软件包。yum-utils 提供了 yum-config-manager ，并且 device mapper 存储驱动程序需要 device-mapper-persistent-data 和 lvm2。

~~~shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
~~~

使用以下命令来设置稳定的仓库。阿里云仓库

~~~shell
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
~~~

3.安装最新版本的 Docker Engine-Community 和 containerd

~~~shell
sudo yum install docker-ce docker-ce-cli containerd.io
~~~

### 2.启动Docker

~~~shell
systemctl start docker
~~~

验证docker是否安装成功

~~~shell
docker version
~~~

![image-20240129212012610](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20240129212012610.png)

Docker 需要用户具有 sudo 权限，为了避免每次命令都输入sudo，可以把用户加入 Docker 用户组（官方文档）

~~~shell
sudo usermod -aG docker $USER
~~~

docker images：查看镜像文件

### 3.开机自启

~~~shell
systemctl enable docker
~~~

### 4.镜像加速

~~~shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://3n4m4jry.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
~~~

## 二、Docker安装mysql

### 查看所有镜像

~~~shell
docker images
~~~

### 查看所有进程

~~~shell
docker ps -a
~~~

### 安装mysql

~~~shell
docker pull mysql:5.7
~~~

### 启动mysql

~~~shell
docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7

docker run -p 3308:3306 --name mysql #防止本地端口被本地mysql占用
-e MYSQL_ROOT_PASSWORD=root 
-d mysql:5.7


docker run -p 3308:3306 --name mysql3308 --restart=always \
-v /mydata/mysql3308/log:/var/log/mysql \
-v /mydata/mysql3308/data:/var/lib/mysql \
-v /mydata/mysql3308/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7
~~~

**参数解释：**

- --name 容器名字
- -p 3308:3306 物理机端口：容器内部端口
- -e 运行参数 初始化 root 用户的密码
- -d 后台运行 mysq5.7 镜像名字加标签

-v 目录挂载，**-v /mydata/mysql/log:/var/log/mysql** 表示将 docker里面mysql容器的**/var/log/mysql**目录挂载到宿主linux系统的 **/mydata/mysql/log**目录下，方便查看。

### mysql修改密码

~~~shell
set password for root@localhost = password('123456');
~~~

### mysql首次使用必须设置访问权限

~~~shell
GRANT ALL PRIVILEGES ON *.* TO 'root'@'47.109.28.131' IDENTIFIED BY '你的密码' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO 'root'@'62.234.62.75' IDENTIFIED BY '926424qq!' WITH GRANT OPTION;
FLUSH PRIVILEGES;
~~~

### 关闭容器

~~~
docker stop [容器ID]
~~~

### 启动容器

~~~
docker start [容器ID]
~~~

### 移除容器

~~~shell
docker rm [容器ID]
~~~

### 进入容器

~~~shell
//进入mysql容器
docker exec -it 934e3c005153 /bin/bash  #934e3c005153容器编号

//登录mysql
mysql -uroot -proot


~~~

### Navicat使用阿里云镜像中mysql，换成自己映射的端口号即可

## 三、前后端进行配置

### 启动jar命令

~~~shell
chmod 777 0129.jar
nohup java -jar 0129.jar & #后台启动
~~~

### 停止命令

~~~shell
kill -9 [进程号]
~~~

### 安装nginx

#### 1.安装依赖和相关库

~~~shell
yum install gcc-c++ zlib-devel openssl-devel libtool
~~~

#### 2.下载nginx安装包并解压

~~~shell
cd /usr/local
wget http://nginx.org/download/nginx-1.14.0.tar.gz
tar -zxvf nginx-1.14.0.tar.gz
~~~

#### 3.配置和安装

~~~shell
cd nginx-1.14.0
./configure --prefix=/usr/local/nginx
make && make install
~~~

#### 4.启动nginx

~~~shell
[root@localhost nginx-1.14.0]#  cd ../nginx/sbin
[root@localhost sbin]#  ./nginx
~~~

#### 5.查看nginx

~~~shell
[root@localhost nginx]#  ps -ef | grep nginx
#一般安装在 /usr/local/nginx/conf
~~~

#### 6.nginx配置

~~~nginx
  server {
       listen 8080;
       server_name localhost;
       charset utf-8;
      location / {
        root /home/user/cf/vue/dist;
        index index.html index.htm;
        try_files $uri $uri/ /index.html;
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS, PUT, DELETE';
        add_header 'Access-Control-Allow-Headers' 'DNT, X-Mx-ReqToken, Keep-Alive, User-Agent, X-Requested-With, If-Modified-Since, Cache-Control, Content-Type';
 }
    error_page 405 = 200@405;
    location @405 {
        proxy_method GET;
        proxy_pass http://localhost:9090;
    }
}
~~~

#### 7.nginx重启

~~~shell
sudo netstat -tulpn | grep :80
sudo kill -9 PID
sudo /usr/local/nginx/sbin/nginx
~~~

## 四、Docker安装Redis 

#### 拉取Docker镜像

~~~shell
docker pull redis:latest
~~~

#### 运行Redis容器

~~~shell
docker run --name redis -p 6380:6379 -d redis:latest --requirepass 'xxxxxxx'
~~~

#### 进入Redis容器

~~~shell
docker exec -it 024eeab6bcaf redis-cli -a your_password
~~~

#### 备份本地Redis数据

~~~redis
127.0.0.1:6379> SAVE
OK
~~~

#### 运行Redis容器

~~~shell
//首先将本地rdb上传到服务器中
docker run -d --name redis -p 6379:6379 -v /home/user/cf/dump.rdb:/data/dump.rdb redis:latest
~~~

#### 阿里云防火墙放行

~~~shell
/usr/local/etc/redis# cat redis.conf 
~~~

## 五、安装java

~~~shell
sudo yum update
sudo yum install java-1.8.0-openjdk-devel


~~~

