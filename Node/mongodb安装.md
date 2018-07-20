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


### mongodb多个`__v`字段

可以在Scheam中加入{versionKey:false}成功解决问题
``` javascript
var mySchema = new mongoose.Schema({
    username:  'string'
}, {versionKey: false}
)
```
### CRUD

### 查询条件
```
   $lt　　　　 小于

　　$lte　　　  小于等于

　　$ne            不等于

　　$in             在多个值范围内

　　$nin           不在多个值范围内

　　$all            匹配数组中多个值

　　$regex　　正则，用于模糊查询

　　$size　　　匹配数组大小

　　$maxDistance　　范围查询，距离（基于LBS）

　　$mod　　   取模运算

　　$near　　　邻域查询，查询附近的位置（基于LBS）

　　$exists　　  字段是否存在

　　$elemMatch　　匹配内数组内的元素

　　$within　　范围查询（基于LBS）

　　$box　　　 范围查询，矩形范围（基于LBS）

　　$center       范围醒询，圆形范围（基于LBS）

　　$centerSphere　　范围查询，球形范围（基于LBS）

　　$slice　　　　查询字段集合中的元素（比如从第几个之后，第N到第M个元素）
```

### populate使用
[populate使用](https://segmentfault.com/a/1190000002727265)

### mongodb.conf配置详情

[mongodb.conf](https://blog.csdn.net/fdipzone/article/details/7442162)
[各种情况下的MongoDB的启动与关闭](http://www.360doc.com/content/17/0820/19/36471792_680678706.shtml)