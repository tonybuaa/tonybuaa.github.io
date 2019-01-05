---
title: 解决“VMware Workstation 与 Device/Credential Guard 不兼容”
date: 2019-01-05 21:54:47
tags: vmware
categories: Technology
---

Windows 10升级后，以前的VMware虚拟机无法启动了，提示“VMware Workstation 与 Device/Credential Guard 不兼容”，并且也无法创建新的64位虚拟机。

解决方法：
1. 在“程序和功能”中打开“启用或关闭Windows功能”，把Hyper-V取消。
2. 在服务管理中将HV主机服务（HvHost）停用，并且设置成禁用。
3. 以管理员身份打开Windows PowerShell，运行`bcdedit /set hypervisorlaunchtype off`。
4. 重启计算机。