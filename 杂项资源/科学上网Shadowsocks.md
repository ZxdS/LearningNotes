# 科学上网之 Shadowsocks 安装及优化加速

最近国内的网络形式越来越严峻，在日益高筑的围墙之下，掌握一门穿墙之术越来越成为需要。相对于 VPN 而已， Shadowsocks 更为轻量级，安装配置过程极其简单。而客户端也可以在windows、mac、iOS和android上轻松运行，被人们所深深喜爱。感谢 [@clowwindy](https://twitter.com/clowwindy)，带给我们一款如此好用的[开源软件](https://github.com/shadowsocks/shadowsocks)。下面说说 Shadowosocks 的安装和优化。1. 服务端安装官方推荐 Ubuntu 14.04 LTS 作为服务器以便使用 TCP Fast Open。服务器端的安装非常简单。Debian / Ubuntu:
`apt-get install python-pippip install shadowsocks`CentOS:
`yum install python-setuptools && easy_install pippip install shadowsocks`然后直接在后台运行：
`ssserver -p 8000 -k password -m rc4-md5 -d start`当然也可以使用配置文件进行配置，方法创建`/etc/shadowsocks.json`文件，填入如下内容：
`{    "server":"my_server_ip",    "server_port":8000,    "local_address": "127.0.0.1",    "local_port":1080,    "password":"mypassword",    "timeout":300,    "method":"rc4-md5"}`然后使用配置文件在后台运行：
`ssserver -c /etc/shadowsocks.json -d start`如果要停止运行，将命令中的`start`改成`stop`。TIPS: 加密方式推荐使用`rc4-md5`，因为 RC4 比 AES 速度快好几倍，如果用在路由器上会带来显著性能提升。旧的 RC4 加密之所以不安全是因为 Shadowsocks 在每个连接上重复使用 key，没有使用 IV。现在已经重新正确实现，可以放心使用。更多可以看 [issue](https://github.com/clowwindy/shadowsocks/issues/178)。2. 客户端安装客户端安装比较入门，这里就不说了，可以参考[这篇文章](http://www.jianshu.com/p/08ba65d1f91a)。3. 加速优化下面介绍几种简单的优化方法，也是比较推荐的几种，能够得到立竿见影的效果。当然还有一些黑科技我没提到，如有大神路过，也可留言指出。3.1 内核参数优化首先，将 Linux 内核升级到 3.5 或以上。**第一步，增加系统文件描述符的最大限数**编辑文件 `limits.conf`
`vi /etc/security/limits.conf`增加以下两行
`* soft nofile 51200* hard nofile 51200`启动shadowsocks服务器之前，设置以下参数
`ulimit -n 51200`**第二步，调整内核参数**
修改配置文件 `/etc/sysctl.conf`
`fs.file-max = 51200net.core.rmem_max = 67108864net.core.wmem_max = 67108864net.core.netdev_max_backlog = 250000net.core.somaxconn = 4096net.ipv4.tcp_syncookies = 1net.ipv4.tcp_tw_reuse = 1net.ipv4.tcp_tw_recycle = 0net.ipv4.tcp_fin_timeout = 30net.ipv4.tcp_keepalive_time = 1200net.ipv4.ip_local_port_range = 10000 65000net.ipv4.tcp_max_syn_backlog = 8192net.ipv4.tcp_max_tw_buckets = 5000net.ipv4.tcp_fastopen = 3net.ipv4.tcp_rmem = 4096 87380 67108864net.ipv4.tcp_wmem = 4096 65536 67108864net.ipv4.tcp_mtu_probing = 1net.ipv4.tcp_congestion_control = hybla`修改后执行 `sysctl -p` 使配置生效3.2 锐速锐速是一款非常不错的TCP底层加速软件，可以非常方便快速地完成服务器网络的优化，配合 ShadowSocks 效果奇佳。目前锐速官方也出了永久免费版本，适用带宽20M、3000加速连接，个人使用是足够了。如果需要，先要在锐速官网注册个账户。然后确定自己的内核是否在锐速的支持列表里，如果不在，请先更换内核，如果不确定，请使用 [手动安装](http://my.serverspeeder.com/w.do?m=lslm)。确定自己的内核版本在支持列表里，就可以使用以下命令快速安装了。
`wget http://my.serverspeeder.com/d/ls/serverSpeederInstaller.tar.gztar xzvf serverSpeederInstaller.tar.gzbash serverSpeederInstaller.sh`输入在官网注册的账号密码进行安装，参数设置直接回车默认即可，
最后两项输入 y 开机自动启动锐速，y 立刻启动锐速。之后可以通过`lsmod`查看是否有appex模块在运行。到这里还没结束，我们还要修改锐速的3个参数，`vi /serverspeeder/etc/config`
`rsc="1" #RSC网卡驱动模式  advinacc="1" #流量方向加速  maxmode="1" #最大传输模式`digitalocean vps的网卡支持rsc和gso高级算法，所以可以开启`rsc="1"`，`gso="1"`。重新启动锐速
`service serverSpeeder restart`3.3 net-speedernet-speeder 原理非常简单粗暴，就是发包翻倍，这会占用大量的国际出口带宽，本质是损人利己，不建议使用。**(1) Ubuntu/Debian 下安装依赖包**
`apt-get install libnet1apt-get install libpcap0.8apt-get install libnet1-devapt-get install libpcap0.8-dev`**(2) Centos 下安装依赖包**
需要配置 epel 第三方源。下载 epel ：<http://dl.fedoraproject.org/pub/epel/> 。例如，Centos 7 x64：
`wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpmrpm -ivh epel-release-7-5.noarch.rpmyum repolist`然后安装依赖包：
`yum install libnet libpcap libnet-devel libpcap-devel`**(3) 下载官方的 tar.gz 压缩包。解压安装运行：**
`wget http://net-speeder.googlecode.com/files/net_speeder-v0.1.tar.gz tar zxvf net_speeder-v0.1.tar.gzcd net_speederchmod 777 *sh build.sh -DCOOKED`首先你需要知道你的网卡设备名，可以使用 ifconfig 查看。假设是eth0，那么运行方法是:
`./net_speeder eth0 "ip"`关闭 net-speeder
`killall net_speeder`哦，对了，作者已经将 net-speeder 迁移到 [GitHub](https://github.com/snooda/net-speeder) 了，感兴趣的可以关注、贡献。以上几种方法是我用过的几种比较有效的加速方法。有任何错误之处还请在下面留言指出。**如果你不想折腾服务端安装和优化，你可以使用虫洞咖啡厅提供的免费 shadowsocks 服务。**

 