---
title: mastodon搭建全攻略
date: 2019-09-23 11:00:26
tags:
---
# Mastodon 搭建全流程攻略

Mastodon 是一个免费的开源社交网络程序，一个商业平台的替代方案，避免了单个公司垄断你沟通的风险。选择你信任的服务器，无论选择的是哪个，你都可以与其他人进行互动。任何人都可以运行自己的 Mastodon 实例，并无缝地参与到社交网络中。可以认为是一个去中心化的twitter。

# 环境需求

---

1. 一台linux服务器
2. nginx
3. [docker](https://www.docker.com/community-edition)
4. [docker-compose](https://github.com/docker/compose/releases/latest)

# 搭建

---

Clone Mastodon仓库，执行如下命令：

    # Clone mastodon to ~/live directory
    git clone https://github.com/tootsuite/mastodon.git live
    # Change directory to ~/live
    cd ~/live
    # Checkout to the latest stable branch
    git checkout $(git tag -l | grep -v 'rc[0-9]*$' | sort -V | tail -n 1)

# 获取Mastodon镜像

---

### 使用预建的镜像

如果你不准备对实例进行个性化的定制，可以使用预建的镜像：

1. 在编辑器中打开`docker-compose.yml`
    1. 注释所有镜像下build:. 这一行
    2. 修改`image: tootsuite/mastodon` 行为任意你需要的版本，如：`image: tootsuite/mastodon:v2.2.0` 不修改默认为latest。
2. 执行 `cp .env.production.sample .env.production` 生成配置文件，供给稍后修改。
3. 执行`docker-compose build` 会从dockerhub中拉取依赖。
4. 执行`chown -R 991:991 public` （官方的说明有缺漏，在上线后会多出一个public/system的文件夹需要给予权限，否则会无法跨站关注）

### 定制镜像

我直接使用了预建的镜像并没有定制，以下是官网的文档。

1. 用编辑器打开`docker-compose.yml` 
    1. 取消所有你需要的镜像`build:.`行的注释
    2. 保存文件
2. 执行`cp .env.production.sample .env.production`
3. 执行`docker-compose build`
4. 执行`chown -R 991:991 public`

# 域名DNS解析

---

买了香港的服务器，不需要去备案。域名也选择了相对便宜的[namesilo](https://www.namesilo.com/login.php)。

1. 创建一条resource record type 为A的
2. address输入自己服务器的IP
3. TTL写3603
4. 保存后等待10多分钟大概就能生效

![Untitled-2ee4f922-f24e-4526-8435-613b45f3a11b.png](https://i.loli.net/2019/09/23/CKsl3rvgjYL5d74.png)

# MAILGUN配置域名邮箱

---

Mastodon需要邮箱进行注册和找回密码。mailgun每个月有1000条免费。

1. ADD NEW DOMAIN

    ![image.png](https://i.loli.net/2019/09/23/8xV4el5ZRUQfPWu.png)

2. 填入域名。之后根据提示，到解析商处填入5条记录。

    ![image.png](https://i.loli.net/2019/09/23/cFmtjly3WdOLhY4.png)

3. 回到 Mailgun, check DNS RECORD
4. 配置routes，CREATE NEW ROUTE
    - catch all：接受发往该域的所有邮件
    - Match Recipient：接受特定用户的邮件

# 构建Mastodon

---

1. 执行`docker-compose run --rm web bundle exec rake mastodon:setup`按照提示输入相关参数。（在执行的时候遇到一个问题，在数据库初始化前，生成的配置文件没有被直接写入`.env.production`，需要将控制台上的输入手动写入到文件中，才能继续执行数据的初始化。）如果机器的内存不够可以增加swap
2. 执行`docker-compose up -d`

# nginx配置

---

修改nginx配置文件

    map $http_upgrade $connection_upgrade {
      default upgrade;
      ''      close;
    }
    
    server {
      listen 80;
      listen [::]:80;
      server_name example.com;
      root /home/mastodon/live/public;
      # Useful for Let's Encrypt
      location /.well-known/acme-challenge/ { allow all; }
      location / { return 301 https://$host$request_uri; }
    }
    
    server {
      listen 443 ssl http2;
      listen [::]:443 ssl http2;
      server_name example.com;
    
      ssl_protocols TLSv1.2;
      ssl_ciphers HIGH:!MEDIUM:!LOW:!aNULL:!NULL:!SHA;
      ssl_prefer_server_ciphers on;
      ssl_session_cache shared:SSL:10m;
    
      ssl_certificate     /etc/letsencrypt/live/example.com/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    
      keepalive_timeout    70;
      sendfile             on;
      client_max_body_size 80m;
    
      root /home/mastodon/live/public;
    
      gzip on;
      gzip_disable "msie6";
      gzip_vary on;
      gzip_proxied any;
      gzip_comp_level 6;
      gzip_buffers 16 8k;
      gzip_http_version 1.1;
      gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
    
      add_header Strict-Transport-Security "max-age=31536000";
    
      location / {
        try_files $uri @proxy;
      }
    
      location ~ ^/(emoji|packs|system/accounts/avatars|system/media_attachments/files) {
        add_header Cache-Control "public, max-age=31536000, immutable";
        try_files $uri @proxy;
      }
      
      location /sw.js {
        add_header Cache-Control "public, max-age=0";
        try_files $uri @proxy;
      }
    
      location @proxy {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header Proxy "";
        proxy_pass_header Server;
    
        proxy_pass http://127.0.0.1:3000;
        proxy_buffering off;
        proxy_redirect off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    
        tcp_nodelay on;
      }
    
      location /api/v1/streaming {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header Proxy "";
    
        proxy_pass http://127.0.0.1:4000;
        proxy_buffering off;
        proxy_redirect off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    
        tcp_nodelay on;
      }
    
      error_page 500 501 502 503 504 /500.html;
    }

# 使用Let's Encrypt进行https加密

---

1. 停止nginx

        systemctl stop nginx

2. 需要创建2次证书，第一次在独立模式下使用TLS SNI认证，第二次会使用webroot方法。

        certbot certonly --standalone -d example.com

    成功后，我们会使用webroot方法：

        systemctl start nginx
        # The certbot tool will ask if you want to keep the existing certificate or renew it. Choose to renew it.
        certbot certonly --webroot -d example.com -w /home/mastodon/live/public/

3. 生成定时任务
    1. 创建定时任务

            nano /etc/cron.daily/letsencrypt-renew

    2. 复制如下脚本

            #!/usr/bin/env bash
            certbot renew
            systemctl reload nginx

    3. 保存并退出文件
    4. 执行定时任务

        chmod +x /etc/cron.daily/letsencrypt-renew
        systemctl restart cron