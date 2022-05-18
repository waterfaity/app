# app
###### WireGuardGen

```
// 使用前添加可执行权限
chmod +x WireGuardGen 
```
```
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
