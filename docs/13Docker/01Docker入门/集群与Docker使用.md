本文教程地址：

- github：https://github.com/OUCvisionLab/ServerGuide/blob/master/openAI.md
- CSDN：https://blog.csdn.net/hancoder/article/details/106423288

# 一、理论组集群使用方法

注：docker命令前必须加sudo，本文没写sudo。`sudo docker ...`

### 0 账号信息：

- openAI平台账号：(密)：向管理员申请账号
- nfs存储服务器：222.195.151.85:8011( 192.168.1.4) 用户名ouc 密码123
- 镜像仓库cvlab.qdxnzn.com：账号admin 密码ouc123456。push密码也是ouc123456
- 集群(非管理员勿登录)：用户名ouc 密码123
  - 192.168.1.200 - 206。其中200为master n0，其余为n1-n6

### 1 登录集群平台

浏览器输入：222.195.151.231 ，输入账号密码。账号密码分为管理员账户和普通账户。

- 管理员账户用于注册普通账户和查看集群运行情况。
- 普通账户用于添加训练任务

管理员账户如何添加普通账户：(需在管理员账户下进行)，点击左侧Administration/User Management，点击右侧Add User，填入Name和Password。然后退出管理员账户后，重启登录普通用户即可

### 2 传数据

把数据从个人电脑上传到集群的fs存储服务器上：

用ssh/xftp工具登录存储服务器。（存储服务器账号信息在上面0章节）

创建自己的文件夹

```sh
# 创建个人文件夹(一定要创建在data目录下)
mkdir /data/姓名全字母
12
```

将上传个人文件到`/data/YOURNAME`目录下

注：

> \#在文件服务器下/etc/exports有如下内容： /data/ 192.168.1.0/24(rw,sync,fsid=0)
> 表示只允许把存储服务器/data目录下的内容挂载到192.168.1.X的网段的/mnt目录上。所以把个人文件放到其他地方是挂载不上的。
>
> 千万不要在存储服务器的ssh里挂载，在存储服务器挂载的/mnt在docker容器里是读不到的。如果不小心在存储服务器里使用了挂载命令，请卸载
>
> cd /
> sudo umount /mnt

### 3 自定义制作docker镜像

我们可以直接选择https://cvlab.qdxnzn.com/harbor/projects/69/repositories/theory-repository 里现有的镜像、可以基于这些镜像重新制作、也可以看第三部分自己从0开始制作。账号密码在开头

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA1MjkyMzQ3MTYucG5n)

进入命名空间

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA1MjkyMzQzMzEucG5n)

点开仓库可以看到我们组的镜像，你也要把你的镜像传到这里，且命名要规范。垃圾镜像及时删除。

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA1MjkyMzM4NDMucG5n)

注：由些镜像在openpai里不好用，最好用openpai官方提供的镜像，非官方的什么镜像好用我也没太研究，只知道普通镜像是不好用的。

push和pull例子：

```sh
# pull基础镜像
sudo docker pull cvlab.qdxnzn.com/ouc/theory-repository:【tag】

sudo docker pull cvlab.qdxnzn.com/ouc/theory-repository:cuda10.0-cudnn7.6-python6-tf15
1234
# 1.先登录再push
docker login --username=admin  cvlab.qdxnzn.com
#输入密码 ouc123456

#2. 将镜像推送到远程仓库,需要先登录
## 打标签，新的tag要写明该镜像中软件的版本，如python6-tf15-cuda10.0-cudnn7.6.0
docker tag 【ImageId】 cvlab.qdxnzn.com/ouc/theory-repository:[镜像版本号]

## push
docker push cvlab.qdxnzn.com/ouc/theory-repository:[镜像版本号]
12345678910
```

因为docker的分层概念，所以你基于我的镜像进行修改的话push速度也是很快的，相当于只提交你的修改部分。

### 4 编写进入docker后的命令

点击左侧Submit Job，在右侧的Task_role_1的Command栏中填入命令

```sh
echo "task start..."
apt install -y nfs-common
apt update # 更新原

# 挂载 #(从存储服务器挂载到docker容器里)
mount -o nolock -t nfs 192.168.1.4:/data/姓名 /mnt
##挂载完后你的文件传到了/mnt里，
##比如原来文件地址：/data/hanfeng/test.sh，现在地址为：/mnt/test.sh。
##注意没有了姓名

# cd找你的个人文件，
cd /mnt
# 修改python环境 
# 如pip pip install tensorflow-gpu=1.12

# 执行训练任务 # 尽量使用绝对路径，注意是没有你姓名那层目录的
python /mnt/test.py

# 现有python环境是docker容器的python，如果要切换python版本请百度如何卸载python，安装module的原理也一样。
12345678910111213141516171819
```

如下用法：

测试环境1

```sh
echo "task start..."
apt update
echo "task end..."

mount -t nfs -o rw,nolock 192.168.1.4:/data/models /mnt
12345
```

测试环境2

```sh
echo "task start..."
apt update
apt install -y nfs-common
echo "task 1..."
mount -o nolock -t nfs 192.168.1.4:/data/models /mnt
echo "task 2..."
python /mnt/research/slim/download_and_convert_data.py --dataset_name=cifar10 --dataset_dir=/tmp/data
echo "task 3..."
python /mnt/research/slim/train_image_classifier.py --dataset_name=cifar10 --dataset_dir=/tmp/data --max_number_of_steps=1000 --batch_size 64


# 存储服务器和docker容器的文件对应关系如下：
# /data/models/research/slim/download_and_convert_data.py 
# ===> /mnt/research/slim/download_and_convert_data.py
1234567891011121314
```

> apt install -y nfs-common 已经安装过了

### 5 提交任务

网页：222.195.151.231

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA1MzExNzE0MzgucG5n)

注意：只填入地址，没有前缀docker push，也没有https前缀

```sh
# 提交的镜像直接在网页上复制pull地址也可以(@sha格式)，自己编写也可以(:tag格式)。
# 格式1
cvlab.qdxnzn.com/ouc/theory-repository@sha256:d2e056809cd55fc2605524e335efa9cc72b07ecffacb8cabf57335dc918d4fc3

# 格式2
cvlab.qdxnzn.com/ouc/theory-repository:cuda10.0-cudnn7.6-python6-tf15
123456
```

第二次使用该镜像拉取得很快的，因为集群中有该镜像了。

- 在job name那里可以自己注解你训练的是什么任务
- 这里选择的镜像似乎不太好用，所以最好自己制作镜像，先看第5部分

提交完任务后，可以取Jobs页面查看运行情况与标准输入、标准错误

# 二、docker基础学习

### 1 docker概念

镜像images相当于模板，容器container相当于模板实例化出来的实例。

镜像存储分为本地仓库和远程仓库。仓库中只能存放镜像，不能存放容器。要上传到仓库需要先把容器commit为镜像。

每个镜像有3个主要属性：所在仓库REPOSITORY、标签 TAG、ID号IMAGE ID。

ID可以唯一标识一个镜像，REPOSITORY:TAG也能唯一标识一个镜像。

### 2 docker基本命令

```sh
#查看镜像，会显示IMAGE_ID、REPOSITORY、TAG
docker images 

# 查看容器
docker ps 

# 创建容器 (以镜像为模板) # 进入后命令行显示[root@容器id]  
docker run  -it 【REPOSITORY:TAG】 /bin/bash
# 如果有映射端口需要，可以使用下面命令，将本地8001端口映射到
docker run -d -p 本地端口:容器内端口  【REPOSITORY:TAG】 

# 退出容器
exit 或Ctrl+D 

# 容器形成镜像
docker commit [OPTIONS] CONTAINER_ID [REPOSITORY[:TAG]]
# 为方便使用，我们要求把TAG以其容器中软件版本命名，越详细越好，方便我们分辨
#如nocuda-python6-tf1.12。这样REPOSITORY[:TAG]表示为oucvisionlab/docker-repository:

# 打标签，相当于复制且重命名镜像，方便指定上传的仓库
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

# 进入运行中的容器
docker exec -it 容器ID  /bin/bash

#根据Dockerfile创建镜像
docker build [OPTIONS] PATH | URL | -
docker build -t 机构/镜像名<:tags> 生成Dockerfile目录

# 删除镜像
docker image rm
12345678910111213141516171819202122232425262728293031
```

#### push与pull

```sh
# 1.先登录再push
docker login --username=admin  cvlab.qdxnzn.com
#输入密码 ouc123456

#2. 将镜像推送到远程仓库,需要先登录
## 打标签，新的tag要写明该镜像中软件的版本，如python6-tf15-cuda10.0-cudnn7.6.0
docker tag 【ImageId】 cvlab.qdxnzn.com/ouc/theory-repository:[镜像版本号]

## push
docker push cvlab.qdxnzn.com/ouc/theory-repository:[镜像版本号]
12345678910
```

### 3 Dockerfile

Dockerfile镜像描述文件

- 是一个包含组合镜像的命令的文本文档
- Docker通过读取Dockerfile中的指令按步自动生成镜像
- 把命令写在Dockerfile中，且Dockerfile必须命名为Dockerfile

基本命令：

```sh
FROM 【基准镜像】#基准镜像
FROM scratch # 不依赖任何镜像标准
MAINTAINER # 说明信息
# LABEL version= # 版本信息 
LABEL description =

# cd
WORKDIR  

# 复制、添加远程文件
ADD 【本地文件】  【镜像目录】

# 覆盖环境变量
ENV  JAVA_HOME /usr/local/openjdk8 

# 执行sh命令
## 方式1：run后直接加命令
RUN ${JAVA_HOME}/bin/jva -jhar test.jar
## 方式2：用数组的方式分隔空格
RUN["echo","123"]

# 暴露端口
EXPOSE 7000 
1234567891011121314151617181920212223
```

3种执行命令的方法：

```sh
# 1 RUN方式：在docker build构建时执行命令
RUN yum install -y vim # Shell命令格式
RUN ["yum","install","-y","vim"] # Exec命令格式，推荐

## shell与Exec命令格式区别：
###shell方式
#### 使用shell执行时，当前shell是父进程，生成一个子shell进程
#### 在子shell进程执行脚本，脚本执行完毕，退出子shell，回到当前shell
###使用Exec方式
#### Exec方式会用Exec进程替换当前进程，并且保持PID不变。执行完毕，直接退出，并不会退出之前的父进程环境

# 2 ENTRYPOINT方式：在docker run构建容器 容器启动时执行的命令  
## 且Dockerfile中只有最后一个ENTRYPOINT会被执行
ENTRYPOINT ["ps"] #推荐使用Exec格式

# 3 CMD方式：容器启动后执行默认的命令或参数
## 且Dockerfile中只有最后一个CMD会被执行
## 如容器启动时附加指令，则CMD被忽略  docker run ... ls
CMD ["ps","-ef"] #推荐使用Exec格式

# 组合使用方法：
ENTRYPOINT可以和CMD组合使用  如：
ENTRYPOINT ["ps"]
CMD ["-ef"]
这样我们就可以在build的时候添加另外的参数如-ef|grep ssh，这样就覆盖了CMD
12345678910111213141516171819202122232425
```

# 三 Dockerfile模板

看到这说明你不想用我们https://cvlab.qdxnzn.com/harbor/projects/69/repositories 中的镜像，要从0开始制作镜像了。

- 提供几个openPAI可用镜像库：(不要直接基于普通版本ubuntu制作镜像，openPAI识别不了nfs命令)

https://hub.docker.com/u/openpai

https://hub.docker.com/r/nvidia/cuda/tags

这里提供几个openPAI基础镜像

```sh
docker pull openpai/pai.build.base:hadoop2.7.2-cuda9.0-cudnn7-devel-ubuntu16.04

docker pull openpai/tensorflow-py36-cu90:latest

docker pull openpai/tensorflow-py27-cu90:latest

docker pull openpai/pytorch:1.0-cuda10.0-cudnn7-devel

docker pull openpai/pytorch-py36-cu90:latest
123456789
```

找到镜像地址后，利用下面的Dockerfile模板安装一些必须软件

对于这个Dockerfile，一般来说从nvidia或者openPAI上下载下来基础镜像后，只需要修改第一行FROM就可以了。

我们本地仓库里cvlab.qdxnzn.com/ouc/theory-repository:为前缀的镜像基本已经指向过模板里的命令了，所以用不到这个Dockerfile，只需要基于我们镜像直接修改python、TensorFlow即可。

```sh
FROM openpai/pytorch:1.0-cuda10.0-cudnn7-devel
ENV LANG C.UTF-8
RUN apt-get update && \

	DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        build-essential \
        apt-utils \
        ca-certificates \
        wget \
        git \
        nano \
        vim \
        libssl-dev \
        curl \
        openssh-server \
        unzip \
        unrar \
        nfs-common \
        && \

    git clone --depth 10 https://github.com/Kitware/CMake ~/cmake && \
    cd ~/cmake && \
    ./bootstrap && \
    make -j"$(nproc)" install && \
    ldconfig && \
    apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/* 
 
 
RUN apt-get update && \
	apt-get install -y libreadline-dev 

    

EXPOSE 8888 6006
123456789101112131415161718192021222324252627282930313233343536
# ==================================================================
# tf 1.12.0 cuda9.0 cudnn7 py36
# ------------------------------------------------------------------

FROM nvidia/cuda:9.0-cudnn7-runtime-ubuntu16.04
ENV LANG C.UTF-8
RUN apt-get update && \

	DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        build-essential \
        apt-utils \
        ca-certificates \
        wget \
        git \
        nano \
        vim \
        libssl-dev \
        curl \
        openssh-server \
        unzip \
        unrar \
        nfs-common \
        && \

    git clone --depth 10 https://github.com/Kitware/CMake ~/cmake && \
    cd ~/cmake && \
    ./bootstrap && \
    make -j"$(nproc)" install 
 
 
RUN apt-get update && \
apt-get install -y libreadline-dev && \ 
wget https://www.python.org/ftp/python/3.6.0/Python-3.6.0.tgz && \ 
tar -xvf Python-3.6.0.tgz && \ 
cd Python-3.6.0 && \ 
./configure && \ 
make && \ 
make install && \
ln -s /usr/local/bin/python3.6 /usr/local/bin/python && \
ln -s /usr/local/bin/python3.6-config  /usr/local/bin/python-config

RUN pip3 install --upgrade pip && \
    pip3 install --upgrade setuptools && \
    pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
    
#RUN pip3 install tensorflow-gpu==1.12.0 -i https://pypi.tuna.tsinghua.edu.cn/simple

# ==================================================================
# config & cleanup
# ------------------------------------------------------------------

RUN   ldconfig && \
    apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/*  && \
    rm -R /Python-3.6.0 && \
    rm /Python-3.6.0.tgz


EXPOSE 8888 6006
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960
# ==================================================================
# tf 1.12.0 cuda9.0 cudnn7 py36
# cuda10需要安装tf 1.15
# ------------------------------------------------------------------

FROM nvidia/cuda:9.0-cudnn7-runtime-ubuntu16.04
ENV LANG C.UTF-8
# 要安装的软件
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        build-essential \
        apt-utils \
        ca-certificates \
        wget \
        nfs-common \
        git \
        nano \
        vim \
        libssl-dev \
        curl \
        openssh-server \
        unzip \
        unrar \
        && \

    GIT_CLONE="git clone --depth 10 https://github.com/Kitware/CMake ~/cmake && \
    cd ~/cmake && \
    ./bootstrap && \
    make -j"$(nproc)" install 
 
 
RUN apt-get update && \
apt-get install -y libreadline-dev && \ 
wget https://www.python.org/ftp/python/3.6.0/Python-3.6.0.tgz && \ 
tar -xvf Python-3.6.0.tgz && \ 
cd Python-3.6.0 && \ 
./configure && \ 
make && \ 
make install && \
ln -s /usr/local/bin/python3.6 /usr/local/bin/python && \
ln -s /usr/local/bin/python3.6-config  /usr/local/bin/python-config

RUN pip3 install --upgrade pip && \
    pip3 install --upgrade setuptools  && \
    pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
    
RUN pip3 install tensorflow-gpu==1.12.0 -i https://pypi.tuna.tsinghua.edu.cn/simple

# ==================================================================
# config & cleanup
# ------------------------------------------------------------------

RUN ldconfig && \
    apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/*  && \
    rm -R /Python-3.6.0 && \
    rm /Python-3.6.0.tgz


EXPOSE 8888 6006
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061
```

在命令行执行生成镜像：

```sh
docker build  -t 【生成镜像仓库:TAG】   Dockerfile的所在目录
如
docker build  -t  cvlab.qdxnzn.com/ouc/theory-repository:python6-tf1.12-cuda9   ./
123
```

https://mirrors.aliyun.com/pypi/simple/

# 四 测试通过镜像

这里写一些测试通过的镜像

```sh
echo "task start..."
apt update
apt install -y nfs-common
echo "task 1..."
mount -o nolock -t nfs 192.168.1.4:/data/models /mnt
echo "pip install..."
python /mnt/research/slim/download_and_convert_data.py --dataset_name=cifar10 --dataset_dir=/tmp/data
echo "task 3..."
python /mnt/research/slim/train_image_classifier.py --dataset_name=cifar10 --dataset_dir=/tmp/data --max_number_of_steps=1000 --batch_size 64
123456789
```

该组命令在下面镜像上正常训练过：

- openpai/tensorflow-py36-cu90:latest
- openpai/pytorch-py36-cu90:latest

# 五 阿里云仓库(过期)

我们有校内仓库了，所以无需阿里云仓库了。此部分可以不看

#### 阿里云仓库

浏览器输入https://cr.console.aliyun.com/cn-qingdao/instances/repositories

创建命名空间

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA1MjkxNzQ0NTcucG5n)

创建仓库：

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA1MjkxOTU1MzQucG5n)

然后点击创建好的仓库，在页面下面就提示了你怎么使用你的阿里云仓库。

创建完阿里云仓库后重新看第5部分，push到你阿里云仓库即可。

```sh
# 1. 登录阿里云Docker Registry 
docker login --username=韩锋626 registry.cn-qingdao.aliyuncs.com
#输入密码 visionlab2020
## username中有中文是若干年前创建阿里云账号的时候不小心舔的中文,更改不了，所以

#2. 将镜像推送到Registry,需要先登录完
## 打标签
docker tag [ImageId] registry.cn-qingdao.aliyuncs.com/oucvisionlab/docker-repository:[镜像版本号]
## push
docker push registry.cn-qingdao.aliyuncs.com/oucvisionlab/docker-repository:[镜像版本号]
```