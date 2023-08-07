# Linux_Nginx_user

# 安装命令 安装环境

1.先安装环境

yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel

2.进入目录处理

cd /usr/local/src/

wget http://downloads.sourceforge.net/project/pcre/pcre/8.45/pcre-8.45.tar.gz

直接wget命令直接复制会因为字符问题报错

内核解压压缩包

tar zxvf pcre-8.45.tar.gz

cd pcre-8.45


./configure
make
make install
pcre-config --version

#环境前置安装文件 正常显示不报错就ok   END

#安装nginx
进入目录下载安装包

cd /usr/local/src/

wget http://nginx.org/download/nginx-1.9.2.tar.gz

解压文件

tar zxvf nginx-1.9.2.tar.gz

cd nginx-1.9.2

#配置nginx环境

./configure --prefix=/usr/local/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.45 --with-stream --with-stream_ssl_module


make && make install

/usr/local/webserver/nginx/sbin/nginx -v

#环境配置完毕 END

#修改核心文件

vim /usr/local/webserver/nginx/conf/nginx.conf

#配置反向代理主要是nginx.conf文件配置

#普通的反向代理  每次多监听一个端口需要增加一个server代码块，server需要在http代码块下面

    server {
        listen 90 ;#监听端口
        server_name localhost;#监听的域名
        location / {
            proxy_pass www.baidu.com;#动态代理的域名
        }
    }

#tcp代理 stream代码块不需要在http代码块下面，和http同级

    stream {
            upstream rabbitmq{
                    server lwww.baidu.com:5555; #代理的域名和端口
            }
            server {
                    listen 5555; #本地拦截的端口
                    proxy_connect_timeout 600s;
                    proxy_pass rabbitmq;
            }
    }


/usr/local/webserver/nginx/sbin/nginx -t 			#检查配置文件命令

/usr/local/webserver/nginx/sbin/nginx -s reload            # 重新载入配置文件

/usr/local/webserver/nginx/sbin/nginx -s reopen            # 重启 Nginx

/usr/local/webserver/nginx/sbin/nginx -s stop              # 停止 Nginx../

/sbin/nginx -c /usr/local/webserver/nginx/conf/nginx.conf #配置文件报错时重新指向conf文件

cd /usr/local/webserver/nginx/sbin

./nginx


查看nginx运行状态

ps -ef | grep nginx
