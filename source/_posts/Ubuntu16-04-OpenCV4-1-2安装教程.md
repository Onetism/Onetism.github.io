---
title: Ubuntu16.04+OpenCV安装教程
---
发布日期: 2019-11-12 09:21:45&emsp;&emsp;作者：Onetism &emsp;&emsp; Github:[https://github.com/Onetism/Onetism.github.io](https://github.com/Onetism/Onetism.github.io)

## 1.安装前的准备

&emsp;&emsp;首先，更新软件

	sudo apt-get update
	sudo apt-get upgrade

&emsp;&emsp;接下来安装最新的cmake,进入[https://cmake.org/download/](https://cmake.org/download/)下载最新版本的cmake源代码，解压：

	tar -zxvf cmake-3.16.0-rc3.tar.gz(下载包的名字)

&emsp;&emsp;先安装cmake(Ubuntu软件库中的旧版cmake)

	sudo apt-get install cmake

&emsp;&emsp;然后编译安装最新版本cmake,按步运行

	cd cmake-3.15.2/(具体看解压后的名称)
	mkdir build
	cd build
	cmake ..
	make -j8(同时编译数，依据芯片核数选)
	sudo make install

&emsp;&emsp;cmake源码安装之后是没有GUI的，要安装cmake-gui，还需要安装cmake-qt-gui：

	sudo apt-get install cmake-qt-gui

&emsp;&emsp;下载[OpenCV](https://github.com/opencv/opencv/releases)的源代码和对应版本的[opencv_contrib](https://github.com/opencv/opencv_contrib/releases)源代码。

## 2.安装依赖项编译

&emsp;&emsp;首先依赖项安装：

	sudo apt-get install build-essential cmake unzip pkg-config
	sudo apt-get install libjpeg-dev libpng-dev libtiff-dev
	sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
	sudo apt-get install libxvidcore-dev libx264-dev
	sudo apt-get install libgtk-3-dev
	sudo apt-get install libatlas-base-dev gfortran
	sudo apt-get install python3-dev

&emsp;&emsp;对前面下载的源代码解压：

	tar -zxvf opencv-4.1.1.tar.gz(根据自己文件名字定)
	tar -zxvf opencv_contrib-4.1.1.tar.gz(根据自己文件名字定)

&emsp;&emsp;新建文件夹，储存编译文件：

	cd opencv-4.1.1/(根据自己文件名字定)
	mkdir build
&emsp;&emsp;打开cmake-gui，设定<font color=red>`source code`</font>目录为<font color=red>`/home/onetism/opencv-4.1.1/`</font>，`onetism`是用户名，每个电脑设置不一样，选择自己的即可。设定<font color=red>`binaries`</font>目录为刚才新建的<font color=red>`build`</font>目录，也就是<font color=red>`/home/onetism/opencv-4.1.1/build`</font>。

&emsp;&emsp;OpenCV默认生成的python版本是python 2.7，为了让OpenCV编译生成<font color=red>`python 3`</font>版本的<font color=red>`opencv_python3`</font>，需要添加一个编译选项。在cmake-gui当中，点击<font color=red>`Add Entry`</font>，如下图所示，填入编译选项名字<font color=red>`PYTHON_DEFAULT_EXECUTABLE`</font>和系统中<font color=red>`python 3`</font>的路径<font color=red>`/usr/bin/python3.5`</font>（依实际情况而定）即可,`Type`选择<font color=red>`FILEPATH`</font>。

&emsp;&emsp;完成以后点击`configure`，按默认操作即可，等待完成，然后进行编译配置，将<font color=red>`OPENCV_GENERATE_PKGCONFIG`</font>打开，新版本的OpenCV默认把这个编译选项给关掉了！如果该选项没有打开，pkg-config就没法使用了。这样，你想在命令行里直接编译opencv程序会是很痛苦的事情，只能借助CMake解决，同时<font color=red>`CMAKE_BUILD_TYPE`</font>输入<font color=red>`Release`</font>。

&emsp;&emsp;为了编译`opencv_contrib`，需要在cmake-gui中将编译选项<font color=red>`OPENCV_EXTRA_MODULES_PATH`</font>填入contrib库所在的路径，比如<font color=red>`/home/your-name/opencv_contrib-4.1.1/modules/`</font>

&emsp;&emsp;最后，编译安装OpenCV生成Makefile之后，进入/home/onetism/opencv-4.1.1/build，编译并安装。然后是漫长的等待过程。

	make -j8
	sudo make install

&emsp;&emsp;验证`pkg-config`功能是否正确，回到主目录：

	pkg-config --cflags --libs opencv4

&emsp;&emsp;如果同时安装了ROS系统，那么`import cv2`会报错，主要是python版本的原因，可以给ROS的改个名字：

	cd /opt/ros/kinetic/lib/python2.7/dist-packages/(自己的路径)
	sudo mv cv2.so cv2_ros.so

&emsp;&emsp;cmake需要下载，有可能因为墙的的原因下载慢或者下载失败，可以查看我的另外博客——[Ubuntu安装ss实现科学上网](https://onetism.github.io/2019/11/12/Ubuntu%E5%AE%89%E8%A3%85ss%E5%AE%9E%E7%8E%B0%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91/)

## 3.配置环境变量

	sudo vim ~/.bashrc

&emsp;&emsp;在最后行添加`export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig`，保存退出。


	sudo vim /etc/ld.so.conf.d/opencv.conf

&emsp;&emsp;创建的文件中加上`/usr/local/lib`，在运行以下命令

	sudo ldconfig

## 4.测试

	cd ~/opencv4.1.2/samples/cpp/example_cmake(自己的路径)
	cmake .
	make -j8
	./opencv_example
&emsp;&emsp;会打开电脑的自带摄像头，如果没有修改`example.cpp`,重新编译测试。