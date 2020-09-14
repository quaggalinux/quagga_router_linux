# quagga_router_linux
quagga路由器软件在linux安装设置（ubuntu,CentOS,Debian,RedHat）

什么是Quagga

  Quagga软件原名是Zebra是由一个日本开发团队编写的一个以GNU版权方式发布的软件。Quagga项目开始于1996年，可以使用

Quagga将linux机器打造成一台功能完备的路由器。

  Quagga支持的路由协议非常广泛。

  Quagga能够同时支持RIPv1、RIPv2、RIPng、OSPFv2、OSPFv3、BGP-4和 BGP-4+等诸多TCP/IP协议。其中：

  RIPv1、RIPv2、OSPFv2适用于Ipv4的自治域系统内部网络路由协议。

  BGP-4是用于Ipv4的自治域系统之间的外部网络路由协议。

  RIPng、OSPFv3、BGP-4+主要扩展对Ipv6的支持。

Quagga的特性

  模块化设计：Quagga基于模块化方案的设计，即对每一个路由协议使用单独的守护进程。

  运行速度快：因为使用了模块化的设计，使得Quagga的运行速度比一般的路由选择程序要快。

  可靠性高：在所有软件模块都失败的情况下，路由器可以继续保持连接并且daemons也会继续运行。故障诊断不必离线的状态

下被诊断和更正

  支持Ipv6：Quagga不仅支持Ipv4，还支持Ipv6。

Quagga的运行机制

  由于Quagga采用模块化的设计，因此Quagga运行时要运行多个守护进程，包括ripd ripngd ospfd ospf6d bgpd 和Zebra。

  其中，Zebra守护进程用来更新内核的路由表，而其他的守护进程负责进行相应路由选择协议的路由更新。

Quagga的好处

  就路由器而论，虽然有各种硬件可用，但是费用较高。所以想到用一个运行Linux系统构件的功能丰富的路由器作为代替。

Quagga路由守护程序已经使这一切变为现实。因为Quagga支持Ipv4、Ipv6和其他各式各样的协议，所以能够满足通常所有的路由

需要。

  使用Quagga的另一个好处是，这个软件配置的很多方面跟Cisco的IOS配置几乎完全相同，如果你在Cisco IOS环境中工作，可

以轻松的过渡到Quagga系统，同时，使用Quagga特能让你积累起丰富的类似于使用Cisco IOS路由器的经验和知识。

  总之，现在完全可以拿一台PC机来完成一些必须用昂贵的Cisco路由器才能完成的比较复杂的路由协议处理控制功能。

  由于各个linux发行版都有quagga路由器软件的安装包，所以下面用ubuntu为例进行安装配置，其他CentOS，Debian，RedHat

安装配置过程大同小异

Ubuntu 14.04/16.04 安装配置quagga路由器软件：

切换到root用户
$su
#sudo apt-get update
#sudo apt-get install make
#sudo apt-get install gcc-multilib
#sudo apt-get install build-essential
#cd /
#sudo apt-get install quagga
 
在Linux系统中，路由表和转发数据包的功能都是Linux内核所提供。在我们的Ubuntu系统中，默认情况下是没有开启数据包转发

功能的。开启方式：
#sudo nano /etc/sysctl.conf

在打开的文件最后一行加入并保存退出

net.ipv4.ip_forward = 1

运行下一个命令来触发内核采用新配置
#sudo sysctl -p

查状态
#sudo sysctl net.ipv4.ip_forward

output：
net.ipv4.ip_forward = 1
 
Quagga配置文件例子在/usr/share/doc/quagga/examples/中，每个功能对应各自配置文件，需将其拷贝至/etc/quagga并改名及

权限

root@bridge:/#cp /usr/share/doc/quagga/examples/*.sample /etc/quagga/
root@bridge:/# cd /etc/quagga
root@bridge:/etc/quagga#mv babeld.conf.sample babeld.conf
root@bridge:/etc/quagga#mv isisd.conf.sample isisd.conf 
root@bridge:/etc/quagga#mv ospfd.conf.sample ospfd.conf 
root@bridge:/etc/quagga#mv ripngd.conf.sample ripngd.conf 
root@bridge:/etc/quagga#mv zebra.conf.sample zebra.conf 
root@bridge:/etc/quagga#mv bgpd.conf.sample bgpd.conf 
root@bridge:/etc/quagga#mv ospf6d.conf.sample ospf6d.conf 
root@bridge:/etc/quagga#mv ripd.conf.sample ripd.conf 
root@bridge:/etc/quagga#mv vtysh.conf.sample vtysh.conf
root@bridge:/etc/quagga#chown quagga:quagga *

选择要启动的路由协议Edit which routing protocols are to run: 

sudo nano /etc/quagga/daemons

要启动的路由协议后的 no 改成 yes

zebra=yes  //zebra也要选yes
bgpd=yes
ospfd=yes
ospf6d=yes
ripd=yes
ripngd=yes
isisd=yes
babeld=yes

编辑Telnet的接入以及余下的路由Edit telnet access and the retaining of routes over restarts: 

sudo nano /etc/quagga/debian.conf 

在打开的文件修改并保存退出

zebra_options=" --daemon -A 127.0.0.1 -P 2601 -u quagga -g quagga"
bgpd_options=" --daemon -A 127.0.0.1 -P 2605 -u quagga -g quagga --retain -p 179"
ospfd_options=" --daemon -A 127.0.0.1 -P 2604 -u quagga -g quagga"
ospf6d_options=" --daemon -A ::1 -P 2606 -u quagga -g quagga"
ripd_options=" --daemon -A 127.0.0.1 -P 2602 -u quagga -g quagga"
ripngd_options=" --daemon -A ::1 -P 2603 -u quagga -g quagga"
isisd_options=" --daemon -A 127.0.0.1 -P 2608 -u quagga -g quagga"
babeld_options=" --daemon -A 127.0.0.1 -P 2609 -u quagga -g quagga"

如果文件里面有类似如下的行，就都加#号注释掉
zebra_options=" --daemon -A 127.0.0.1"


重启quagga，使配置生效

root@bridge:/#sudo /etc/init.d/quagga restart


所有的telnet密码及en密码缺省都是zebra，如果改变了任何进程的密码，都需要运行上面的语句重启服务才生效！！！


注意：如果使用Telnet进行配置，可能需要设置密码但不一定是必须，否则会报错
如下所示：
#telnet localhost zebra
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Vty password is not set.
Connection closed by foreign host.
因此需要先设置密码：

为zebra进程设置telnet密码
#sudo echo "password 123" >> /etc/quagga/zebra.conf
或 #sudo nano /etc/quagga/zebra.conf

在打开的文件最后一行加入并保存：
password 123

从上面打开的文件可以看到telnet进去路由器后enable(en)的密码是zebra


任何一个进程配置文件密码更改完成后都要需要重启服务，否则新密码不起作用

#sudo /etc/init.d/quagga restart

为ripd进程设置telnet密码（理论上配置样板文件缺省有密码不用改，都是zebra）
sudo echo "password 123" >> /etc/quagga/ripd.conf
或 sudo nano /etc/quagga/ripd.conf

在打开的文件最后一行加入并保存：
password 123

进入rip路由协议配置需要#telnet 127.0.0.1 ripd   (在zebra的全局config t不能配置路由协议！！！)

现在可以通过zebra来配置一个IP地址了，重复以上步骤就可以完成BGP (bgpd.conf), OSPF (ospfd.conf) 等所有配置。

Note that quagga is Cisco-like command so you should have familiarity with the Cisco command.
quagga就是与Cisco的命令行是基本一样的，也可以用简写如show run可以写成sh ru
最大区别是Cisco在全局直接配置各种路由协议，quagga必须每个路由协议单独telnet进去配置


连接zebra（相当于全局配置）：
telnet localhost zebra
telnet 127.0.0.1 zebra
telnet密码是zebra（>号提示符），然后en密码是zebra（#号提示符）

config t进入配置模式Router(config)#
Router(config)# int ens33 进入interface配置模式
Router(config-if)#

exit退出到上一层命令提示符，直至完全退出quagga回到linux提示符

在如何一层提示符下都可以打 ? 号显示可用命令，命令后都可以跟 ? 号显示后面的选项

