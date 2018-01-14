### TCP优化
```
http {
    sendfile           on;
    tcp_nopush         on;
    tcp_nodelay        on;

    keepalive_timeout  60;
    ... ...
}
```
第一行的 sendfile 配置可以提高 Nginx 静态资源托管效率。sendfile 是一个系统调用，直接在内核空间完成文件发送，不需要先 read 再 write，没有上下文切换开销。

TCP_NOPUSH 是 FreeBSD 的一个 socket 选项，对应 Linux 的 TCP_CORK，Nginx 里统一用 tcp_nopush 来控制它，并且只有在启用了 sendfile 之后才生效。启用它之后，数据包会累计到一定大小之后才会发送，减小了额外开销，提高网络效率。

TCP_NODELAY 也是一个 socket 选项，启用后会禁用 Nagle 算法，尽快发送数据，某些情况下可以节约 200ms（Nagle 算法原理是：在发出去的数据还未被确认之前，新生成的小数据先存起来，凑满一个 MSS 或者等到收到确认后再发送）。Nginx 只会针对处于 keep-alive 状态的 TCP 连接才会启用 tcp_nodelay。

可以看到 TCP_NOPUSH 是要等数据包累积到一定大小才发送，TCP_NODELAY 是要尽快发送，二者相互矛盾。实际上，它们确实可以一起用，最终的效果是先填满包，再尽快发送。

配置最后一行用来指定服务端为每个 TCP 连接最多可以保持多长时间。Nginx 的默认值是 75 秒，有些浏览器最多只保持 60 秒，所以我统一设置为 60。

另外，还有一个 TCP 优化策略叫 TCP Fast Open（TFO），这里先介绍下，配置在后面贴出。TFO 的作用是用来优化 TCP 握手过程。客户端第一次建立连接还是要走三次握手，所不同的是客户端在第一个 SYN 会设置一个 Fast Open 标识，服务端会生成 Fast Open Cookie 并放在 SYN-ACK 里，然后客户端就可以把这个 Cookie 存起来供之后的 SYN 用。

### 开启 Gzip
```
http {
    gzip               on;
    gzip_vary          on;

    gzip_comp_level    6;
    gzip_buffers       16 8k;

    gzip_min_length    1000;
    gzip_proxied       any;
    gzip_disable       "msie6";

    gzip_http_version  1.0;

    gzip_types         text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript application/javascript;
    ... ...
}
```
gzip_vary 用来输出 Vary 响应头，用来解决某些缓存服务的一个问题。

gzip_disable 指令接受一个正则表达式，当请求头中的 UserAgent 字段满足这个正则时，响应不会启用 GZip，这是为了解决在某些浏览器启用 GZip 带来的问题。特别地，指令值 msie6 等价于 MSIE [4-6]\.，但性能更好一些。另外，Nginx 0.8.11 后，msie6 并不会匹配 UA 包含 SV1 的 IE6（例如 Windows XP SP2 上的 IE6），因为这个版本的 IE6 已经修复了关于 GZip 的若干 Bug。

默认 Nginx 只会针对 HTTP/1.1 及以上的请求才会启用 GZip，因为部分早期的 HTTP/1.0 客户端在处理 GZip 时有 Bug。现在基本上可以忽略这种情况，于是可以指定 gzip_http_version 1.0 来针对 HTTP/1.0 及以上的请求开启 GZip。

### 开启缓存
优化代码逻辑的极限是移除所有逻辑；优化请求的极限是不发送任何请求。这两点通过缓存都可以实现。
#### 客户端
服务端在输出响应时，可以通过响应头输出一些与缓存有关的信息，从而达到少发或不发请求的目的。HTTP/1.1 的缓存机制稍微有点复杂，这里简单介绍下：

首先，服务端可以通过响应头里的 Last-Modified（最后修改时间） 或者 ETag（内容特征） 标记实体。浏览器会存下这些标记，并在下次请求时带上 If-Modified-Since: 上次 Last-Modified 的内容 或 If-None-Match: 上次 ETag 的内容，询问服务端资源是否过期。如果服务端发现并没有过期，直接返回一个状态码为 304、正文为空的响应，告知浏览器使用本地缓存；如果资源有更新，服务端返回状态码 200、新的 Last-Modified、Etag 和正文。这个过程被称之为 HTTP 的协商缓存，通常也叫做弱缓存。

另外一种缓存机制是服务端通过响应头告诉浏览器，在什么时间之前（Expires）或在多长时间之内（Cache-Control: Max-age=xxx），不要再请求服务器了。这个机制我们通常称之为 HTTP 的强缓存。

一旦资源命中强缓存规则后，再次访问完全没有 HTTP 请求（Chrome 开发者工具的 Network 面板依然会显示请求，但是会注明 from cache；Firefox 的 firebug 也类似，会注明 BFCache），这会大幅提升性能。所以我们一般会对 CSS、JS、图片等资源使用强缓存，而入口文件（HTML）一般使用协商缓存或不缓存，这样可以通过修改入口文件中对强缓存资源的引入 URL 来达到即时更新的目的。

这里也解释下为什么有了 Expires，还要有 Cache-Control。也有两个原因：1）Cache-Control 功能更强大，对缓存的控制能力更强；2）Cache-Control 采用的 max-age 是相对时间，不受服务端 / 客户端时间不对的影响。

另外关于浏览器的刷新（F5 / cmd + r）和强刷（Ctrl + F5 / shift + cmd +r）：普通刷新会使用协商缓存，忽略强缓存；强刷会忽略浏览器所有缓存（并且请求头会携带 Cache-Control:no-cache 和 Pragma:no-cache，用来通知所有中间节点忽略缓存）。只有从地址栏或收藏夹输入网址、点击链接等情况下，浏览器才会使用强缓存。