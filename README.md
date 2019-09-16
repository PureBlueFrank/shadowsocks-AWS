亚马逊云EC2 Linux服务器上安装shadowsocks服务端

前要

亚马逊云AWS使用教程：https://amazonaws-china.com/cn/getting-started/?sc_channel=em&sc_campaign=wlcm&sc_publisher=aws&sc_medium=em_wlcm_1&sc_detail=wlcm_1d&sc_content=other&sc_country=global&sc_geo=global&sc_category=mult&ref_=pe_1679150_261538020

亚马逊云EC2个人主页：https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#Home:

AWS主页：https://us-west-2.console.aws.amazon.com/console/home?region=us-west-2

SSH登录EC2主机：
ssh -i "LinuxKeyPair.pem" ec2-user@ec2-34-213-115-240.us-west-2.compute.amazonaws.com
或 ssh -i ~/.ssh/LinuxKeyPair.pem ec2-user@34.213.115.240

如果系统是裸机需要安装Python

1 .在 CentOS 7 中安装 Python 之前，请确保系统中已经有了所有必要的开发依赖如gcc环境等：
$ sudo yum -y groupinstall development
$ sudo yum -y install zlib-devel
2 .安装或升级python版本
（1）下载源码包,后面的下载链接直接在python官网找的，如果想安装更高的版本自行更换
    $ wget https://www.python.org/ftp/python/2.7.10/Python-2.7.10.tgz
（2）解压压缩包        
    $ tar xvf Python-2.7.10.tgz
（3）指定安装路径     
    $ cd Python-2.7.10
    $ ./configure --prefix=/usr/local/python2
（4）编译并安装
    $ sudo make
    $ sudo make install
（5）创建链接将python指向2.7版本
    $ sudo rm -f /usr/bin/python
    $ sudo ln -s /usr/local/python2/bin/python /usr/bin/python

3.安装pip
    $ sudo yum install python-pip

4.安装shadowsocks
    $ sudo pip install shadowsocks

5.创建ss配置文件 
    $ sudo mkdir /etc/shadowsocks
    $ sudo vim /etc/shadowsocks/ss.json
     配置文件内容：
     {     "server":"0.0.0.0",     "server_port":443,     "local_address":"127.0.0.1",     "local_port":1080,     "password":"www.jianshu.com/u/e02df63eaa87",     "timeout":300,     "method":"aes-256-cfb",     "fast_open":false,     "workers": 1     }
配置字段	说明
server	服务端监听地址(IPv4或IPv6)
server_port	服务端端口，一般为443
local_address	本地监听地址，缺省为127.0.0.1
local_port	本地监听端口，一般为1080
password	用以加密的密匙
timeout	超时时间（秒）
method	加密方法，默认为aes-256-cfb，更多请查阅Encryption 
fast_open	是否启用TCP-Fast-Open，true或者false
workers	
worker数量

6.启动Shadowsocks
启动：sudo ssserver -c /etc/shadowsocks/ss.json -d start 停止：sudo ssserver -c /etc/shadowsocks/ss.json -d stop 重启：sudo ssserver -c /etc/shadowsocks/ss.json -d restart
服务器上关于shadowsocks的一些命令：https://blog.whsir.com/post-1045.html
7、设置SS为开机自启动
 $ sudo vi /etc/rc.local
加入：sudo ssserver -c /etc/shadowsocks/ss.json -d start

8、BBR加速shadowsocks
安装完成以后，发现其实速度可能并不是很快，可以进行加速，加速方法也有不少，好像都对内核有要求，BBR也是让我成功加速的一种，输入命令：
$ wget –no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
$ chmod +x bbr.sh
$ ./bbr.sh
安装完，重启一下shadowsocks，应该就ok了。加速参考：https://www.zybuluo.com/Tyhj/note/1172287

9、本地设备连接到Shadowsocks服务器
到GitHub下载最新的客户端：
Windows客户端下载地址
macOS客户端下载地址 ：https://github.com/shadowsocks/ShadowsocksX-NG/releases
Linux客户端下载地址

安装之后，添加服务器，地址为AWS的外网地址，登录AWS控制台，查看正在运行中的实例，找到公有ip。 端口号为刚才配置Shadowsocks服务器时的端口号，密码也是刚才配置的(当然密码可以自行设置)，设置完之后保存。

关于Windows下使用Shadowsocks的方法，还请自行搜索。

10、防止AWS到期或其他原因扣费
为了防止流量超出或者此悲剧发生。
需要设置账单警报，前面已经介绍了。
还有就是在安全组下面设置入站和出站规则。
注意: 到期前把你账户下所有AWS实例关闭并销毁。




注意：亚马逊AWS EC2 ping不通的原因

如果把入站规则改成所有的流量 ping通当然没有问题



打开安全组 加上所有的流量这一要求就可以







其他翻墙资料友情链接：https://www.jianshu.com/p/b5db24490e18
