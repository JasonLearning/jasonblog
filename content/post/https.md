---
keywords:
- 小绿锁
- https
- nginx配置https
- 腾讯云 https
- 配置https
- 申请证书
title: "让你的网站拥有小绿锁"
date: 2018-01-03T21:54:39+08:00
---

>现在大部分的网站都有htts了，再浏览的时候左边会有一个小绿锁，是不是觉得很安心？你可以马上拥有，只需要三步。


### 第一步：申请https证书
如果你的域名是在腾讯云或是阿里云申请的，他们都提供了免费的https证书申请服务，而且非常的迅速，自动审核完成。如果你是在别的地方购买的域名，也有很多第三方的证书服务，在此就不赘述了。以腾讯云为例，申请下来的目录结构如下：
```
Apache/  
IIS/
Nginx/  
Tomcat/  
www.yoursite.com.csr*
```

### 第二步：安装https证书
这边以nginx为例：
一共有三个文件需要使用到，分别将其传入/etc/ssl/private目录下
```
1_www.yoursite.com_bundle.crt  
www.yoursite.com.csr
2_www.yoursite.com.key
```
用于当验证使用。由于只使用这几个文件加密较弱，因此我们还需要使用另一个加密（dhparam）方式来使其更加靠谱一点，进入同级的/etc/ssl/certs/中
```
openssl dhparam -out dhparam.pem 2048
# 如果对机器的配置有自信的话可以用4096
```
这样我们的准备工作都已经做完了

### 第三步：修改配置文件
同样以nginx为例，我直接贴一下我的配置文件，直接更改自己的站名和证书地址就可以啦
```
server {
  listen 80;
  listen [::]:80;
  listen 443 ssl;
  listen [::]:443 ssl;
  server_name yoursite.com;
  root /your/path;
  location ^~ / {
    proxy_pass http://localhost:8000;
    proxy_intercept_errors on;
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
  error_page 404  /404.html;
  # 下面这几行限定必须https
  add_header Strict-Transport-Security max-age=63072000;
  add_header X-Frame-Options DENY;
  add_header X-Content-Type-Options nosniff;
  # ssl的配置
  ssl on;
  ssl_certificate /etc/ssl/private/1_www.yoursite.com_bundle.crt;
  ssl_certificate_key /etc/ssl/private/2_www.yoursite.com.key;
  ssl_prefer_server_ciphers on;
  ssl_dhparam /etc/ssl/certs/dhparam.pem;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers "EECDH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 EECDH+ECDSA+SHA256 EECDH+aRSA+SHA384 EECDH+aRSA+SHA256 EECDH+aRSA+RC4 EECDH EDH+aRSA !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS !RC4";
  keepalive_timeout 70;
  ssl_session_cache shared:SSL:10m;
  ssl_session_timeout 10m;
}
```
就可以很简单的开启https了。如果你开启了强制https，记得做一个301重定向到你的新https站点
```
server {
  listen 80;
  server_name  yoursite.com;
  return 301
  https://www.yoursite.com$request_uri;
}
```

通过以上这几步，就可以拥有自己的小绿锁了。还有一点，记得保证自己的所有外链也是https的，不然还是会提示不安全哦。
