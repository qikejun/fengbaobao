---
title: 阿里云ecs服务器nginx搭建
date: 2019-10-24 14:00:15
categories:
  - 服务器
  - nginx
tags: nginx
photos:
  - 'https://yunpengGit.github.io/code/mock-img/fengbaobao/baoer4.jpg'
---

# 服务器部署文档 - windows 

## 测试服务器

centOS6.9

内网ip `10.88.80.142`

公网ip `39.97.236.118`

### 服务器操作

1. 使用工具

  Xshell 6 连接服务器，Xftp 6 本地文件上传服务器（快捷入口（标准按钮，和 Xshell 图标一起的绿色图标，点击打开 Xftp），**上传文件注意当前服务器文件夹所在目录**）

2. 连接服务器

3. yum安装必要的包（阿里云服务器内置 yum ）

   ```bash
   yum -y install gcc gcc-c++ autoconf pcre-devel make automake
   yum -y install wget httpd-tools vim
   
   #有些包服务器已经内置
   ```

4. 安装nginx

   ```bash
    yum install nginx
    #查看版本
    nginx -v
   ```

5. 查看nginx 安装目录

   ```
    rpm -ql nginx
    
    #文件目录
     /etc/logrotate.d/nginx
     /etc/nginx
     /etc/nginx/conf.d
     /etc/nginx/conf.d/default.conf
     /etc/nginx/fastcgi_params
     /etc/nginx/koi-utf
     /etc/nginx/koi-win
     /etc/nginx/mime.types
     /etc/nginx/modules
     /etc/nginx/nginx.conf
     /etc/nginx/scgi_params
     /etc/nginx/uwsgi_params
     /etc/nginx/win-utf
     /etc/sysconfig/nginx
     /etc/sysconfig/nginx-debug
     /usr/lib/systemd/system/nginx-debug.service
     /usr/lib/systemd/system/nginx.service
     /usr/lib64/nginx
     /usr/lib64/nginx/modules
     /usr/libexec/initscripts/legacy-actions/nginx
     /usr/libexec/initscripts/legacy-actions/nginx/check-reload
     /usr/libexec/initscripts/legacy-actions/nginx/upgrade
     /usr/sbin/nginx
     /usr/sbin/nginx-debug
     /usr/share/doc/nginx-1.16.1
     /usr/share/doc/nginx-1.16.1/COPYRIGHT
     /usr/share/man/man8/nginx.8.gz
     /usr/share/nginx
     /usr/share/nginx/html
     /usr/share/nginx/html/50x.html
     /usr/share/nginx/html/index.html
     /var/cache/nginx
     /var/log/nginx
   ```

6. nginx配置文件

   ```
   /etc/nginx/conf.d/default.conf
   /etc/nginx/nginx.conf
   ```

7. 使用的基本命令

   ```bash
   #nginx 命令
   nginx #启动服务器
   nginx -s quit #停止服务器
   nginx -s reload #重启服务器
   ps -ef | grep nginx #查询启动的nginx进程
   kill -QUIT 进程id #从容停止进程
   kill -TERM 进程id #快速停止进程
   kill -9 进程id #强制停止进程
   fuser -n tcp 端口号 #查看端口占用
   kill -9 端口号 #结束端口占用
   
   #文件查看编辑
   cd /  #直接到顶层 / 目录下
   cd .. #返回上一层目录
   cd 文件夹名称 #进入到文件夹名目录下
   ls   #查看当前目录下的所有文件，文件夹
   cat 文件名 #查看文件内容
   
   vim 文件名 #编辑文件
   #进入编辑模式
   #按 i 开始编辑，上下左右减调整指针位置，
   #按 esc 退出编辑模式
   #输入 :q 不保存退出 :wq 保存退出
   ```

### 反向代理 配置

<span style="color:red">注：在文件 etc/nginx/conf.d/default.conf 中配置是无效的</span>

我刚开始配置在 default.conf 中时，控制台报错

```
502 Bad Gateway
```



查询 var/log/nginx  cat error.log可能会遇到以下错误：

```
SSL_do_handshake() failed (SSL: error:14077102:SSL routines:SSL23_GET_SERVER_HELLO:unsupported protocol) while SSL handshaking to upstream
```

 **<span style="color:skyblue;">配置文件  etc/nginx/nginx.conf</span>**

 将当前的请求https://ceshi.com/api/getData(服务器域名(ceshi.com)转发到 https://www.baidu.com/getData

```bash
http {
    server {
    	listen       80;
        #server_name  localhost;
        server_name  ceshi.com;

        location ^~/api/ {
 	        proxy_pass https://www.baidu.com/;
        }
    }
}
```

