# app
## 1.WireGuardGen
### 1.1. 功能说明
自动生成Wireguard配置文件
### 1.2. 生成文件

![20220518232306](https://user-images.githubusercontent.com/13865936/169079771-55ffa086-8623-45cd-84da-856ed11dc7c4.png)
```
wg0.conf  服务端配置文件
wg_c_2 客户端配置文件2
wg_c_3 客户端配置文件3
wg_client.zip 客户端所有配置文件的压缩包
```
### 1.3. 使用
```
// 使用前添加可执行权限
chmod +x WireGuardGen 
```
#### 1.3.1. 帮助
```
root@ecs-32:~# ./WireGuardGen -h
optional arguments:
  -h, --help    show this help message and exit
  -v            版本
  -u U          url:服务器域名/ip
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
#### 1.3.2 生成
```
root@ecs-32:~# ./WireGuardGen -u 192.168.1.1
```
### 1.4 配置文件内容
wg0.conf
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
```
wg_c_2.conf
```
[Interface]
PrivateKey = TbZhZCnFqS4hvfWAMIwhc+BQPmlD3SjR4u07yKGx/0s=
ListenPort = 51820
Address = 10.13.14.2/32
DNS = 114.114.114.114
MTU = 1420

[Peer]
PublicKey = WAMIwhc+BQTbZhZCnFqS4hvfPmlD3SjR4u07yKGx/0s=
AllowedIPs = 0.0.0.0/0
PersistentKeepalive  = 25
Endpoint = 192.168.1.1:51820


# 启动/停止
# wg-quick up wg_c_2
# wg-quick down wg_c_2
# 开机启动/不启动
# systemctl enable wg-quick@wg_c_2
# systemctl disable wg-quick@wg_c_2

```
