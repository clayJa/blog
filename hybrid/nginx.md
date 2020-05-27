## 前端需要了解的基本nginx操作

#### 1、服务器上安装nginx

(1)默认情况Centos7中无Nginx的源，Nginx官网提供了Centos的源地址。因此可以如下执行命令添加源：
```
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```
(2)安装Nginx
　　通过yum search nginx看看是否已经添加源成功。如果成功则执行下列命令安 Nginx
```
sudo yum install -y nginx
```

#### 2、配置nginx

1、执行命令nginx -t 查看配置文件的位置 <br />
2、修改nginx.conf配置文件
```
 server {
        # 8199 为 nginx 监听的端口，从这里开始访问
        listen       8199 default_server;
        listen       [::]:8199 default_server;
        server_name  _;
        # /project/html 为部署静态文件的地址
        root         /project/html;
        index index.html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
          index index.html;
          # history路由模式需要加入 try_files，hash 路由模式则不需要
          try_files $uri $uri/ /index.html;
        }
        
        # 在这里增加访问后端的代理
        location ^~/api/ {
            # 192.168.2.2:8090 为后端服务地址，记得要加 /,否则会有问题
            proxy_pass http://192.168.2.2:8090/;
            break;
        }
        error_page 404 /404.html;
        location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```
#### 3、上传前端打包后的文件到服务器

使用任意FTP工具（例如windows下WinSCP,macOS 下transmit）,将打包后的文件上传到第二步中配置的目录下

#### 4、启动服务

启动Nginx
```
<!-- 注意：usr/local/nginx 是安装目录 -->
cd usr/local/nginx/sbin
./nginx
```
重启Nginx（每次修改完 nginx 配置都需要重启服务）
```
./nginx -s reopen
```
重新加载Nginx配置文件，然后以优雅的方式重启Nginx
```
./ginx -s reload
```
强制停止Nginx服务
```
./nginx -s stop  
```						
优雅地停止Nginx服务（即处理完所有请求后再停止服务）
```
./nginx -s quit 

```

### 其他nginx配置

####  负载均衡

```
http {

  ...  #这里省略其他配置

  upstream apiHost {
    # 这里是两台服务器部署相同的服务
    server 196.1.158.1:3000;
    server 196.1.158.2:3000;
	}

  ...  #这里省略其他配置

  server {
   
    ...  #这里省略其他配置

    location  ^~ /api/ {
        # 这里就代理到负载均衡了
        proxy_pass http://apiHost/;
    }
  }

}
```

####  rewrite

如果访问链接带了其他无关路径，我们就把它 rewrite 掉

```
http {

  ...  #这里省略其他配置

  server {
   
    ...  #这里省略其他配置

    location ~.*/api/ {
      rewrite ^/api/(.*)$ /$1 break;
      proxy_pass http://196.1.250.241:8888;
      break;
    }
  }

}

```

####  文件太大上传被拦截掉了
放在哪都可以 （http、server、location）
```
 client_max_body_size 20M;
```

####  [其他更多](https://www.nginx.cn/doc)