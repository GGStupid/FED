### 原因
每次写博客写文章，截个图片然后想上传到七牛，都需要手动去七牛去上传，觉得特别麻烦，所以打算写个图片上传的接口，正好这个写文章的[makdown组件](https://github.com/hinesboy/mavonEditor)本身就有上传图片的api,~~!虽然这个api不是很好用，以后再重新换个markdown组件

### 实践
从request获取文件流，上传到七牛的服务器，还是直接上代码吧。

首先在`config`下修改`config.default.js`内容，增加七牛上传相关的配置：
```
config.qn_config={
    accessKey: '你的Ak密钥',
    secretKey: '你的SK密钥',
    bucket: '要上传的空间',
    origin: '图片访问的域名地址',
}
```

在`service`下新建`upload.js`文件，内容如下
```
'use strict';

const Service = require('egg').Service;
const qiniu = require('qiniu');

class UpLoadService extends Service {
  /*
  *七牛上传
  *@param {Stream} readableStream 流
  *@param {String} key 文件名
   */
  qnUpload(readableStream, key) {
    const { accessKey, secretKey, bucket } = this.config.qn_config;
    const mac = new qiniu.auth.digest.Mac(accessKey, secretKey);
    const putPolicy = new qiniu.rs.PutPolicy({ scope: bucket });
    const uploadToken = putPolicy.uploadToken(mac);

    const config = new qiniu.conf.Config();
    const formUploader = new qiniu.form_up.FormUploader(config);
    const putEatra = new qiniu.form_up.PutExtra();

    return new Promise(function(resolve, reject) {
      formUploader.putStream(uploadToken, key, readableStream, putEatra, function(respErr,
        respBody, respInfo) {
        if (respErr) {
          reject(respErr);
        }
        if (respInfo.statusCode === 200) {
          resolve(respBody);
        } else {
          reject(respInfo.statusCode, respBody);
        }
      });
    });
  }
}

module.exports = UpLoadService;
```

在`controller`下新建`upload.js`,代码如下：
```
'use strict';
const Controller = require('egg').Controller;
const path = require('path');

class UpLoadController extends Controller {
  async upLoadImg() {
    const { ctx, service, config } = this;
    const stream = await ctx.getFileStream();
    const filename = Date.now() + path.extname(stream.filename).toLocaleLowerCase();
    const resulet = await service.upload.qnUpload(stream, filename);
    const imgUrl = config.qn_config.origin + resulet.key;
    ctx.body = {
      code: 200,
      data: { imgUrl },
      msg: '成功',
      success: true,
    };
  }
}

module.exports = UpLoadController;
```
然后再`router.js`下，增加新的图片上传路由即可：
```
router.post('/egg/api/upload/file', upload.upLoadImg);
```

还有一个地方要注意下，就是`config.default.js`下，增加如下：
```
 config.security = {
    csrf: {
      enable: false,
    },
  };
```
由于`egg`安全相关的限制，在前后端分离的情况下，不加这个配置，会403通不过安全限制。

### 参考
[七牛SDK文档](https://developer.qiniu.com/kodo/sdk/1289/nodejs)

[egg官网安全](https://eggjs.org/zh-cn/core/security.html)