---
title: Fastdfs+nginx编译部署.md
date: 2019-07-09 16:12:40
tags:
---
## 系统环境
- 华为泰山2280服务器
- 中标麒麟操作系统

## 安装包下载
- [libevent-2.1.8-stable.tar.gz](http://libevent.org/)
- [libfastcommonV1.0.39](https://github.com/happyfish100/libfastcommon)
- [fastdfs5.08](https://github.com/happyfish100/fastdfs)
- [fastdfs-nginx-module_1.20.tar.gz](https://github.com/happyfish100/fastdfs-nginx-module/releases)

## 安装步骤：
### 1. libevent-2.1.8-stable.tar.gz
```
tar -zxvf  libevent-2.1.8-stable.tar.gz
cd libevent-2.1.8-stable
./configure --prefix=/usr  
make
make install
```

### 2. libfastcommonV1.0.39
```
tar -zxvf libfastcommonV1.0.39.tar.gz
cd libfastcommonV1.0.39
./make.sh
./make.sh install
cp /usr/lib64/libfastcommon.so /usr/lib
```

### 3. fastdfs5.12 -- tracker服务
```
# 安装Tracker服务
unzip fastdfs-master.zip
cd fastdfs-master
./make.sh
./make.sh install

#查看安装结果:
ll /usr/bin/fdfs_*  

#把conf目录下的所有的配置文件都复制到/etc/fdfs下
cp conf/* /etc/fdfs
     
#配置tracker服务
cd /etc/fdfs
vi tracker.conf

#修改
base_path=/usr/local/fastdfs/tracker
#创建/usr/local/fastdfs/tracker路径
mkidr -p /usr/local/fastdfs/tracker

#重启使用命令
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart

```
### 4. fastdfs5.08 -- storage服务
```
#设置日志保存路径
base_path=/usr/local/fastdfs/storage
#设置图标文件的保存位置
store_path0=/usr/local/fastdfs/storage
#设置tracker服务
tracker_server={IP}:22122
#创建该路径
mkdir /usr/local/fastdfs/storage
#启动服务
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart
```

### 5. 测试
```
cd /etc/fdfs
vi client.conf
#设置日志路径
base_path=/usr/local/fastdfs/client
#设置链接服务
tracker_server={IP}:22122
#创建路径
mkdir /usr/local/fastdfs/client
#开始测试
cd /etc/fdfs
#上传当前anti-steal.jpg图片
/usr/bin/fdfs_test /etc/fdfs/client.conf upload anti-steal.jpg
#结果如下则成功:
  file crc32=2553063749example file url: http://{IP}/group1/M00/00/00/rBIHSVp4F2eAVzatAABdrZgsqUU155.jpg
remote_filename=M00/00/00/rBIHSVp4F2eAVzatAABdrZgsqUU155_big.jpg
example file url: http://{IP}/group1/M00/00/00/rBIHSVp4F2eAVzatAABdrZgsqUU155_big.jpg
```

### 6. 安装fastdfs-nginx-module_1.20.tar.gz

#### 首先需要安装nginx  [nginx 1.15.1](http://nginx.org/download/nginx-1.15.1.tar.gz)
```
#下载nginx 1.15.1
tar -zxvf nginx-1.15.1.tar.gz
cd nginx-1.15.1
./configure
make
make install
```

#### 添加nginx为系统服务
cat /usr/lib/systemd/system/nginx.service

```
[Unit]
Description=nginx
After=network.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -q -g 'daemon on; master_process on;'
ExecStart=/usr/local/nginx/sbin/nginx -g 'daemon on; master_process on;'
ExecReload=/usr/local/nginx/sbin/nginx -g 'daemon on; master_process on;' -s reload
ExecStop=-/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /usr/local/nginx/logs/nginx.pid
TimeoutStopSec=5
KillMode=mixed

[Install]
WantedBy=multi-user.target
```

#### 修改fastdfs-nginx-module配置文件
```
cd fastdfs-nginx-module_1.20
cd src
vi config

# 注意删除local,很多头文件在/usr/include目录下，而不是/usr/local/include
# config文件内容如下 

ngx_addon_name=ngx_http_fastdfs_module

if test -n "${ngx_module_link}"; then
    ngx_module_type=HTTP
    ngx_module_name=$ngx_addon_name
    ngx_module_incs="/usr/include"
    ngx_module_libs="-lfastcommon -lfdfsclient"
    ngx_module_srcs="$ngx_addon_dir/ngx_http_fastdfs_module.c"
    ngx_module_deps=
    CFLAGS="$CFLAGS -D_FILE_OFFSET_BITS=64 -DFDFS_OUTPUT_CHUNK_SIZE='256*1024' -DFDFS_MOD_CONF_FILENAME='\"/etc/fdfs/mod_fastdfs.conf\"'"
    . auto/module
else
    HTTP_MODULES="$HTTP_MODULES ngx_http_fastdfs_module"
    NGX_ADDON_SRCS="$NGX_ADDON_SRCS $ngx_addon_dir/ngx_http_fastdfs_module.c"
    CORE_INCS="$CORE_INCS /usr/include"
    CORE_LIBS="$CORE_LIBS -lfastcommon -lfdfsclient"
    CFLAGS="$CFLAGS -D_FILE_OFFSET_BITS=64 -DFDFS_OUTPUT_CHUNK_SIZE='256*1024' -DFDFS_MOD_CONF_FILENAME='\"/etc/fdfs/mod_fastdfs.conf\"'"
fi
```

#### 对nginx重新config
```
./configure --add-module=/iflytek/install/fastdfs-nginx-module-master/src
make
make install

# 把module中的mod_fastdfs.conf文件复制到/etc/fdfs目录下。编辑：
# 修改base_path
# 修改tracker_server
# 修改url_have_group_name(改为true)
# 修改store_path0（图片保存路径）
```
#### 修改nginx配置文件 重启nginx
```
    server {
        listen       80;
        server_name  localhost;

        location /group1/M00/{
                #root /home/FastDFS/fdfs_storage/data;
                ngx_fastdfs_module;
        }
    }
```
#### 将libfdfsclient.so拷贝至/usr/lib下
cp /usr/lib64/libfdfsclient.so /usr/lib/

#### 启动nginx
systemctl start nginx
到此处，即可直接从浏览器访问上传的文件（图片）