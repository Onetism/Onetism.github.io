---
title: Ubuntu安装ss实现科学上网
---
发布日期: 2019-11-12 09:21:45&emsp;&emsp;作者：Onetism &emsp;&emsp; Github:[https://github.com/Onetism/Onetism.github.io](https://github.com/Onetism/Onetism.github.io)

### 安装步骤
&emsp;&emsp;为了便于开发，偶尔还是需要墙外的资源，因此在Ubuntu下如何进行科学上网。新装的系统，首先输入命令,更新软件：

    sudo apt-get update
	sudo apt-get upgrade

&emsp;&emsp;然后安装`pip`,本次使用的是Python3,所以首先安装`pip3`,s输入命令：

    sudo apt-get install python3-pip


&emsp;&emsp;如果可以科学上网的话，可以直接输入下面命令进行安装：

    pip3 install https://github.com/shadowsocks/shadowsocks/archive/master.zip


&emsp;&emsp;不科学上网上面命令是无法运行的，可以先在能够科学上网的设备上将安装包[https://github.com/shadowsocks/shadowsocks/archive/master.zip](https://github.com/shadowsocks/shadowsocks/archive/master.zip)下载下来,然后复制到系统下，输入命令下面命令安装：

    pip3 install 压缩包的名字(例如：shadowsocks-master.zip)

&emsp;&emsp;安装完成之后，输入下面命令查看版本号，如果是`3.0.0`的话，是支持`aes-256-gcm`加密方式的。

    ssserver --version

&emsp;&emsp;创建Shadowsocks配置文件：

    sudo vim /etc/shadowsocks.json

&emsp;&emsp;复制粘贴如下内容:

    {
	    "server":"服务器地址",
	    "server_port":服务器端口,
	    "local_address": "127.0.0.1",
	    "local_port":1080,
	    "password":"服务器密码",
	    "timeout":300,
	    "method":"aes-256-gcm(和服务器一致)",
	    "fast_open": false
    }

&emsp;&emsp;按自己修改好后保存，退出，然后终端运行命令：

    sudo sslocal -c /etc/shadowsocks.json -d start

&emsp;&emsp;如果命令找不到，使用绝对路径，用`which sslocal`,找到命令所在路径，再按照[浏览器配置代理教程](https://proxy-switchyomega.com/settings/)，教程测试谷歌即可。

&emsp;&emsp;接下来，配置自启动，新建管理文件：

    sudo vim /lib/systemd/system/shadowsocks.service

&emsp;&emsp;复制以下内容，然后保存退出：

	[Unit]
	Description=Shadowsocks Server
	After=network.target
	
	[Service]
	ExecStart=/usr/local/bin/sslocal（命令所在位置） -c /etc/shadowsocks/shadowsocks.json（配置文件所在位置）
	Restart=on-abort

	[Install]
	WantedBy=multi-user.target

&emsp;&emsp;运行`shadowsocks.service`

    sudo systemctl start shadowsocks.service

&emsp;&emsp;允许开机自动启动

    sudo systemctl enable /lib/systemd/system/shadowsocks.service

&emsp;&emsp;查看运行状态

    sudo systemctl status shadowsocks.service

&emsp;&emsp;至此，安装完成，`ssserver`使用在服务器上，`sslocal`使用在客户机上。

### 下载最新libsodium
&emsp;&emsp; 现在最新的chacha20加密方式，上面是不能够用的，因此需要更新，通过以下方式就可以实现了：

	sudo apt-get install build-essential
	wget https://download.libsodium.org/libsodium/releases/LATEST.tar.gz 或者wget https://github.com/jedisct1/libsodium/releases/download/1.0.10/libsodium-1.0.10.tar.gz
	tar -xzvf LATEST.tar.gz或者libsodium-1.0.10.tar.gz
	cd libsodium*（解压文件夹下）
	./configure && make -j8 && sudo make install
	ldconfig