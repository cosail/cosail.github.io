---
layout: post
title: "VirtualBox中使用不了USB设备？"
date: 2014-10-31 10:46:06 +0800
comments: true
categories: ubuntu-faq
---

### ubuntu下virtualbox中的usb设备为灰色不可用怎么办?

virtualbox 装完成后会有 vboxusers 用户组，只要添加用户到组就行了：

$ sudo vim /etc/group  
找到这一行：  vboxusers:x:127:  
添加自己的用户名(假设为w)：  vboxusers:x:127:w

然后注销重新登陆w试试。
