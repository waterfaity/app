######  [WireGuardGen ](https://github.com/waterfaity/app/blob/main/WireGuardGen)

## 1. 功能说明
自动生成Wireguard配置文件

环境搭建篇见 : >> [Wireguard - 简书 (jianshu.com)](https://www.jianshu.com/p/92bea89bf819)
## 2. 生成文件目录结构

![](https://upload-images.jianshu.io/upload_images/4875198-abece2bbc805e30e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
#说明 :
wg0.conf  服务端配置文件
wg_c_2    客户端配置文件2
wg_c_3    客户端配置文件3
...
wg_client.zip 客户端所有配置文件的压缩包
```
## 3. 使用
```
// 使用前添加可执行权限
chmod +x WireGuardGen 
```
##### 3.1. 帮助
```
./WireGuardGen -h
// 输出如下:
optional arguments:
  -h, --help    show this help message and exit
  -v            版本
  -u U          url:服务器域名/ip (必选)
  -p P          port:监听端口号,默认:51820
  -d D          dns,默认:114.114.114.114
  -sub SUB      internal_subnet:子网掩码,默认:10.13.13.0/24
  -pka PKA      persistent_keepalive:保持持久连接,单位:秒,默认:25
  -mtu MTU      MTU:最大传输单元,单位:B,默认:1420
  -ai AI        allowed_ips:允许的IPs,默认:10.13.13.0/24,注:默认情况下会随internal_subnet改变
  -n N          gen_num:生成配置文件个数,默认:20
  -f F          gen_from:配置文件从from开始生成,默认:0
  -cn CN        client_name:生成客户端配置文件名字,默认:wg_c_
  -czn CZN      client_zip_name:生成客户端压缩文件名字,默认:wg_client
  -sn SN        server_name:生成服务端配置文件名字,默认:wg0
  -spubk SPUBK  server_public_key:服务端公钥,默认:自动生成
  -sprik SPRIK  server_private_key:服务端私钥,默认:自动生成
  -saddr SADDR  server_address:服务器地址,默认:10.13.13.1,注:默认情况下会随internal_subnet改变
```
##### 3.2 生成
```
root@ecs-32:~# ./WireGuardGen -u 192.168.1.1
```
## 4. 生成文件内容如下
wg0.conf(服务端配置文件)
```
#pub:BQTbZhZCnFqPmlD3SjR4u0S4hvfWAMIwhc+7yKGx/0s=
[Interface]
Address = 10.13.14.4
ListenPort = 51820
PrivateKey = S4hvfWAMIwhc+BQTbZhZCnFqPmlD3SjR4u07yKGx/0s=
DNS = 114.114.114.114
MTU = 1420
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE



# pri:TbZhZCnFqS4hvfWAMIwhc+BQPmlD3SjR4u07yKGx/0s=
[Peer]
PublicKey = WAMIwhc+BQTbZhZCnFqS4hvfPmlD3SjR4u07yKGx/0s=
AllowedIPs = 10.13.14.2/32


# pri:WAMIwhc+BQTbZhZCnFqS4hvfPmlD3SjR4u07yKGx/0s=
[Peer]
PublicKey = +BQTbZhZCnFqSWAMIwhc4hvfPmlD3SjR4u07yKGx/0s=
AllowedIPs = 10.13.14.3/32

...

```
wg_c_2.conf(客户端配置文件)
```
[Interface]
PrivateKey = TbZhZCnFqS4hvfWAMIwhc+BQPmlD3SjR4u07yKGx/0s=
ListenPort = 51820
Address = 10.13.14.2/32
DNS = 114.114.114.114
MTU = 1420

[Peer]
PublicKey = WAMIwhc+BQTbZhZCnFqS4hvfPmlD3SjR4u07yKGx/0s=
AllowedIPs = 10.13.13.0/24
PersistentKeepalive  = 25
Endpoint = 192.168.1.1:51820


# 启动/停止
# wg-quick up wg_c_2
# wg-quick down wg_c_2
# 开机启动/不启动
# systemctl enable wg-quick@wg_c_2
# systemctl disable wg-quick@wg_c_2

```
## 5. 其它说明
###### 5.1 -n参数
```  
  默认20;(注:最大限制到255.255.255.254)
  1个服务端配置文件 :wg0.conf;
  19个客户端配置文件:wg_c_2.conf  ...  wg_c_19.conf;  
```
###### 5.2 -f参数
```  
  从某个位置开始生成,
  如果想接上次客户端编号(wg_c_19),对应编号减1,即19-1=18. 此时会从wg_c_20开始生成. 
```
###### 5.3 ip生成规则
```
根据子网掩码规则配置.第一个ip保留不用.第二个作为服务器的ip,第三个开始作为客户端ip.
如: 子网掩码 10.13.13.0/24 对应范围: 10.13.13.0 ... 10.13.13.255
10.13.13.0 保留
10.13.13.1 服务端ip,对应文件wg0.conf,(Docker:linuxserver/wireguard 中默认配置文件名)
10.13.13.2 客户端ip,对应文件wg_c_2.conf,(如子网规则开始为0,则可以保证文件编号和末尾ip对应到255)
... 
注:不将10.13.13.255作为最后一个客户端ip,长度根据-n参数.

```

