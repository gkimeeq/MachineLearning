1.安装CentOS 6.10

首先安装[VirtualBox](https://www.virtualbox.org/)，默认安装过程即可。然后在VirtualBox中新建一个虚拟机来安装[CentOS 6.10](https://www.centos.org/)。下载的是[CentOS-6.10-x86_64-minimal.iso](http://mirrors.163.com/centos/6.10/isos/x86_64/)。

2.匹配网络

因为是minimal，没有图形界面，安装完之后，登陆界面是文字界面。

只有root一个帐号，所以先用root登陆。

测试网络：

```
#ping www.baidu.com
```

会报错：`unknown host`

修改文件：

```
#vi /etc/sysconfig/network-scripts/ifcfg-eth0
```

把`ONBOOT=no`改为`ONBOOT=yes`，`NM_CONTROLLED=yes`改为`NM_CONTROLLED=no`。

重启系统：

```
#reboot
```

或重启网络：

```
#service network restart
```

再检测网络：

```
#ping www.baidu.com
```

如果没问题，会有相应的应答信息。如果还是`unknown host`，试一下修改`resolv.conf`：

```
#vi /etc/resolv.conf
```

加一行：`nameserver 8.8.8.8`。

网卡配置文件说明：

```
DEVICE=eth0 #指出设备名称
NM_CONTROLLED=yes #network mamager的参数，实时生效，不需要重启
ONBOOT=yes #设置为yes，开机自动启用网络连接
IPADDR=192.168.21.129 #IP地址
BOOTPROTO=none #设置为none禁止DHCP，设置为static启用静态IP地址，设置为dhcp开启DHCP服务
NETMASK=255.255.255.0 #子网掩码
DNS1=8.8.8.8 #第一个dns服务器
TYPE=Ethernet #网络类型为：Ethernet
GATEWAY=192.168.21.2 #设置网关
DNS2=8.8.4.4 #第二个dns服务器
IPV6INIT=no #禁止IPV6
USERCTL=no #是否允许非root用户控制该设备，设置为no，只能用root用户更改
HWADDR=00:0C:29:2C:E1:0F #网卡的Mac地址
PREFIX=24
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
NAME="System eth0" #定义设备名称
```

3.安装gcc

```
#yum -y install gcc gcc-c++ kernel-devel
```

4.安装桌面

如果需要图形界面登陆，可以安装这个。图形界面有GNOME和KDE，GNOME高效，KDE华丽更耗资源。

```
#yum -y groupinstall "X Window System"
#yum -y groupinstall "Desktop"  #这是GNOME
#yum -y groupinstall "KDE Desktop"  #这是KDE
```

更改默认启动项：

```
#vi /etc/inittab
```

把`id:3:initdefault:`改为`id:5:initdefault:`。3表示终端启动，5表示图形桌面启动。如果已经图形桌面登陆，可以用`Ctrl+Alt+F1~F7`来切换不同的终端及返回图形桌面。

重启：

```
#reboot
```

由于还只是一个root帐号，而root帐号不能直接登陆图形界面，按提示添加新用户。

启动X Window：

```
#startx
```

安装firefox：

```
#yum -y install firefox
```

要返回文本模式，也可以通过命令：

```
#init 3
```

5.安装中文输入法

```
#yum -y groupinstall "Input Methods"
```

支持中文：

```
#yum -y groupinstall "Chinese support"
```

安装monaco字体（先安装wget）：

```
#yum install wget
#mkdir /usr/share/fonts/monaco
#cd /usr/share/fonts/monaco
#wget -c https://github.com/todylu/monaco.ttf/blob/master/monaco.ttf?raw=true
#chmod 644 monaco.ttf
#yum install mkfontscale
#mkfontscale
#mkfontdir
#reboot
```

6.ssh远程登陆

```
$ifconfig
```
记下IP。

查看ssh是否已经安装：

```
#rpm -qa | grep ssh
```

如果还没有安装，则安装：

```
#yum -y install openssh-server
```

查看sshd是否已经运行：

```
#netstat -antp | grep sshd
```

没有运行则启动：

```
#service sshd restart
```

同时设置一下开机自动启动：

```
#chkconfig sshd on
```

为了省事，把iptables服务都停了，并设置为开机不启动：

```
#service iptables status
#service iptables stop
#chkconfig iptables off
```

禁用SELinux：

```
#vi /etc/selinux/config
```

把`SELINUX`改为`disabled`：

```
SELINUX=disabled
```

修改主机名：

```
#vi /etc/sysconfig/network
```

改为：

```
HOSTNAME=abchost
```

添加IP地址与主机名的映射：

```
#vi /etc/hosts
```

增加一行：

```
xxx.xxx.xxx.xxx abchost abchost
```

IP就是用`ifconfig`查看到的IP。

设置普通用户的sudo权限：

```
#visudo
```

找到行：`root ALL(ALL) ALL`，并在它下面增加一行：`user ALL(ALL) ALL`。

重启：

```
#reboot
```

重启网络：

```
#service network restart
```

然后在Windows下，需要管理员权限，打开文件：`C:\Windows\system32\drivers\etc\hosts`，增加一行：

```
xxx.xxx.xxx.xxx abchost
```
IP就是用`ifconfig`查看到的IP。

在Linux与Windows之间互传文件可以用[WinSCP](https://winscp.net/eng/index.php)，远程登陆可以用[PuTTY](https://www.putty.org/)。
