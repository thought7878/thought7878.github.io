# Nginx在前端部署中的应用详解

Nginx是前端部署中最常用的Web服务器和反向代理服务器，以其高性能、稳定性和丰富的功能成为前端部署的首选工具。以下是Nginx在前端部署中的详细应用指南：

## 一、Nginx简介

Nginx（发音为"engine-x"）是一个高性能的HTTP和反向代理服务器，也是一个IMAP/POP3/SMTP代理服务器。

在前端部署中，Nginx主要扮演以下角色：
- **静态文件服务器**：高效提供HTML、CSS、JS等静态资源
- **反向代理**：将请求转发给后端API服务
- 负载均衡：在多服务器环境中分发流量
- SSL/TLS终止：处理HTTPS加密解密
- 缓存：提高静态资源访问速度
- 压缩：减少传输数据量

## 二、Nginx安装

### Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

### macOS (使用Homebrew)
```bash
brew install nginx
brew services start nginx
```

### Windows
可从[Nginx官网](http://nginx.org/en/download.html)下载Windows版本

## 三、基本目录结构

- `/etc/nginx/`：主要配置目录
- `/etc/nginx/nginx.conf`：主配置文件
- `/etc/nginx/sites-available/`：可用站点配置
- `/etc/nginx/sites-enabled/`：启用的站点配置（通常是sites-available的符号链接）
- `/var/www/`：网站文件默认存放位置
- `/var/log/nginx/`：日志文件目录

## 四、前端项目部署核心配置

### 1. 基础配置模板

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    
    # 项目根目录，指向构建后的dist目录
    root /var/www/your-project/dist;
    index index.html;
    
    # 处理前端路由（SPA应用关键配置）
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # 静态资源缓存优化
    location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|svgz|mp4|ogg|ogv|webm|htc)$ {
        expires 1M;
        access_log off;
        add_header Cache-Control "public";
    }
    
    location ~* \.(?:css|js)$ {
        expires 1y;
        access_log off;
        add_header Cache-Control "public, immutable";
    }
    
    # 开启Gzip压缩
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml application/json application/javascript application/xml application/xml+rss;
    
    # 隐藏Nginx版本号
    server_tokens off;
}
```

### 2. SPA应用特殊配置

单页面应用(SPA)如React、Vue、Angular需要特别配置以处理前端路由：

```nginx
location / {
    # 关键配置：当请求的文件不存在时，返回index.html
    try_files $uri $uri/ /index.html;
}
```

### 3. 反向代理配置（前后端分离）

```nginx
server {
    listen 80;
    server_name yourdomain.com;
    
    root /var/www/your-frontend/dist;
    index index.html;
    
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # 将API请求代理到后端服务
    location /api/ {
        proxy_pass http://backend-server:3000/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    
    # 处理WebSocket连接
    location /socket.io/ {
        proxy_pass http://backend-server:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }
}
```

## 五、高级配置技巧

### 1. 环境变量支持

对于需要在运行时配置环境变量的前端应用（如create-react-app）：

```nginx
server {
    # ...
    
    location / {
        # 使用sub_filter替换index.html中的占位符
        sub_filter 'REPLACE_API_URL' $api_url;
        sub_filter 'REPLACE_ENV' $env;
        sub_filter_once off;
        try_files $uri $uri/ /index.html;
    }
    
    # 通过请求头或查询参数设置变量
    set $api_url "https://api.example.com";
    if ($arg_env = "staging") {
        set $api_url "https://staging-api.example.com";
    }
}
```

### 2. 灰度发布配置

```nginx
http {
    # 定义两个上游服务器组
    upstream production {
        server 127.0.0.1:8080;
    }
    
    upstream beta {
        server 127.0.0.1:8081;
    }
    
    server {
        # 5%的流量到beta环境
        if ($http_cookie ~* "beta=true") {
            set $target beta;
        }
        if ($arg_beta = "true") {
            set $target beta;
        }
        if ($remote_addr ~ "192\.168\.1\.[0-9]+") {
            set $target beta;
        }
        
        location / {
            proxy_pass http://$target;
            # 默认指向production
        }
    }
}
```

### 3. 安全加固配置

```nginx
server {
    # ...
    
    # 防止点击劫持
    add_header X-Frame-Options "SAMEORIGIN";
    
    # 启用内容安全策略
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' https://apis.google.com; style-src 'self' 'unsafe-inline'; img-src 'self' data:; font-src 'self' fonts.gstatic.com; connect-src 'self'; object-src 'none'; frame-ancestors 'none'; base-uri 'self'; form-action 'self';";
    
    # 防止MIME类型嗅探
    add_header X-Content-Type-Options "nosniff";
    
    # 启用XSS防护
    add_header X-XSS-Protection "1; mode=block";
    
    # 限制请求方法
    if ($request_method !~ ^(GET|HEAD|POST)$ ) {
        return 405;
    }
    
    # 防止敏感文件访问
    location ~ /\. {
        deny all;
    }
    
    location ~ ~$ {
        deny all;
    }
}
```

## 六、HTTPS配置（使用Let's Encrypt）

### 1. 安装Certbot

```bash
sudo apt install certbot python3-certbot-nginx
```

### 2. 获取并配置SSL证书

```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

### 3. 自动生成的HTTPS配置

```nginx
server {
    listen 443 ssl;
    server_name yourdomain.com www.yourdomain.com;
    
    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
    
    # 安全强化
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
    
    # HSTS (HTTP Strict Transport Security)
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    
    # 其他前端配置...
    root /var/www/your-project/dist;
    index index.html;
    
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # 其他优化配置...
}

# HTTP自动重定向到HTTPS
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$host$request_uri;
}
```

## 七、性能优化技巧

### 1. 静态资源缓存策略

```nginx
# 长期缓存带哈希的资源
location ~* \.(?:css|js)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}

# 无哈希的HTML不缓存
location ~* \.html$ {
    expires -1;
    add_header Cache-Control "no-store, no-cache, must-revalidate";
}
```

### 2. 启用Brotli压缩（比Gzip更高效）

```nginx
# 需要先安装ngx_brotli模块
brotli on;
brotli_comp_level 6;
brotli_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```

### 3. HTTP/2支持

```nginx
listen 443 ssl http2;
```

## 八、常用命令

```bash
# 测试配置文件语法
sudo nginx -t

# 重新加载配置（不中断服务）
sudo nginx -s reload

# 重启Nginx
sudo systemctl restart nginx

# 查看Nginx状态
sudo systemctl status nginx

# 查看访问日志
tail -f /var/log/nginx/access.log

# 查看错误日志
tail -f /var/log/nginx/error.log
```

## 九、常见问题排查

### 1. 404错误（SPA应用）

**问题**：前端路由刷新后显示404  
**解决方案**：确保配置了`try_files $uri $uri/ /index.html;`

### 2. 静态资源加载失败

**问题**：CSS/JS文件返回404  
**解决方案**：
- 检查root路径是否正确
- 确认构建后的文件确实存在于指定目录
- 检查Nginx用户是否有读取权限

### 3. 跨域问题

**解决方案**：
```nginx
location /api/ {
    proxy_pass http://backend;
    add_header 'Access-Control-Allow-Origin' '*';
    add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
    add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
    add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';
    
    if ($request_method = 'OPTIONS') {
        add_header 'Access-Control-Max-Age' 1728000;
        add_header 'Content-Type' 'text/plain; charset=utf-8';
        add_header 'Content-Length' 0;
        return 204;
    }
}
```

### 4. 缓存问题

**问题**：用户看不到最新版本  
**解决方案**：
- 使用带哈希的文件名（webpack/vite默认支持）
- 设置合理的缓存头
- 版本更新时清除CDN缓存

Nginx作为前端部署的核心组件，合理配置可以极大提升应用性能、安全性和用户体验。掌握这些配置技巧，能够帮助你高效、专业地完成前端部署工作。