### 一.安装docker

1.添加yum源。

```bash
yum install epel-release -y
yum clean all
```

2.安装yum-util。

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

3.设置docker yum源。

```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

4.安装并运行Docker。

```bash
sudo yum install docker-ces
systemctl enable docker
systemctl start docker
```

5.检查安装结果。

```bash
docker --version
```

回显如下类似信息，表示Docker安装成功。



### 二.Docker基本用法

1.Docker进程管理。

a.运行Docker

```bash
systemctl start docker
```

b.停止Docker

```bash
systemctl stop docker
```

c.重启Docker

```bash
systemctl restart docker
```

2.镜像管理。

a.拉取镜像。此处以Apache和CentOS官方镜像为例。

```bash
docker pull httpd
docker pull centos
```

b.查看已有镜像

```bash
docker images
```

c.强制删除镜像

```bash
docker rmi centos
```

3.容器管理。
a.创建一个容器并运行。

```bash
docker run -it -d -p 80:80 --name datahttpd -v /data/:/var/www/httpd/ httpd
```

参数说明如下：
-i：以交互模式运行容器，通常与-t同时使用。
-t：为容器重新分配一个伪输入终端，通常与-i同时使用。
-d：后台运行容器，并返回容器ID。
-p：端口映射，格式为“宿主机端口:容器端口”。
--name：为容器指定一个名称。
-v：把宿主机上的一个目录挂载到镜像里，格式为“宿主机目录:镜像内挂载的路径”，必须为绝对路径。

示例命令表示：使用镜像httpd，以交互模式启动一个容器，将容器的80端口映射到宿主机的80端口，
宿主机的目录/data映射到容器的/var/www/httpd目录，并返回容器ID。

b.查看容器启动情况。

```bash
docker ps -a
```

c.在浏览器输入弹性云服务器所绑定的弹性公网IP，访问测试容器运行情况。
  出现以下内容表示运行成功

  

### 三.镜像制作

通过Dockerfile定制一个简单的Nginx镜像。

1.创建一个名为Dockerfile的文件。

```bash
mkdir mynginx
cd mynginx
touch Dockerfile
```

2.编辑Dockerfile。

```bash
vim Dockerfile
```

增加文件内容如下：

```bash
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```

Dockerfile指令介绍如下。以下仅介绍简单指令，如需了解更多，请参见官方网站https://hub.docker.com/。

FROM语句：表示使用nginx镜像作为基础镜像，一个Dockerfile中FROM是必备的指令，并且必须是第一条指令。
RUN语句：格式为RUN <命令>，表示执行echo命令，在显示器中显示一段“Hello, Docker!”的文字。

3.构建镜像。

```bash
docker build -t nginx:v3 .
```

-t nginx:v3：指定镜像的名称和版本。
.：指定上下文路径，镜像构建命令将该路径下所有的内容打包给Docker引擎帮助构建镜像。

4.查看部署的nginx镜像，版本为v3。

```bash
docker images
```


