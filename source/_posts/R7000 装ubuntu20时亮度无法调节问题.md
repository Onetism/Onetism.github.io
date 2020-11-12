---
title: R7000 装ubuntu20时亮度无法调节问题
---
发布日期: 2020-11-12 09:21:45&emsp;&emsp;作者：Onetism &emsp;&emsp; Github:[https://github.com/Onetism/Onetism.github.io](https://github.com/Onetism/Onetism.github.io)


## 1.第一步
&emsp;&emsp;终端输入
```
sudo vim /etc/default/grub
```
&emsp;&emsp;打开的文件修改成如下形式
<div align=center><img src="R7000 装ubuntu20时亮度无法调节问题/1.png" width="75%" ></div>

## 2.第二步
&emsp;&emsp;终端输入
```
sudo update-grub
```

## 3.第三步
&emsp;&emsp;终端输入
```
sudo vim /usr/share/X11/xorg.conf.d/10-nvidia.conf 
```
&emsp;&emsp;修改文件内容成以下形式
```
Section "Device"
        Identifier "Device0"
        Driver "nvidia"
        VendorName "NVIDIA Corporation"
        Option "RegistryDwords" "EnableBrightnessControl=1"
        Option "NoLogo" "True"
Endsection

```
<div align=center><img src="R7000 装ubuntu20时亮度无法调节问题/2.png" width="75%" ></div>

## 4.第五步
&emsp;&emsp;禁用`ideapad_laptop`,终端行输入
```
sudo vim /etc/modprobe.d/blacklist.conf
```
&emsp;&emsp;在文件最后一行加上`blacklist ideapad_latop`

&emsp;&emsp; 重启即可使用`Fn+快捷键`调节亮度了
