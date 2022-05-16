实战攻击非法网站
==================

攻击顺序
---------------------

    - 选择目标网站
    - ping 目标网站地址 获得网站ip
    - 选择攻击原理
        - 如何实现服务器拥堵瘫痪
            - 1.制作病毒 传播病毒 控制N台电脑同时访问目标站 造成目标站的网络拥堵 
            - 2.租用一个 支持IP伪装的[大流量服务器]
                - IP伪装: 把一个IP地址同时分裂成N个全球不同IP地址
            - 3.连接[大流量服务器]

>>>chmod 777 ntp
>>>./ntp ip 80 ntpcan.txt 2 -1 120

** 法则: **
    - 1.只攻击非法网站; 
    - 2.不攻击你所在国家的非法网站



ssr 一键搭建
==================

ssr 一键部署:
---------------------
::

    wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssrmu.sh && chmod +x ssrmu.sh && bash ssrmu.sh

使用bbr加速
---------------------

>>> wget https://d.kxxzz.com/sh/tcp.sh    `2019年脚本`
>>> wget https://d.kxxzz.com/sh/tcp2020/tcp.sh   `2020年脚本`
>>> sudo `使用root权限`
>>> chmod +x tcp.sh
>>> ./tcp.sh

**对应的内核版本应与对应的加速管理模块匹配,否则无法正常使用.**

详情参考: https://www.kxxzz.com/xx/367.html



v2ray 一键搭建
==================

centos版 一键部署:
---------------------
::

    bash <(curl -sL https://raw.githubusercontent.com/hijkpw/scripts/master/centos_install_v2ray.sh)

参考:
^^^^^^^^^^^^^^^^^^^^^

1. 查看v2ray配置参数/运行状态：bash <(curl -sL https://raw.githubusercontent.com/hijkpw/scripts/master/centos_install_v2ray.sh) info;

2. v2ray管理命令：启动：systemctl start v2ray，停止：systemctl stop v2ray，重启：systemctl restart v2ray;

3. 更改端口、alterid最简单的办法：重新运行一键脚本；

4. 更新v2ray到最新版：重新运行一键脚本

5. 卸载v2ray： bash <(curl -sL https://raw.githubusercontent.com/hijkpw/scripts/master/centos_install_v2ray.sh) uninstall

详情参考: https://ssrvps.org/archives/1023


ubuntu版 一键部署:
-------------------------
::

    bash <(curl -sL https://raw.githubusercontent.com/hijkpw/scripts/master/ubuntu_install_v2ray.sh)

参考:
^^^^^^^^^^^^^^^^^^^

1. 查看v2ray配置参数/运行状态：bash <(curl -sL https://raw.githubusercontent.com/hijkpw/scripts/master/centos_install_v2ray.sh) info;

2. v2ray管理命令：启动：systemctl start v2ray，停止：systemctl stop v2ray，重启：systemctl restart v2ray;

3. 更改端口、alterid最简单的办法：重新运行一键脚本；

4. 更新v2ray到最新版：重新运行一键脚本

5. 卸载v2ray： bash <(curl -sL https://raw.githubusercontent.com/hijkpw/scripts/master/centos_install_v2ray.sh) uninstall


域名伪装一键脚本,参考:
---------------------------

::

    https://ssrvps.org/archives/1023

