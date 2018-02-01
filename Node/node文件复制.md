### path.join 与 path.resolve 的区别

`path.join()` 方法使用平台特定的分隔符把全部给定的 path 片段连接到一起，并规范化生成的路径。

长度为零的 path 片段会被忽略。 如果连接后的路径字符串是一个长度为零的字符串，则返回 '.'，表示当前工作目录。

```
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..');
// 返回: '/foo/bar/baz/asdf'

path.join('foo', {}, 'bar');
// 抛出 'TypeError: Path must be a string. Received {}'

```

`path.resolve()` 方法会把一个路径或路径片段的序列解析为一个绝对路径。

给定的路径的序列是从右往左被处理的，后面每个 path 被依次解析，直到构造完成一个绝对路径。 例如，给定的路径片段的序列为：/foo、/bar、baz，则调用 path.resolve('/foo', '/bar', 'baz') 会返回 /bar/baz。

如果处理完全部给定的 path 片段后还未生成一个绝对路径，则当前工作目录会被用上。

生成的路径是规范化后的，且末尾的斜杠会被删除，除非路径被解析为根目录。

长度为零的 path 片段会被忽略。

如果没有传入 path 片段，则 `path.resolve()` 会返回当前工作目录的绝对路径。

如果任何参数不是一个字符串，则抛出 TypeError。

```
path.resolve('/foo/bar', './baz');
// 返回: '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/');
// 返回: '/tmp/file'

path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif');
// 如果当前工作目录为 /home/myself/node，
// 则返回 '/home/myself/node/wwwroot/static_files/gif/image.gif'
```

### __dirname，__filename，process.cwd()和'./'

```
__dirname： 获得当前执行文件所在目录的完整目录名
__filename： 获得当前执行文件的带有完整绝对路径的文件名
process.cwd()：获得当前执行node命令时候的文件夹目录名
./： 不使用require时候，./与process.cwd()一样，使用require时候，与__dirname一样
```

只有在 require() 时才使用相对路径(./, ../)的写法，其他地方一律使用绝对路径，如下：
```
// 当前目录下
 path.dirname(__filename) + '/path.js'; 
// 相邻目录下
 path.resolve(__dirname, '../regx/regx.js');
```
[fs路径](https://www.jianshu.com/p/aeb3d4318d07)

### 文件复制
小文件复制方式，一次性把所有文件内容都读取到内存中后再一次性写入磁盘：
```
const fs = require('fs');

function copy(src, dst) {
  fs.writeFileSync(dst, fs.readFileSync(src));
}
```

大文件复制方式，读一点写一点：
```
const fs = rquire('fs')

function copy(src, dst) {
  fs.createReadStream(src).pipe(fs.createWriteStream(dst))
}
```

复制目录：
```
const fs=require('fs');
const stat=fs.stat;

function copy (src,dst){
    //读取目录
    fs.readdir(src,function(err,paths){
        console.log(paths)
        if(err){
            throw err;
        }
        paths.forEach(function(path){
            var _src=src+'/'+path;
            var _dst=dst+'/'+path;
            var readable;
            var writable;
            stat(_src,function(err,st){
                if(err){
                    throw err;
                }
                
                if(st.isFile()){
                    readable=fs.createReadStream(_src);//创建读取流
                    writable=fs.createWriteStream(_dst);//创建写入流
                    readable.pipe(writable);
                }else if(st.isDirectory()){
                    exists(_src,_dst,copy);
                }
            });
        });
    });
}

function exists (src,dst,callback){
    //判断某个路径下文件是否存在
    fs.exists(dst,function(exists){
        if(exists){//不存在
            callback(src,dst);
        }else{//存在
            fs.mkdir(dst,function(){//创建目录
                callback(src,dst)
            })
        }
    })
}

// 具体使用
copy('/fromAa','/toBb')
```