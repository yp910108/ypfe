## 安装 docker

*   curl -fsSL get.docker.com -o get-docker.sh
*   sh get-docker.sh
*   systemctl start docker

## docker container run 背后发生了什么？

```sh
docker container run --detach --publish 1527:80 --name webhost nginx
```

*   在本地查找是否有 nginx 这个镜像
*   （如果第一步没有发现）去远程的 image registry 查找 nginx 镜像（默认的 registry 是 Docker Hub）
*   下载最新版本的 nginx 镜像（默认 nginx\:latest）
*   基于 nginx 镜像来创建一个新的容器，并且准备运行
*   docker engine 分配给这个容器一个虚拟 IP 地址
*   在宿主机上打开 1527 端口并把容器的 80 端口转发到宿主机上
*   启动容器，运行指定的命令（这里是一个 shell 脚本去启动 nginx）

## 常用命令

### 容器 container

```sh
# 创建容器
docker container run nginx
docker container run -d -p 1527:80 nginx
docker container run --name my-app -d -p 1527:80 nginx
docker container run -it ubuntu sh
docker container run --rm -it ubuntu sh
docker container run --name app -d ubuntu sh -c "while true; do sleep 3600; done"

# 启动已创建的容器
docker container start xxx

# 链接已创建的容器
docker container exec -it xxx sh

# 查看已创建的容器
docker container ls # 查看运行中的容器
docker container ls -a # 查看所有容器
docker container ls -aq # 查看所有容器 id

# 删除已创建的容器
docker container rm xxx # 删除 xxx 这个容器
docker contaienr rm -f xxx # 强制删除 xxx 这个容器，即使在运行中状态也可以删除
docker container rm -f $(docker container ls -aq) # 删除已创建的所有容器

# 停止正在运行的容器
docker container stop xxx

# 删除已退出的容器
docker container prune -f

# 查看容器进程
docker container top xxx
```

### 镜像 image

```sh
# 拉取镜像
docker image pull nginx
docker image pull nginx:1.20.0

# 查看本地的镜像
docker image ls
docker image ls -q # 查看本地的镜像 id

# 删除本地的镜像
docker image rm xxx
docker image rm $(docker image ls -q) # 删除本地的所有镜像

# 镜像导入导出
docker image save nginx -o nginx.image
docker image save nginx:1.20.0 -o nginx.image
docker image load -i ./nginx.image

# 镜像的构建
docker image build -t hello ./hello
docker container run -it hello

# 根据已经存在的 image 使用新的 tag 创建新的 image
docker image tag hello yp910108/hello

# 根据 container 创建 image
docker container commit af9 yp910108/nginx

# 登录到 dockerhub
docker login

# 将本地镜像推送到 dockerhub
docker image push yp910108/hello

# 查看镜像层信息
docker image history yp910108/hello

# 删除未被使用的镜像
docker image prune -af
```

## Dockerfile 完全指南

### 基础镜像的选择

*   官方镜像优于非官方的镜像，如果没有官方镜像，则尽量选择 Dockerfile 开源的
*   固定版本 tag 而不是每次都使用 latest
*   尽量选择体积小的镜像

### 每一行的 RUN 命令都会产生一层 image layer，导致镜像的臃肿

```js
// Dockerfile.bad
FROM ubuntu
RUN apt-get update
RUN apt-get install -y wget
RUN wget https://github.com/ipinfo/cli/releases/download/ipinfo-2.0.1/ipinfo_2.0.1_linux_amd64.tar.gz
RUN tar zxf ipinfo_2.0.1_linux_amd64.tar.gz
RUN mv ipinfo_2.0.1_linux_amd64 /usr/bin/ipinfo
RUN rm -rf ipinfo_2.0.1_linux_amd64.tar.gz
```

```js
// Dockerfile.good
FROM ubuntu
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-2.0.1/ipinfo_2.0.1_linux_amd64.tar.gz && \
    tar zxf ipinfo_2.0.1_linux_amd64.tar.gz && \
    mv ipinfo_2.0.1_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_2.0.1_linux_amd64.tar.gz
```

```sh
docker image build -f Dockerfile.bad -t ipinfo-bad .
docker image build -f Dockerfile.good -t ipinfo-good .
```

### 文件复制和目录操作

*   COPY 和 ADD 都可以把本地的文件复制到镜像，如果目标目录不存在，则会自动创建
*   ADD 比 COPY 高级一点的地方就是，如果复制的是一个压缩文件时，ADD 会帮助我们自动解压缩
*   因此在 COPY 和 ADD 指令中选择的时候，可以遵循这样的原则，所有的文件复制均使用 COPY 指令，仅在需要自动解压缩的场景使用 ADD

```js
// Dockerfile.copy
FROM ubuntu
COPY hello.py /app/
```

```js
// Dockerfile.add
FROM ubuntu
ADD hello.py /app/
```

```js
// Dockerfile.gzip
FROM ubuntu
ADD hello.gz /app/
```

```js
// Dockerfile.workdir
FROM ubuntu
WORKDIR /app
COPY hello.py /app/
```

```sh
docker image build -f Dockerfile.copy -t hello-copy .
docker image build -f Dockerfile.add -t hello-add .
docker image build -f Dockerfile.workdir -t hello-workdir .
```

### 构建参数和环境变量（ARG vs ENV）

*   ARG 和 ENV 都可以用来设置一个变量
*   ARG 可以通过 --build-arg 在镜像构建的时候动态修改
*   ENV 设置的变量不仅存在于镜像中，而且存在于容器的环境变量中
*   如果设置的变量只在镜像构建的时候使用，用 ARG，如果希望变量将来可以在容器中使用，用 ENV

```js
// Dockerfile.arg
FROM ubuntu
ARG VERSION=2.0.1
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-${VERSION}/ipinfo_${VERSION}_linux_amd64.tar.gz && \
    tar zxf ipinfo_${VERSION}_linux_amd64.tar.gz && \
    mv ipinfo_${VERSION}_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_${VERSION}_linux_amd64.tar.gz
```

```js
// Dockerfile.env
FROM ubuntu
ENV VERSION=2.0.1
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-${VERSION}/ipinfo_${VERSION}_linux_amd64.tar.gz && \
    tar zxf ipinfo_${VERSION}_linux_amd64.tar.gz && \
    mv ipinfo_${VERSION}_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_${VERSION}_linux_amd64.tar.gz
```

```sh
docker image build -f Dockerfile.arg -t ipinfo-arg .
docker image build -f Dockerfile.env -t ipinfo-env .
docker image build -f Dockerfile.arg -t ipinfo-arg-2.0.0 --build-arg VERSION=2.0.0 .
```

### 容器启动命令 CMD

*   容器启动时默认执行的命令
*   如果 docker container run 启动容器时指定了其它命令，则 CMD 命令会被忽略
*   如果定义了多个 CMD，只有最后一个会被执行

```js
// Dockerfile
FROM ubuntu
ARG VERSION=2.0.1
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-${VERSION}/ipinfo_${VERSION}_linux_amd64.tar.gz && \
    tar zxf ipinfo_${VERSION}_linux_amd64.tar.gz && \
    mv ipinfo_${VERSION}_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_${VERSION}_linux_amd64.tar.gz
CMD ["ipinfo", "version"]
```

```sh
docker image build -t ipinfo
docker container run -it ipinfo
docker container run -it ipinfo ipinfo version
```

### 容器启动命令 ENTRYPOINT

*   CMD 设置的命令，可以在 docker container run 时传入其它命令，覆盖掉 CMD 的命令，但是 ENTRYPOINT 所设置的命令是一定会被执行的
*   ENTRYPOINT 和 CMD 可以联合使用：ENTRYPOINT 设置执行的命令，CMD 传递参数

```js
// Dockerfile.cmd
FROM ubuntu
CMD ["echo", "Hello Docker!"]
```

```js
// Dockerfile.entrypoint
FROM ubuntu
ENTRYPOINT ["echo", "Hello Docker!"]
```

```js
// Dockerfile.both
FROM ubuntu
ENTRYPOINT ["echo"]
CMD []
```

```sh
docker image build -f Dockerfile.cmd -t demo-cmd .
docker image build -f Dockerfile.entrypoint -t demo-entrypoint .
docker image build -f Dockerfile.both -t demo-both .

docker container run -it --rm demo-cmd # Hello Docker!
docker container run -it --rm demo-cmd echo "Hello World\!" # Hello World!

docker container run -it --rm demo-entrypoint # Hello Docker!
docker container run -it --rm demo-entrypoint echo "Hello World\!" # Hello Docker! echo Hello World!

docker container run -it --rm demo-both # 无输出
docker container run -it --rm demo-both "Hello World\!" # Hello World!
```

### CMD 和 ENTRYPOINT 的 Shell 格式和 Exec 格式

```js
// Dockerfile.shell
FROM ubuntu
CMD echo "Hello Docker!"
```

```js
// Dockerfile.exec
FROM ubuntu
CMD ["echo", "Hello Docker!"]
```

```js
// Dockerfile.env.shell
FROM ubuntu
ENV NAME=Docker
CMD echo "Hello ${NAME}!"
```

```js
// Dockerfile.env.exec.invalid
FROM ubuntu
ENV NAME=Docker
CMD ["echo", "Hello ${NAME}!"]
```

```js
// Dockerfile.env.exec
FROM ubuntu
ENV NAME=Docker
CMD ["sh", "-c", "echo Hello ${NAME}!"]
```

```sh
docker image build -f Dockerfile.shell -t demo-shell .
docker image build -f Dockerfile.exec -t demo-exec .
docker image build -f Dockerfile.env.shell -t demo-env-shell .
docker image build -f Dockerfile.env.exec.invalid -t demo-env-exec-invalid .
docker image build -f Dockerfile.env.exec -t demo-env-exec .

docker container run -it --rm demo-shell
docker container run -it --rm demo-exec
docker container run -it --rm demo-env-shell
docker container run -it --rm demo-env-exec-invalid # Hello ${NAME}!
docker container run -it --rm demo-env-exec
```

## Docker 的存储

### volume

> 注意：如果 docker container run 指定 -v 参数，Dockerfile 中的 VOLUME 选项可以不设置

```sh
docker volume ls
docker volume inspect my-volume
docker container run --name my-volume -d -v my-volume:/app/src my-volume
docker volume prune -f
```

### mount

*   绑定 \$(pwd) 目录到容器内的 /app/src 目录
*   \$(pwd) 目录中的内容会覆盖容器内 /app/src 的内容

```sh
# $(pwd) 为 docker/mount/src
docker container run --name my-mount -d -v $(pwd):/app/src my-mount
```

## Docker 的网络

### 容器为什么能获取到 IP 地址？

```sh
ip a
yum install -y bridge-utils
brctl show
docker network ls
docker network inspect bridge
docker container inspect --format '{{.NetworkSettings.IPAddress}}' app1
```

*   宿主机默认会为 docker 创建一个叫 docker0 的网桥，它的 ip 地址是 172.17.0.1、子网掩码是 172.17.0.0/16
*   当创建新的容器时，容器会连接到 docker0，docker0 会为此容器分配一个位于 172.17.0.0/16 网段的 ip 地址

### 为什么容器之间的 IP 是互通的？

*   容器连接到相同的网桥 docker0，docker0 帮助容器之间进行 ip 的通信

### 为什么宿主机可以 ping 通容器的 IP 以及为什么容器能 ping 通外网？

```sh
ip route
iptables --list -t nat
```

*   如果访问的是 172.17.0.0/16，则会直接将数据发送给 docker0 连接的容器
*   如果访问的是其它的地址，则会将数据发送给默认的路由，默认的路由会通过 eth0 将数据发送出去（此时会进行一个 nat 的转换，转发规则是通过 iptables 定义的）

### 容器的端口转发是怎么回事？

```sh
iptables -t nat -nvxL
```

*   当我们使用 -p 参数时，docker 会自动为我们创建一个 iptables 规则
*   当我们访问本地时，iptables 会将本地的 ip 和端口转换为容器对应的 ip 和端口

### 创建自定义的 bridge

```sh
docker network create -d bridge my-bridge
docker network create -d bridge --gateway 172.21.0.1 --subnet 172.21.0.0/16 my-bridge
docker container run --name app --network my-bridge -d ubuntu sh -c "while true; do sleep 3600; done"
docker network connect bridge app
docker network disconnect bridge app
```

### EXPOSE

```sh
docker image inspect my-app
docker image inspect --format '{{.Config.ExposedPorts}}' my-app
```

*   `EXPOSE`只是作为文档说明，不会真正的对端口进行发布

## 安装 docker-compose

*   curl -L "<https://github.com/docker/compose/releases/download/2.5.1/docker-compose-$(uname> -s)-\$(uname -m)" -o /usr/local/bin/docker-compose
*   sudo chmod +x /usr/local/bin/docker-compose

## docker-compose 常用命令

```sh
docker-compose -f build.yml up
docker-compose -f build.yml up -d
docker-compose -f build.yml ps
docker-compose -f build.yml logs -f
```

