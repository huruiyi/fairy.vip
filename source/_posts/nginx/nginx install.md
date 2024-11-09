---
title: Step.1 Nginx的安装
categories:
- [Nginx]
---


# mac下安装nginx
```yaml
brew install nginx

brew reinstall nginxßßß

Docroot is: /usr/local/var/www

The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080 so that nginx can run without sudo.

nginx will load all files in /usr/local/etc/nginx/servers/.

To start nginx:
  brew services start nginx

Or, if you don't want/need a background service you can just run:
  /usr/local/opt/nginx/bin/nginx -g 'daemon off;'

==> Summary
🍺  /usr/local/Cellar/nginx/1.21.3: 26 files, 2.2MB

```



# Linux(Ubuntu)下安装nginx

`安装依赖：`

```yaml
sudo apt install gcc
sudo apt install make
sudo apt install zlib1g-dev
sudo apt install libpcre3-dev
```

`nginx版本和ssl版本：`

```yaml
openssl-3.4.0.tar.gz
nginx-1.27.2.tar.gz
```

`安装配置：`

```yaml
sudo ./configure  \
    --prefix=/home/huruiyi/app/nginx  \
    --pid-path=/home/huruiyi/app/nginx/pid/nginx.pid  \
    --lock-path=/lock/nginx.lock  \
    --error-log-path=/home/huruiyi/app/nginx/log/error.log  \
    --http-log-path=/home/huruiyi/app/nginx/log/access.log  \
    --http-client-body-temp-path=/home/huruiyi/app/nginx/temp/client  \
    --http-proxy-temp-path=/home/huruiyi/app/nginx/temp/proxy  \
    --http-fastcgi-temp-path=/home/huruiyi/app/nginx/temp/fastcgi  \
    --http-uwsgi-temp-path=/home/huruiyi/app/nginx/temp/uwsgi  \
    --http-scgi-temp-path=/home/huruiyi/app/nginx/temp/scgi  \
    --with-http_ssl_module  \
    --with-http_gzip_static_module \
    --with-openssl=/home/huruiyi/soft/openssl-3.4.0
```

`执行结果：`

```yaml
Configuration summary
  + using system PCRE library
  + using OpenSSL library: /home/huruiyi/soft/openssl-3.4.0
  + using system zlib library

  nginx path prefix: "/home/huruiyi/app/nginx"
  nginx binary file: "/home/huruiyi/app/nginx/sbin/nginx"
  nginx modules path: "/home/huruiyi/app/nginx/modules"
  nginx configuration prefix: "/home/huruiyi/app/nginx/conf"
  nginx configuration file: "/home/huruiyi/app/nginx/conf/nginx.conf"
  nginx pid file: "/home/huruiyi/app/nginx/pid/nginx.pid"
  nginx error log file: "/home/huruiyi/app/nginx/log/error.log"
  nginx http access log file: "/home/huruiyi/app/nginx/log/access.log"
  nginx http client request body temporary files: "/home/huruiyi/app/nginx/temp/client"
  nginx http proxy temporary files: "/home/huruiyi/app/nginx/temp/proxy"
  nginx http fastcgi temporary files: "/home/huruiyi/app/nginx/temp/fastcgi"
  nginx http uwsgi temporary files: "/home/huruiyi/app/nginx/temp/uwsgi"
  nginx http scgi temporary files: "/home/huruiyi/app/nginx/temp/scgi"
```

`安装：`

```yaml
make install
```



# nginx安装其他配置项

```yaml
--sbin-path=path
--modules-path=path
--conf-path=path
--builddir=path
--with-perl_modules_path=path
--with-perl=path
--add-module=path
--add-dynamic-module=path
--with-cc=path
--with-cpp=path

--with-pcre=path
--with-zlib=path
--with-libatomic=path
--with-openssl=path
```

# nginx启动相关命令

```
To start nginx, run the executable file. Once nginx is started, it can be controlled by invoking the executable with the -s parameter. Use the following syntax:

nginx -s signal
Where signal may be one of the following:

stop — fast shutdown
quit — graceful shutdown
reload — reloading the configuration file
reopen — reopening the log files

For example, to stop nginx processes with waiting for the worker processes to finish serving current requests, the following command can be executed:

nginx -s quit
This command should be executed under the same user that started nginx.
Changes made in the configuration file will not be applied until the command to reload configuration is sent to nginx or it is restarted. To reload configuration, execute:

nginx -s reload
Once the master process receives the signal to reload configuration, it checks the syntax validity of the new configuration file and tries to apply the configuration provided in it. If this is a success, the master process starts new worker processes and sends messages to old worker processes, requesting them to shut down. Otherwise, the master process rolls back the changes and continues to work with the old configuration. Old worker processes, receiving a command to shut down, stop accepting new connections and continue to service current requests until all such requests are serviced. After that, the old worker processes exit.

A signal may also be sent to nginx processes with the help of Unix tools such as the kill utility. In this case a signal is sent directly to a process with a given process ID. The process ID of the nginx master process is written, by default, to the nginx.pid in the directory /usr/local/nginx/logs or /var/run. For example, if the master process ID is 1628, to send the QUIT signal resulting in nginx’s graceful shutdown, execute:

kill -s QUIT 1628
For getting the list of all running nginx processes, the ps utility may be used, for example, in the following way:

ps -ax | grep nginx
For more information on sending signals to nginx, see Controlling nginx.
```

