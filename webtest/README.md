# web 开放服务配置服务

## 1.docker 基本操作

```bash
# 启动镜像
# --net=host:配置docker container使用主机网络和端口
docker run -t -i --net=host image_name
# 带cpu限制启动镜像
docker run -t -i --net=host --cpu-period=50000 --cpu-quota=5000 --cpuset-cpus 1 image_name
# 保存更改
docker commit --author "liusicong" --message "new_message" container_id ubuntu:new_image_name

# 导出镜像
# 1.查看container id
docker ps -a
# 2.导出镜像
docker export container_id > filename.tar

# 导入镜像
docker import - image_name < filename.tar
```

## 2.DNS 服务

默认 docker 镜像与主机共享**dns:53**端口，所以不用再改变什么。

```bash
# 进入docker镜像后，启动dns服务
systemctl start bind9

# 查看dns服务状态
systemctl status bind9

# 修改bind9 named配置文件
vim /etc/bind/named.conf.local

# 修改bind9 映射配置文件
vim /etc/bind/xxxx

# 修改完重启dns服务
systemctl restart bind9

#　设置开机自启服务
systemctl enable bind9
```

## 3.HTTP 代理

默认 docker 镜像与主机共享**tinyproxy:8888**端口，所以也不用再改变什么。

```bash
# 进入docker镜像后，启动tinyproxy服务
systemctl start tinyproxy

# 查看tinyproxy服务状态
systemctl status tinyproxy

# 修改tinyproxy配置文件
vim /etc/tinyproxy/tinyproxy.conf

# 修改完tinyproxy配置文件后，重启服务
systemctl restart tinyproxy

#　设置开机自启服务
systemctl enable tinyproxy
```

请求的 http 服务在 56 服务器上，一个简单的 Flask web 服务，返回请求的 ip 地址。

```powershell
# 进入文件夹
cd C:\Users\lsc\Desktop\httpServer
# 启动flask服务
C:\Users\lsc\develop\python\python app.py
```

## 4.NTP 服务

默认 docker 镜像与主机共享**ntp:133**端口，所以也不用再改变什么。

```bash
# 进入docker镜像后，启动ntp服务
systemctl start ntp

# 查看ntp服务状态
systemctl status ntp

# 修改ntp配置文件
vim /etc/ntp.conf

# 修改完ntp配置文件后，重启服务
systemctl restart ntp

# 设置开机自启服务
systemctl enable ntp
```

## 5.限制 docker 容器的硬件属性

### 5.1 限制 docker 使用 CPU

这里只是简单的限制 docker 可用的 CPU 核心数：

```bash
# 容器最多可以使用主机上两个CPU，除此之外，还可以指定如1.5之类的小数
docker run -it --rm --cpus=2 image_name

# 表示容器中的进程可以在CPU-1和CPU-3上执行
docker run -it --cpuset-cpus="1,3" image_name

# 表示容器中的进程可以在 CPU-0、CPU-1 及 CPU-2 上执行。
docker run -it --cpuset-cpus="0-2" image_name
```

### 5.2 限制 docker 使用内存

与操作系统类似，docker container 可以使用的内存包括两部分：**物理内存**和**Swap**。
Docker 通过下面两组参数来控制容器内存的使用量。

- -m 或 --memory：设置内存的使用限额，例如：100MB，2GB。
- --memory-swap：设置内存+swap 的使用限额。

默认情况下，上面两组参数为-1，即对容器内存和 swap 的使用没有限制。如果在启动容器时，只指定-m 而不指定--memory-swap， 那么--memory-swap 默认为-m 的两倍。

```bash
# 允许该容器最多使用200MB的内存和100MB的swap。
docker run -m 200M --memory-swap=100M image_name

# 容器最多使用200MB的内存和400MB的swap
docker run -m 200M  image_name
```

### ~~5.3 限制 docker 使用磁盘~~

### ~~5.4 限制 docker 使用 GPU~~

### 5.5 限制 docker 带宽

因为 docker 启动使用的是**host**模式：_--net=host_,所以限制宿主机的网络带宽就达到了限制 docker 容器带宽的目的，使用**_Wondershaper_** 来对指定网口带宽进行限制。62 服务器的主网卡名称为：**_eno1_**。

```bash
# 1.安装
sudo apt-get install wondershaper

# 2.查找网口信息
ifconfig

# 3.使用命令限制网速
# 确定网卡名称以后，按照以下命令限制网络带宽：
# -a：网卡名称;
sudo wondershaper <adapter> <下行rate> <上行rate>

# 举例，把下行、上行速率分别限制为100Kbps和100Kbps
sudo wondershaper  eno1 100 100

# 取消限速
sudo wondershaper clear <adapter>

# 网速测试
speedtest-cli
```

## 6. scapy 发送数据包

*52 服务器*运行程序命令

```bash
sudo ~/anaconda3/bin/python main.py
```
