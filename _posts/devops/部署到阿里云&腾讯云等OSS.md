前端部署其实也算前端工程化的一部分，这篇博客是我结合实际工作经验和业余探索，总结出的前端部署的一些尝试。一方面自己有所记录，另一方面也能给大家带来些启示。

- 部署到七牛云
- 部署到阿里云OSS，部署到腾讯云OSS
- 部署到腾讯云服务器并使用nginx做反向代理
- 总结与反思

前提条件：

- 通过`npm run build / yarn run build`打包出的前端dist包，需要部署到公网环境供用户访问
- 已购买一台腾讯云服务器，实例配置1核 1GB 1Mbps。可用于存储前端dist包，nginx反向代理，运行后端服务等等
- 已购买一个可用域kaigege.cn并备案。可用于CNAME解析，二级域名解析等等

若是对上述一些名词不理解，不用着急，一点一点来。

dist包目录结构如下:

```
|-- arya-spring-vue-fe
      |-- favicon.ico
      |-- index.html
      |-- manifest.json
      |-- precache-manifest.dc33a1b9e99c64c524c43168e7777109.js
      |-- robots.txt
      |-- service-worker.js
      |-- css
      |   |-- app.8be73ceb.css
      |   |-- chunk-vendors.8aa8b174.css
      |   |-- login.8c104af7.css
      |   |-- user.2cc930ce.css
      |-- fonts
      |   |-- ionicons.99ac3308.woff
      |   |-- ionicons.d535a25a.ttf
      |-- img
      |   |-- arya-spring-vue-fe.8d6fbad1.png
      |   |-- ionicons.a2c4a261.svg
      |   |-- logo.82b9c7a5.png
      |   |-- icons
      |       |-- android-chrome-192x192.png
      |       |-- android-chrome-512x512.png
      |       |-- apple-touch-icon-120x120.png
      |       |-- apple-touch-icon-152x152.png
      |       |-- apple-touch-icon-180x180.png
      |       |-- apple-touch-icon-60x60.png
      |       |-- apple-touch-icon-76x76.png
      |       |-- apple-touch-icon.png
      |       |-- favicon-16x16.png
      |       |-- favicon-32x32.png
      |       |-- msapplication-icon-144x144.png
      |       |-- mstile-150x150.png
      |       |-- safari-pinned-tab.svg
      |-- js
          |-- about.b6757b1f.js
          |-- about.b6757b1f.js.map
          |-- app.15053451.js
          |-- app.15053451.js.map
          |-- chunk-vendors.acd76e03.js
          |-- chunk-vendors.acd76e03.js.map
          |-- login.e0db28e9.js
          |-- login.e0db28e9.js.map
          |-- user.4699a3d1.js
          |-- user.4699a3d1.js.map
```

### 部署到七牛云

---

因为手上负责的项目dist包是存储在七牛云的，而且第一个接触到的云存储也是七牛云，所以自然而然想到了七牛云OSS。

已具备条件：

- 七牛云账号
- 七牛云对象存储服务
- 用于存储文件的bucket

需要做的事：

- 融合CDN添加域名并绑定bucket
- 域名添加CNAME解析
- 上传文件到bucket

#### 融合CDN添加域名并绑定bucket

融合CDN->域名管理->创建域名`foo.kaigege.cn`->源站配置里选择七牛云存储绑定bucket

![image.png](https://ucc.alicdn.com/pic/developer-ecology/70160c39751c40a3b742412f14f584a1.png "image.png")

#### 域名添加CNAME解析

七牛云域名管理并复制CNAME->腾讯云域名管理->解析->添加记录->记录类型选择CNAME，主机记录填写foo->记录值填写foo-kaigege-cn.qiniudns.com

![image.png](https://ucc.alicdn.com/pic/developer-ecology/cd0e7a99906f4ca1829ecc1cf325d502.png "image.png")

#### 上传文件到bucket

这一步就很有趣了。

看一下我们的dist目录可以发现，除了index.html这一级外，还有css，fonts，img，js4个目录。

而**七牛云的bucket竟然不支持创建目录**。

唯一的办法就是[如何在空间下创建文件夹？](https://link.segmentfault.com/?enc=%2Bu5gTjFuwcGUqf3lvTf3Bw%3D%3D.Pq2zU1lDMOanaFiZoiiVm21zKqyRDqfyHFtWUXzYP%2FGilrxLGl7NubT3bGknzhPx%2FDTsV5fxsOhSPE7luBYuI71sz3emJG%2FgyAEEsL3hGNzgOX820QXyAiEJbnQipEwx)

> 在空间中不能创建文件夹，但是为了区分不同的文件，可以这么做:文件名以 2017/1/12/1.img , 即创建这样的虚拟目录 2017/1/12/ 做区分。

虽然我手上的项目就是上传七牛云，是之前的老大写了一个node脚本按照这种方式上传的，但是当用过阿里云OSS和腾讯云OSS以后，我真心觉得七牛云这个功能有待提高。代码就不贴出了，七牛云提供了各种SDK，python，nodejs等等部署脚本都能写出。

考虑到后期项目部署的方便性，果断放弃上传前端文件到七牛云的方案.

### 部署到阿里云OSS，部署到腾讯云OSS

---

部署到阿里云OSS和部署到腾讯云OSS。

二者都是对象存储，bucket内都支持创建目录，和七牛云一样都有CDN加速。

阿里云OSS我实在是找不到收费标准，腾讯云有一个1块钱6个月都免费体验权，因此我就以腾讯云为例子来讲解。

已具备条件：

- 腾讯云账号
- 腾讯云对象存储服务
- 用于存储文件的存储桶

需要做的事：

- 为存储桶添加自定义加速域名
- 域名添加CNAME解析
- 创建文件夹并上传文件

#### 为存储桶添加自定义加速域名

![image.png](https://ucc.alicdn.com/pic/developer-ecology/9af3d4e78eb74c8eb44d4ac2c6c38b5a.png "image.png")

#### 域名添加CNAME解析

域名添加CNAME解析可参考七牛云域名解析，是一样的。

#### 创建文件夹并上传文件

![image.png](https://ucc.alicdn.com/pic/developer-ecology/5a5884a624634bd3b3657875bb874643.png "image.png")

此时我们访问：foo.kaigege.cn/foo/index.html是不就可以访问到文件了呢？No。其它文件的请求路径为foo.kaigege.cn/js/app.js。缺失了foo目录。该怎么办呢？

在vue.config.js配置webpack的publicPath为"/foo"即可。  
在vue.config.js配置webpack的publicPath是“/foo”。

看一下index.html就知道了。

| 添加publicPath /foo前 添加 publicPath /foo 之前 | 添加publicPath /foo后 添加 publicPath /foo 后 |
| ---------------------------------------- | --------------------------------------- |
| `<link href=/js/app.15053451.js>`        | `<link href=/foo/js/app.15053451.js>`   |

Ok，腾讯云OSS部署前端文件完毕。

### 部署到腾讯云服务器并使用nginx做反向代理

---

为什么会想到这样部署？

- 托管再github pages的前端博客，需要一个域名做代理
- 现公司开发环境就是使用nginx构建的反向代理服务器，手上负责项目的前端文件就存储在这台机器上
- CDN加速是收费的，虽然腾讯云0.11元/G，但是免费的显然更好

于是开始折腾。

已具备条件：

- frankkai.github.io
- 腾讯云centOS服务器
- 部署nginx并初步具备nginx知识点

需要做的事

- 配置nginx访问blog.kaigege.cn重定向到frankkai.github.io  
  安置nginx blog.kaigege.cn 加权方向到达frankkai.github.io
- 配置nginx访问foo.kaigege.cn加载前端静态文件
- 腾讯云域名管理解析二级域名到服务器
- 使用fileZIla连接腾讯云服务器上传前端静态文件

### nginx配置如下

---

```nginx
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    gzip                on;
    gzip_types          text/plain application/javascript text/css;    

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
    # 配置nginx访问blog.kaigege.cn重定向到frankkai.github.io
    server {
        listen       80;
        server_name  blog.kaigege.cn;
        location / {
            proxy_pass https://frankkai.github.io; 
        }
    }
   # 配置nginx访问foo.kaigege.cn加载前端静态文件。
    server {
        listen       80;
        server_name  foo.kaigege.cn;
        location / {
            root     /usr/share/nginx/foo;
            index    index.html;
        }
    }

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _; 
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {}

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}
```

对nginx以及nginx 反向代理不熟的话，可以看[nginx入门](https://link.segmentfault.com/?enc=OT7PKMHN8zgx0EF9elyEQA%3D%3D.285O%2FraVPEsANvqIL17avflKxrhZTLz2QjdLJbBZjCOwMNSV5ZWcRmGlasC4w%2BnOrxNS85QXYq7hWAKUllbWUg%3D%3D)或[前端必会的 Nginx入门视频教程(共11集)](https://link.segmentfault.com/?enc=xySYEulvOHwz3hiDs3a2SA%3D%3D.3mVrDNxsyDxoX21Hu8nneHCXlQBJcsLzWru9MI4Z91UrTKPiN%2B5aZKhHufQewhDH)补充一下。这里就不再赘述了。

#### 腾讯云域名管理解析二级域名到服务器

![image.png](https://ucc.alicdn.com/pic/developer-ecology/9890612ebb2b46b69157ed6d4b304db4.png "image.png")

#### 使用fileZIla连接腾讯云服务器上传前端静态文件

这个坑是比较多的，需要做以下几件事。

- 放开腾讯云安全组的22端口
- 腾讯云SSH秘钥下载秘钥
- 配置fileZila新站点
- 上传前端dist目录

##### 放开腾讯云安全组的22端口

![image.png](https://ucc.alicdn.com/pic/developer-ecology/8a5231ead67c46aaa1a4cd92fd24c17e.png "image.png")

##### 腾讯云SSH秘钥下载秘钥

![image.png](https://ucc.alicdn.com/pic/developer-ecology/3ad91993d5aa4858b3484867244421b1.png "image.png")

配置fileZila新站点

添加秘钥

![image.png](https://ucc.alicdn.com/pic/developer-ecology/cf5b1b7aa8ef47769235b4a899f8324c.png "image.png")

###### 新增站点配置

需要选择SFTP协议，通过22端口登录

![image.png](https://ucc.alicdn.com/pic/developer-ecology/6814c3ecdaf74be09f468fa2db40cfba.png "image.png")

###### 上传前端dist目录

上传到哪里呢？回看一下nginx的配置。

会发现dist目录下的文件，右键上传到/usr/share/nginx/foo即可。

![image.png](https://ucc.alicdn.com/pic/developer-ecology/17000a9b9d004fd0a5511b7c407d19a0.png "image.png")

上面的事情做完了，我们得到的结果是怎样呢？

1. 访问blog.kaigege.cn重定向到frankkai.github.io  
   访问blog.kaigege.cn中闿问题到frankkai.github.io
2. 访问foo.kagege.cn/index.html获取到前端静态资源

ok，部署到腾讯云服务器并使用nginx做反向代理完毕。

备注：nginx的反向代理配置中，统一监听了80端口，同样也需要在服务器实例安全组配置中放开。

### 总结与反思

---

- 七牛云对象存储，阿里云OSS，腾讯云OSS：存储文件并CDN加速。七牛云bucket不支持创建目录，OSS支持。
- 腾讯云域名管理： CNAME，解析二级域名。需要公网备案过的域名才能玩起来。
- 腾讯云服务器：存储文件并部署nginx。解析二级域名全靠这个。
- nginx：反向代理重定向博客，访问前端资源。nginx是神器。
- 未涉及docker，k8s新型前端部署，有机会实践时再做补充。

## 参考

[如何部署前端dist目录？-阿里云开发者社区](https://developer.aliyun.com/article/948133)
