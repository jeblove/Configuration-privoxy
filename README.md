# Configuration-privoxy

配置Privoxy

本文中使用路由器固件OpenWRT

1、安装Privoxy
路由器管理页面，先更新软件包列表
安装本体 privoxy (必须)
安装界面 luci-app-privoxy (可选)
界面安装完，【服务】里有【Privoxy WEB proxy】

2、配置Privoxy

界面方式：
·Files and Directories（文件和目录）：Action Files 删除到只剩一个框，填入 match-all.action。Filter files 和 Trust files 均留空。
·Access Control（访问控制）：Listen addresses 填写 0.0.0.0:8118，Permit access 填写 192.168.0.0/16。Enable action file editor 勾选。
·Miscellaneous（杂项）：Accept intercepted requests 勾选。
·Logging（日志）：全部取消勾选。
点击 Save & Apply。

命令方式：
wget -P /root/tmp https://gitee.com/jeblove/configuration-privoxy/raw/master/privoxy
mv /root/tmp/privoxy /etc/config/privoxy
启动privoxy
/etc/init.d/privoxy start
设置开启启动
/etc/init.d/privoxy enable

3、设置防火墙
进入路由器的防火墙-自定义规则
添加规则

iptables -t nat -N http_ua_drop

iptables -t nat -I PREROUTING -p tcp --dport 80 -j http_ua_drop

iptables -t nat -A http_ua_drop -d 0.0.0.0/8 -j RETURN
iptables -t nat -A http_ua_drop -d 127.0.0.0/8 -j RETURN
iptables -t nat -A http_ua_drop -d 192.168.0.0/16 -j RETURN
iptables -t nat -A http_ua_drop -p tcp -j REDIRECT --to-port 8118
重启防火墙

4、利用privoxy替换ua
在所在的局域网中访问http://config.privoxy.org/edit-actions-list?f=0
点击Edit编辑按钮，Action 那一列中，hide-user-agent 改选为 Enable（绿色），在右侧 User Agent string to send 框中填写 Privoxy/1.0；其它全部选择为 No Change （紫色）。点击 Submit 按钮。

5、确认效果
打开 ua.chn.moe，网页上应该显示 Privoxy/1.0。

参考于：
https://catalog.chn.moe/%E6%8A%80%E6%9C%AF/OpenWrt/%E5%9C%A8%E5%8E%A6%E5%A4%A7%E5%AE%BF%E8%88%8D%E5%AE%89%E8%A3%85%E8%B7%AF%E7%94%B1%E5%99%A8/
https://lilynas.com/archives/644/


