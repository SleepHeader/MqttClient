#CenterOs 环境下Service搭建

##安装工具
yum install gcc gcc-c++

yum install openssl-devel

yum install c-ares-devel

yum install libuuid-devel

yum install wget

yum install cmake

yum install build-essential python quilt devscripts python-setuptools python3 

yum install libssl-dev libc-ares-dev uuid-dev daemon openssl-devel

##下载并编译安装libwebsockets
wget https://libwebsockets.org/git/libwebsockets/snapshot/libwebsockets-2.0.2.tar.gz

tar zxvf libwebsockets-2.0.2.tar.gz

cd libwebsockets-2.0.2

mkdir build

cd build

cmake .. -DLIB_SUFFIX=64

make install

ldconfig

##下载并编译安装mosquitto
wget http://mosquitto.org/files/source/mosquitto-1.4.9.tar.gz

tar -xzvf mosquitto-1.4.9.tar.gz

cd mosquitto-1.4.9

更改configure.mk中

WITH_WEBSOCKETS:=no

为

WITH_WEBSOCKETS:=yes

make

make install

cp mosquitto.conf /etc/mosquitto

##修改mosquitto.conf文件
在`/etc/mosquitto/mosquitto.conf`的`Default Listener`一节添加如下几行：

```
pid_file /var/run/mosquitto.pid
user root
port 1883
max_connections -1
allow_anonymous true

```

##运行mosquitto
mosquitto -c /etc/mosquitto/mosquitto.conf

或mosquitto -c /etc/mosquitto/mosquitto.conf -v

或mosquitto -c /etc/mosquitto/mosquitto.conf -d

##本机测试mosquitto
在服务器上新建两个shell，A和B

A 订阅主题：`mosquitto_sub -t topic`

B 推送消息：`mosquitto_pub -t topic -h localhost -m "topicA test"`


##可能出现的错误
* `mosquitto_sub: error while loading shared libraries: libmosquitto.so.1: cannot open shared object file: No such file or directory`

```
解决方案：
cp ./lib/libmosquitto.so.1 /usr/local/lib
sudo ln -s /usr/local/lib/libmosquitto.so.1 /usr/lib/libmosquitto.so.1
ldconfig
```

