### 环境版本

Centos:7 x64;

MongoDB:v3.6.3

### 下载

``` bash
curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.6.3.tgz
```

### 解压
``` bash
tar -zxvf mongodb-linux-x86_64-3.6.3.tgz
```
### 重命名当当前目录并以版本号结尾
```
mv mongodb-linux-x86_64-3.6.3 ./mongodb3.6.3
```

### 导出全局变量
```
vim vim ~/.bashrc
// 添加路径
 export PATH=<mongodb-install-directory>/bin:$PATH
// 然后
source ~/.bashrc
```
### 新建数据存放目录及日志目录
```
mkdir -p /data/db
mkdir -p /data/log
```

### 修改mongodb.conf
```
vim /data/mongodb.conf

-------------------
# 数据文件存放目录
dbpath = /root/mongodb/data/db
# 日志文件存放目录
logpath = /root/mongodb/data/mongodblog/mongodb.log
# 日志输出方式，使用追加方式
logappend = true
# mongodb默认是绑定到本地端口:127.0.0.0:27017，会限制远程连接,修改成0.0.0.0,这个ip相当于本机所有ip地址(不安全,测试时使用）
bind_ip = 0.0.0.0
# 端口
port = 27017
# 以守护程序方式启动,即在后台运行
fork = true

```

### 启动mongodb

```
mongod -f /data/mongodb.conf
```

### mongodb.conf配置详情

[mongodb.conf](https://blog.csdn.net/fdipzone/article/details/7442162)