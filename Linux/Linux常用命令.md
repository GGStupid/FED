### 文件复制命令cp

``` bash
命令格式：cp [-adfilprsu] source destination

参数说明：
    -a:是指archive的意思，也说是指复制所有的目录
    -d:若源文件为连接文件(link file)，则复制连接文件属性而非文件本身
    -f:强制(force)，若有重复或其它疑问时，不会询问用户，而强制复制
    -i:若目标文件(destination)已存在，在覆盖时会先询问是否真的操作
    -l:建立硬连接(hard link)的连接文件，而非复制文件本身 
    -p:与文件的属性一起复制，而非使用默认属性
    -r:递归复制，用于目录的复制操作
    -s:复制成符号连接文件(symbolic link)，即“快捷方式”文件
    -u:若目标文件比源文件旧，更新目标文件
    如将/test1目录下的file1复制到/test3目录，并将文件名改为file2,可输入以下命令：
    cp /test1/file1 /test3/file2
```

### CentOS查看和修改PATH环境变量的方法
查看PATH：echo $PATH

以添加mongodb server为列

修改方法一：

export PATH=/usr/local/mongodb/bin:$PATH //配置完后可以通过echo $PATH查看配置结果。

生效方法：立即生效

有效期限：临时改变，只能在当前的终端窗口中有效，当前窗口关闭后就会恢复原有的path配置

用户局限：仅对当前用户

修改方法二：

通过修改.bashrc文件:

vim ~/.bashrc 

//在最后一行添上：
export PATH=/usr/local/mongodb/bin:$PATH

生效方法：（有以下两种）

1、关闭当前终端窗口，重新打开一个新终端窗口就能生效

2、输入“source ~/.bashrc”命令，立即生效

有效期限：永久有效

用户局限：仅对当前用户

修改方法三:

通过修改profile文件:
vim /etc/profile

/export PATH //找到设置PATH的行，添加
export PATH=/usr/local/mongodb/bin:$PATH

生效方法：系统重启

有效期限：永久有效

用户局限：对所有用户

修改方法四:

通过修改environment文件:

vim /etc/environment
在PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"中加入“:/usr/local/mongodb/bin”

生效方法：系统重启

有效期限：永久有效
用户局限：对所有用户

### 查看端口和进程

``` bash
# 端口是否占用
lsof -i :27017

# 进程
ps -ef | grep mongod

```