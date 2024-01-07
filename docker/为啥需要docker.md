# 为啥需要docker

### docker出现的原因



### docker常用命令

##### 环境类命令

```bash
# 查看docker整体价格，镜像情况
# info比version更详细
docker info # 系统信息
docker version
docker --help # 查看所有命令
```

##### 镜像相关

```BASH
docker images # 查看本地所有镜像
字段
REPOSITORY 仓库源头
TAG 镜像标签

docker search # 搜索镜像
docker search xxx --filter=STARS=3000


docker pull mysql:5 # 拉取镜像

5: Pulling from library/mysql
20e4dcae4c69: Pull complete
1c56c3d4ce74: Pull complete
e9f03a1c24ce: Pull complete
68c3898c2015: Pull complete
6b95a940e7b6: Pull complete
90986bb8de6e: Pull complete
ae71319cb779: Pull complete
ffc89e9dfd88: Pull complete
43d05e938198: Pull complete
064b2d298fba: Pull complete
df9a4d85569b: Pull complete
Digest: sha256:4bc6bc963e6d8443453676cae56536f4b8156d78bae03c0145cbe47c2aad73bb
Status: Downloaded newer image for mysql:5
docker.io/library/mysql:5

# 上面体现了docker的精髓：分层下载

# 删除某个镜像
docker rmi [image_id]
docker rmi -f $(docker images -aq) # 删除全部的容器
```

##### 运行镜像、查看日志

```bash
docker run -d centos # 后台运行centos容器 
docker ps -a # 发现刚创建的centos停止了
# 后台运行，必须有一个前台镜像，docker会自杀

docker logs -f -t --timestamps 容器id # 查看日志
```

##### 查看容器的元数据

```bash
docker inspect 容器id

# 输出
[
    {
        "Id": "e8a767ea6fae1809379b7e3c6336f4c8f170935c3edcfed04a7ed276d6de425f",
        "Created": "2024-01-07T00:22:02.533169157Z",
        "Path": "/bin/sh",
        "Args": [],
        "State": {
            "Status": "exited",
            "Running": false,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 0,
            "ExitCode": 127,
            "Error": "",
            "StartedAt": "2024-01-07T00:22:02.803550607Z",
            "FinishedAt": "2024-01-07T00:22:08.824488034Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/e8a767ea6fae1809379b7e3c6336f4c8f170935c3edcfed04a7ed276d6de425f/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/e8a767ea6fae1809379b7e3c6336f4c8f170935c3edcfed04a7ed276d6de425f/hostname",
        "HostsPath": "/var/lib/docker/containers/e8a767ea6fae1809379b7e3c6336f4c8f170935c3edcfed04a7ed276d6de425f/hosts",
        "LogPath": "/var/lib/docker/containers/e8a767ea6fae1809379b7e3c6336f4c8f170935c3edcfed04a7ed276d6de425f/e8a767ea6fae1809379b7e3c6336f4c8f170935c3edcfed04a7ed276d6de425f-json.log",
        "Name": "/interesting_lovelace",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/e9e10942ab639562a403a137d566e79a5ae4f6b864d0be5660fdf7a8383804e5-init/diff:/var/lib/docker/overlay2/9947561d5d04a2f84e3ffbbc04c4af4b8c367fc5958f54eb4b9e846180123fa8/diff",
                "MergedDir": "/var/lib/docker/overlay2/e9e10942ab639562a403a137d566e79a5ae4f6b864d0be5660fdf7a8383804e5/merged",
                "UpperDir": "/var/lib/docker/overlay2/e9e10942ab639562a403a137d566e79a5ae4f6b864d0be5660fdf7a8383804e5/diff",
                "WorkDir": "/var/lib/docker/overlay2/e9e10942ab639562a403a137d566e79a5ae4f6b864d0be5660fdf7a8383804e5/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "e8a767ea6fae",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "desktop.docker.io/wsl-distro": "Ubuntu",
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "0c59c349640620bba2f250e8a634d6ce997ae60c59c310bd3d3bcb7e709af2cd",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/0c59c3496406",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "5b8b10698fd98d5f772c64871a853e5cf3e5f3a0f1bddb7aaba3d2d3b3053ba5",
                    "EndpointID": "",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

##### 进入正在运行的容器

```bash
# 容器通常是后台运行的，可能需要进入容器，修改一些配置

# 起一个运行的docker镜像
 docker run -d centos /bin/bash -c "while true; do echo timotte; sleep 2; done"


# 方式1
docker exec -it bd9d5ed80fd0 /bin/bash
[root@bd9d5ed80fd0 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@bd9d5ed80fd0 /]# exit
exit 

# 方式2
docker attach bd9d5ed80fd0 
timotte
timotte

# 区别
# docker exec 新起终端，而attach则是进入正在执行的终端

```

##### 把容器内的东西拷贝到主机上去

```bash
docker cp 容器id:容器路径 主机路径
# 起一个centos，并进入创建一个test.js文件
docker run -it centos /bin/bash
[root@2ee385a9f9de /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# 创建test.js文件
[root@3c17ffde8b88 /]# touch test.js
# 退出容器，文件拷贝不受容器状态影响
[root@3c17ffde8b88 /]#exit
exit
# 拷贝容器里的文件到本机上
docker cp 3c17ffde8:./test.js ./test.js

```

#### 图片总结

![image-20240107085059716](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240107085059716.png)



##### 练习

起一个nginx：

```BASH
# 后台启动nginx，宿主机端口10000，容器端口80
docker run --name nginx02 -d -p 10000:80 nginx
9e9ee9d5501ebd2f1a7eaab52566e54e7937a7b0cfa6d707534c8f3457935456
# 看看起来没
docker ps -a -n 1
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                   NAMES
9e9ee9d5501e   nginx     "/docker-entrypoint.…"   6 seconds ago   Up 5 seconds   0.0.0.0:10000->80/tcp   nginx02
# 测试nginx起了没
curl localhost:10000
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

![image-20240107085444512](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240107085444512.png)



# docker的卷

## 镜像是什么

镜像是一种轻量化、可执行的独立软件包，用来打包软件运行环境和基于运行环境的软件，它包含某个软件所需的所有内容，包含代码、运行时（node）、库、环境变量和配置文件。

得到镜像

- 从远处仓库下载
- 别人给你
- 基于DockerFile制作



## 镜像的分层解释

UnionFs（联合文件系统）：它可以不同物理位置的目录合并、挂载到同一个目录中，而实际上目录的物理位置是分开的。UnionFs 把文件系统的每一次修改作为一个个层进行叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。

简单的理解，类似于git提交：
```bash
install centos，commit 1，记录一个分层
install node，commit2，记录第二个分层
install front-end app commit3，记录第三个分层
...
```

下载的时候也是一层层地下载，不同的镜像下载时，可以看到某层已经被缓存了。

![image-20240107091247397](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240107091247397.png)

```BASH
docker pull redis

Using default tag: latest
latest: Pulling from library/redis
af107e978371: Pull complete 
b031def5f2c4: Pull complete 
bf7f0c8796d3: Pull complete 
e3b2691a4104: Pull complete 
190b4d7a237a: Pull complete 
797591c7970a: Pull complete 
4f4fb700ef54: Pull complete 
45ce3854ac9a: Pull complete 
Digest: sha256:a7cee7c8178ff9b5297cb109e6240f5072cdaaafd775ce6b586c3c704b06458e
Status: Downloaded newer image for redis:latest
docker.io/library/redis:latest
```



![image-20240107092237299](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240107092237299.png)

#### commit镜像

```bash
docker commit # 提交容器成为一个新的副本

docker commit -m="commit描述信息" -a="作者" 容器id 目标镜像名:[TAG]
```

##### 测试

```bash
1. 启动一个nginx容器，并启动

2. 拷贝一份前端应用到容器中，并用Nginx设置访问

3. commit，成为一份新的镜像（上述步骤更合理的使用dockerfile来操作）
```



## 数据卷

对于某些容器，可能需要将其中的数据持久化到宿主机上去（比如mysql、redis等）。为此docker提高了数据卷的方式，使得宿主机和容器间可以同步数据、甚至是容器和容器间的数据同步。

个人理解数据卷：把容器上的某个目录挂载到宿主机上去（双向绑定）



#### 卷的命令

```bash
# 卷命令cli
docker volume
docker volume ls # 查看所有的卷

# 利用inspect查看某个容器的挂载目录
docker inspect 容器id
```

#### 卷的创建

```bash
# 匿名卷，以nginx为例
docker run --name test-nginx -d -p 10001:80 -v /etc/nginx nginx # 仅指定容器的挂载路径

docker inspect 容器id # Mount字段指定了容器卷的挂载路径
"Mounts": [
    {
        "Type": "volume",
        "Name": "689c141de3b71a90a7c8229201221c4ddd57252b8c9a813146f6c136ebe044ad",
        "Source": "/var/lib/docker/volumes/689c141de3b71a90a7c8229201221c4ddd57252b8c9a813146f6c136ebe044ad/_data",
        "Destination": "/etc/nginx",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
],

# 具名挂载
docker run --name test-nginx -d -p 10002:80 -v juming-nginx:/etc/nginx nginx # :后是挂载的容器路径，而冒号前的是卷的名字
docker inspect juming-nginx # 查看卷的详情
[
    {
        "CreatedAt": "2024-01-07T05:26:17Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]

# 指定宿主路径挂载
docker run -d -p 10003:80 --name test-nginx3 -v /host/xxx:/etc/nginx nginx # 指定宿主机路径

## 拓展
## 容器路径后面跟:ro或者跟:rw
juming-nginx:/etc/nginx:ro # 容器内无法操作这个路径，readonly
juming-nginx:/etc/nginx:rw # 容器内可以操作这个路径，readwrite

```

#### dockerfile里的数据卷

```
# 下面是个dockerfile

FROM centos

# 匿名挂载，本地路径volum1，volume2
VOLUME ["volume1", "volume2"]

```

构建镜像并运行后，inspect查看mount：
```BASH
"Mounts": [
        {
            "Type": "volume",
            "Name": "e7fc785b982b18f398a9a57894a4eec76500cc6506588aabf38dceb53f6d3922",
            "Source": "/var/lib/docker/volumes/e7fc785b982b18f398a9a57894a4eec76500cc6506588aabf38dceb53f6d3922/_data",
            "Destination": "volume1", # HERE!
            "Driver": "local",
            "Mode": "",
            "RW": true,
            "Propagation": ""
        },
        {
            "Type": "volume",
            "Name": "788ef75c30ae77b29acb685fdcd3dbf31e3caac329c517178f8b5c3cc61f6303",
            "Source": "/var/lib/docker/volumes/788ef75c30ae77b29acb685fdcd3dbf31e3caac329c517178f8b5c3cc61f6303/_data",
            "Destination": "volume2", # HERE!
            "Driver": "local",
            "Mode": "",
            "RW": true,
            "Propagation": ""
        }
    ],
```



### 数据卷容器

被挂载的父容器，成为数据卷容器



```bash
FROM centos
# 两个匿名卷
VOLUME ["volume01", "volume02"]


CMD /bin/bash

```

基于上面的dockerfile制作一个测试镜像

然后先跑起来第一个容器：
```BASH
docker run -it --name docker01  test:1.0

# 第一个容器已有有了volume1和volume02
[root@9bfc5ffdd061 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01  volume02
[root@9bfc5ffdd061 /]# exit
exit

# 然后利用--volumes-from指定docker01为数据卷
docker run -it --name docker02 --volumes-from docker01  test:1.0

# 进入docker02的volume01，创建一个文件，然后回到docker01的volume1，里面也有！
# 然后把docker01删了，再回到docker02里，可以看到创建的文件还存在！
```



**结论**

docker的卷机制：

![image-20240107140145210](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240107140145210.png)

卷的生命周期独立于使用它的容器，这意味着卷可以在容器删除后继续存在，除非你显式删除该卷。



## dockerfile

dockerfile用于构建镜像

1. 编写dockerfile文件
2. build 构建镜像
3. run 运行镜像
4. push 发送镜像，供别人使用



### dockerfile文件的编写

1. 每个内置关键字大写
2. 从上到下编写指令
3. 每一层都是一层commit
4. #写注释

下图解释了dockerfile各个命令的阶段：
![image-20240107141550661](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240107141550661.png)



```BASH
FROM             # 基础镜像，一切从这开始
MAINTAINER       # 维护者信息
WORKDIR          # 镜像的工作目录，后续cmd，run的命令都在此命令下，包含exec -it进入容器内部

RUN              # 构建时需要执行的命令
ADD              # 往里面加东西
COPY             # 拷贝文件到镜像中


VOLUME           # 挂载卷
EXPOSE           # 指定容器暴露端口
CMD              # 容器运行时的指令，但只有最后一个会生效
ENTRYPOINT       # 和CMD一致，可以追加命令、参数
ENV              # 设置环境变量

```



下面写了个一个ubuntu的镜像：
```BASH
FROM ubuntu:latest
LABEL author=xxx
LABEL email=xxx@gmail.com
# 环境变量
ENV USERDIR /usr/local
# 工作目录
WORKDIR $USERDIR

# 更新软件包列表
RUN apt-get update

# 安装 vim
RUN apt-get install -y vim
RUN apt-ge install net-tools

EXPOSE 80

CMD echo $USERDIR
CMD ehco "----end----"
# 运行容器后直接进入teminal
CMD /bin/bash

# 构建
docker build -t 文件 -t tag . # .不能漏了！

docker run -it 2c6b85f44230 
root@e7d64f093a0a:/usr/local # 可以看到，一进去就在 /usr/local下
```

可以使用docker history 查看某个镜像的构建历史：

```bash
docker history 镜像id

docker history c48d6d7dd0
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
c48d6d7dd03a   2 months ago   nginx -g daemon off;                            3.52kB    test
295c7be07902   4 years ago    /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B        
<missing>      4 years ago    /bin/sh -c #(nop)  STOPSIGNAL SIGTERM           0B        
<missing>      4 years ago    /bin/sh -c #(nop)  EXPOSE 80                    0B        
<missing>      4 years ago    /bin/sh -c ln -sf /dev/stdout /var/log/nginx…   22B       
<missing>      4 years ago    /bin/sh -c set -x  && apt-get update  && apt…   53.8MB    
<missing>      4 years ago    /bin/sh -c #(nop)  ENV NJS_VERSION=1.14.2.0.…   0B        
<missing>      4 years ago    /bin/sh -c #(nop)  ENV NGINX_VERSION=1.14.2-…   0B        
<missing>      4 years ago    /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B        
<missing>      4 years ago    /bin/sh -c #(nop)  CMD ["bash"]                 0B        
<missing>      4 years ago    /bin/sh -c #(nop) ADD file:4fc310c0cb879c876…   55.3MB  
```

### CMD和ENTRYPOINT的区别

上面的dockerfile已经可以看到，CMD只有最后一个才会被执行

下面的dockerfile：
```BASH
FROM ubuntu:latest

CMD ['ls', '-a']

# 构建镜像
docker build -f xxx -t test01 .

# 运行时希望添加一点参数
 docker run test01 -f
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "-f": executable file not found in $PATH: unknown.
ERRO[0000] error waiting for container: context canceled 

# 改用entrypoint
FROM ubuntu:latest
ENTRYPOINT ['ls', '-a']

# 构建
docker build -f xxx -t test2 .
docker run xxx -f # 成功啦！

```

通常而言，docker run后面可以再跟参数的，是用ENTRYPOINT定义的



## docker网络

Docker0，默认的linux系统下，安装了docker后，docker会注册一个Docker0的网卡（docker容器的路由器）：

![image-20240107175940263](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240107175940263.png)

测试：

```BASH
## 两个容器id 40f49d391ff和8e23050087c8
docker inspect 分别查看

 "Networks": {
    "bridge": {
        "IPAMConfig": null,
        "Links": null,
        "Aliases": null,
        "NetworkID": "5b8b10698fd98d5f772c64871a853e5cf3e5f3a0f1bddb7aaba3d2d3b3053ba5",
        "EndpointID": "257c2042bbc872951ab80adeb5ac795eca78b52cbaa79d17f7bbf9c8e8417b1d",
        "Gateway": "172.17.0.1",
        "IPAddress": "172.17.0.8",
        "IPPrefixLen": 16,
        "IPv6Gateway": "",
        "GlobalIPv6Address": "",
        "GlobalIPv6PrefixLen": 0,
        "MacAddress": "02:42:ac:11:00:08",
        "DriverOpts": null
    }
}

"Networks": {
    "bridge": {
        "IPAMConfig": null,
        "Links": null,
        "Aliases": null,
        "NetworkID": "5b8b10698fd98d5f772c64871a853e5cf3e5f3a0f1bddb7aaba3d2d3b3053ba5",
        "EndpointID": "acbfa54c73d6946ac1b7b1bb4096a6cc32c7f27274642996430670e5bdef3bc0",
        "Gateway": "172.17.0.1",
        "IPAddress": "172.17.0.7",
        "IPPrefixLen": 16,
        "IPv6Gateway": "",
        "GlobalIPv6Address": "",
        "GlobalIPv6PrefixLen": 0,
        "MacAddress": "02:42:ac:11:00:07",
        "DriverOpts": null
    }
}

可以看到在同一个网关下，子网掩码相同16位，用Curl也能搞通：
docker exec -it tomcat1 curl 172.17.0.8:8080
<!doctype html><html lang="en"><head><title>HTTP Status 404 – Not Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 – Not Found</h1><hr class="line" /><p><b>Type</b> Status Report</p><p><b>Description</b> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.</p><hr class="line" /><h3>Apache Tomcat/10.1.17</h3></body></html>timott
```



#### --link

在启动容器时，可以通过 --link xxx容器，让新的容器单向连接到 xxx容器，从而直接通过容器名称访问：

```BASH
docker network --help # 所有网络相关的命令

## 真相！
# 运行tomcat3，且单向建立tomcat2的映射(tomcat2不能直接call容器名到tomcat3)
docker run -d --name tomcat3 --link tomcat2 tomcat
b5844df05b8d18e5db7ab4c551149346a3286fe36d706adb8d76df62d80550a3

docker exec -it tomcat3 curl tomcat2
curl: (7) Failed to connect to tomcat2 port 80 after 0 ms: Connection refused

# curl通了
docker exec -it tomcat3 curl tomcat2:8080
<!doctype html><html lang="en"><head><title>HTTP Status 404 – Not Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 – Not Found</h1><hr class="line" /><p><b>Type</b> Status Report</p><p><b>Description</b> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.</p><hr class="line" /><h3>Apache Tomcat/10.1.17</h3></body></html>

# 查看tomcat3容器的dns配置，发现直接把"tomcat2"的域名解析到了tomcat2的ip地址
## 真相！！
docker exec -it tomcat3 cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.8      tomcat2 8e23050087c8
172.17.0.9      b5844df05b8d
```



### 自定义网络

docker的网络模式

- bridge，默认的，创建容器不指定就这个模式，走Docker0
- host：和宿主机共享网络

```bash
## 创建一个子网范围是192.168.1.0/24的子网，网关是192.168.1.1，名称是my-network
docker network create --driver bridge --gateway 192.168.1.1 --subnet 192.168.1.0/24 my-network
601254890f06c8ee49f5cf6eca7d8bf1aa4d0e5c10ccb562a9ebadee57486d00

# 创建两个tomcat容器，处于my-network网络下
docker run -d --name tomcat01 --net my-network tomcat
ef70d1ff1ea984a2bef4438e3bd45733e5b99ef3122b31fa57bedffaa580035b

docker run -d --name tomcat02 --net my-network tomcat
1249ffd78c4d5806df408f978666558912869018995c9653d4f10362447a50fd

docker network inspect 601254890f06 ## 可以看到，创建的两个tomcat镜像在my-network网络下
[
    {
        "Name": "my-network",
        "Id": "601254890f06c8ee49f5cf6eca7d8bf1aa4d0e5c10ccb562a9ebadee57486d00",
        "Created": "2024-01-07T10:34:51.085616327Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.1.0/24",
                    "Gateway": "192.168.1.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "1249ffd78c4d5806df408f978666558912869018995c9653d4f10362447a50fd": {
                "Name": "tomcat02",
                "EndpointID": "ccf107c73a3fa32fd0cb1f6ae5c4f0f836d4d2a76c05d6f8e168f815b29fca0a",
                "MacAddress": "02:42:c0:a8:01:03",
                "IPv4Address": "192.168.1.3/24",
                "IPv6Address": ""
            },
            "ef70d1ff1ea984a2bef4438e3bd45733e5b99ef3122b31fa57bedffaa580035b": {
                "Name": "tomcat01",
                "EndpointID": "f452d65b45872251ffc894abdc4bbb1e6c3cd7c7f083ba410c13b135dfe2de09",
                "MacAddress": "02:42:c0:a8:01:02",
                "IPv4Address": "192.168.1.2/24",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

# 自己创建的网络，可以直接用容器名作为域名去调用：
docker exec -it tomcat01 curl tomcat02:8080
<!doctype html><html lang="en"><head><title>HTTP Status 404 – Not Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 – Not Found</h1><hr class="line" /><p><b>Type</b> Status Report</p><p><b>Description</b> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.</p><hr class="line" /><h3>Apache Tomcat/10.1.17</h3></body></html>timott
```

#### 网络互联

![image-20240107184201567](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240107184201567.png)

```bash
# 测试：
# 走默认的Docker0子网
docker run -d --name tomcat03 tomcat

# 把tomcat03连接到my-network网络，其实就是在my-network里的公网ip
docker network connect tomcat03 my-network

docker network inspect 601254890f06
[
    {
        "Name": "my-network",
        "Id": "601254890f06c8ee49f5cf6eca7d8bf1aa4d0e5c10ccb562a9ebadee57486d00",
        "Created": "2024-01-07T10:34:51.085616327Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.1.0/24",
                    "Gateway": "192.168.1.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "1249ffd78c4d5806df408f978666558912869018995c9653d4f10362447a50fd": {
                "Name": "tomcat02",
                "EndpointID": "ccf107c73a3fa32fd0cb1f6ae5c4f0f836d4d2a76c05d6f8e168f815b29fca0a",
                "MacAddress": "02:42:c0:a8:01:03",
                "IPv4Address": "192.168.1.3/24",
                "IPv6Address": ""
            },
            "7a6a4d40d2e37ccd56a23f2b7b8c6021f2a3b47cce00ae704b306ce3337ddf28": {
                "Name": "tomcat03",
                "EndpointID": "172567b051cfa187c7654ece42b9c915d65ad543f4d23719771cbd9f58f7cc8f",
                "MacAddress": "02:42:c0:a8:01:04",
                "IPv4Address": "192.168.1.4/24",
                "IPv6Address": ""
            },
            "ef70d1ff1ea984a2bef4438e3bd45733e5b99ef3122b31fa57bedffaa580035b": {
                "Name": "tomcat01",
                "EndpointID": "f452d65b45872251ffc894abdc4bbb1e6c3cd7c7f083ba410c13b135dfe2de09",
                "MacAddress": "02:42:c0:a8:01:02",
                "IPv4Address": "192.168.1.2/24",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

## curl测试
# tomcat03 到 tomcat02通了
docker exec -it tomcat03 curl tomcat02:8080
<!doctype html><html lang="en"><head><title>HTTP Status 404 – Not Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 – Not Found</h1><hr class="line" /><p><b>Type</b> Status Report</p><p><b>Description</b> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.</p><hr class="line" /><h3>Apache Tomcat/10.1.17</h3></body>

## tomcat02 到 tomcat03通了
docker exec -it tomcat02 curl tomcat03:8080
<!doctype html><html lang="en"><head><title>HTTP Status 404 – Not Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 – Not Found</h1><hr class="line" /><p><b>Type</b> Status Report</p><p><b>Description</b> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.</p><hr class="line" /><h3>Apache Tomcat/10.1.17</h3></body></html>

# 再在Docker0里跑一个tomcat04
docker run -d --name tomcat04 tomcat

# tomcat02 call 不同 tomcat04
docker exec -it tomcat04 curl tomcat04:8080
Error: No such container: tomcat04
```

