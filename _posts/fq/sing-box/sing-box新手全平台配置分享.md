[sing-box 新手全套观看指南](https://linux.do/t/topic/172323)
[原文](https://linux.do/t/topic/165799?filter=summary)

> **sing-box 全套观看指南已经完全，如果想从全局了解知识体系的，请先到观看指南中浏览各文章的知识点**

折腾了一周，算是把 sing-box 常用的功能都测试了一遍，目前基本算是得心应手了。

之前分享过两个配置，不过评论区还是有人觉得难以上手，所以昨晚花了些时间写了一份全平台的配置文件，基本稍作修改即可使用。不过还是需要强调的是，任何配置不可能适用于所有情况，也不可能完全小白，最起码要有点搭建的基础经验。

## 该配置的优势和支持的协议

> 本系列配置介绍：  
> 1、该配置完全适配了 vps 用户。如果是机场用户，你需要手动添加机场节点，或者采用订阅转换的方式，再把机场节点信息拷贝到本系列配置中，之前分享的两篇教程中有讲解。  
> 2、目前测试完成的平台：服务端，win端，iOS端；（sing-box 所有平台配置文件结构都是相似的，所以即便那些没测试的平台，只要把已经测试好的平台，稍微修改一下入站即可使用。）  
> 3、配置文件采用 realip 的 DNS 解析模式，使用 sing-box 1.9+内核的新功能 ecs，解决国内小众网站访问慢，国内没有套国外cdn访问慢，解决fakeip会导致游戏无法连接情况；  
> 4、DNS 解析过程中，realip 理论上要比 fakeip 模式慢一些。如果使用 DOH 这种会维持长链接，解析时间和裸UDP相当，平时使用香港这种延迟低的节点，再加上 realip 还有缓存和预解析，realip 对比 fakeip 两者几乎无法感知；  
> 5、解决喜闻乐见的DNS泄露问题；  
> 6、傻瓜式部署，新手可以直接使用，不需要再折腾，老手也可以借鉴其中配置的模块；

sing-box 目前支持绝大多数的协议，当然一些刚出的协议，它也会立马跟进，本配置只采用了比较常用的协议类型。这里申明一下：不同地区，不同运营商，以及不同的 IDC 运营方式都有区别，*配置中的协议在你场景不合适时候，你只需要选择更换其中一款即可*。

> 1、Shadowsocks 2022  
> 2、VLESS+Vision+REALITY  
> 3、VMess+WebSocket  
> 4、VMess+WebSocket+TLS  
> 5、Hysteria2  
> 6、TUIC V5  
> 7、Shadowsocks 2022 + Brutal  
> 8、VLESS+Vision+REALITY+Brutal  
> 9、VMess+WebSocket+Brutal  
> 10、VMess+WebSocket+TLS+Brutal

这里给大家讲解一下 *Brutal 是什么*，完整的应该是 TCP Brutal， TCP Brutal 是 Hysteria 中的同名拥塞控制算法移植到 TCP 的版本，作为一个 Linux 内核模块。*因为是 TCP 类型，不会被运营商 QOS，更加适合垃圾线路的vps*，不过可惜支持的客户端比较少，只有在 sing-box 中最通用。

还有 sing-box 还支持多用户配置，ECH 配置等等，配置中就不写了，新手用不到，老手可以采用。

## 服务端安装教程和配置

下面脚本基于 ubuntu/debian 最新版本，其他版本大家自己适配；

### TCP Brutal安装

> ubuntu/debian  
> 官方脚本安装

`bash <(curl -fsSL https://tcp.hy2.sh/)`

输出 Congratulation! tcp-brutal v1.0.0 has been successfully installed and loaded on your server.

`sudo dkms status`

输出 tcp-brutal/1.0.2, 6.1.0-22-cloud-amd64, x86_64: installed

`cat /proc/sys/net/ipv4/tcp_allowed_congestion_control`

输出 reno cubic bbr brutal

### 自签证书 安装
用自签证书的好处就是不需要有自己的域名，需要 **开启允许不安全连接**，为 bing.com 生成一个 100 年的自签证书

```bash
mkdir -p /home/ubuntu/hysteria && openssl ecparam -genkey -name prime256v1 -out /home/ubuntu/hysteria/private.key && openssl req -new -x509 -days 36500 -key /home/ubuntu/hysteria/private.key -out /home/ubuntu/hysteria/cert.pem -subj "/CN=bing.com"
```

### sing-box 安装

版本号自行修改，因为是整理的，就不重新修改为通过临时环境搭建最新版本的教程，需要的大家仔细搜索！

切换root用户。**为 root 用户设置密码:**
```bash
# 为 root 用户设置密码:
sudo passwd root
# 切换到 root 用户:
su root
```

下载安装包

```bash
wget https://github.com/SagerNet/sing-box/releases/download/v1.9.4/sing-box_1.9.4_linux_amd64.deb
```

安装sing-box

```bash
sudo dpkg -i sing-box_1.9.4_linux_amd64.deb
```

查看安装包内容
```bash
dpkg -c sing-box_1.9.4_linux_amd64.deb
```


修改配置文件，如果不习惯使用vim的，直接把配置文件上传覆盖也行；
```bash
sudo vim /etc/sing-box/config.json
```


自启

```bash
sudo systemctl enable sing-box.service
```

查看sing-box状态

```bash
systemctl status sing-box.service
```

重启sing-box状态

```bash
sudo systemctl restart sing-box.service
```

启动sing-box

```bash
sudo systemctl start sing-box.service
```

停止sing-box

```bash
systemctl stop sing-box.service
```

配置文件地址，注意该目录不能存在其他 json 文件

`/etc/sing-box/config.json`

## 各个平台配置文件分享

> 注意点：  
> 1、教程主要还是给新手写的，大佬可以手搓；  
> 2、服务端主要是三个文件，config.json、bing.com.crt、bing.com.key；  
> 3、三个文件存放在 /etc/sing-box 目录下即可；

服务端配置里的参数也是新制作的，大家可以直接使用，不需要修改，也可以自定义为自己的配置；如何自定义为自己的配置，可以看上两篇写的 sing-box 教程，里面有详细的如何自定义，各种协议都列举了一下。如果 sing-box 启动失败，大概率是证书和密钥路径问题，还有就是注意把协议端口开放给防火墙，服务器设置算是搞定了。  
证书下载地址：[https://share.gugu.ee/s/IzNTkzM 32](https://share.gugu.ee/s/IzNTkzM) 密码：IzNTkzM  
不知道能撑多久，随缘！

### 服务端配置文件

> config_server_sing-box.json

```json
{
  "log": {
    "disabled": false,
    "level": "info",
    "timestamp": true
  },
  "inbounds": [
    {
      "type": "shadowsocks",
      "tag": "eu3pkop2-in",
      "listen": "::",
      "listen_port": 37065,
      "sniff": true,
      "sniff_override_destination": true,
      "method": "2022-blake3-aes-256-gcm",
      "password": "ZBdsPBMPLM4vkK7yoS7jQjZmIoqmet3PWhrzxwLharY=",
      "multiplex": {
        "enabled": true,
        "padding": true,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vless",
      "tag": "gthagvb4-in",
      "listen": "::",
      "listen_port": 60316,
      "sniff": true,
      "sniff_override_destination": true,
      "users": [
        {
          "uuid": "695dcbdb-1499-482d-9741-70c1cbac4710",
          "flow": ""
        }
      ],
      "tls": {
        "enabled": true,
        "server_name": "icloud.cdn-apple.com",
        "reality": {
          "enabled": true,
          "handshake": {
            "server": "icloud.cdn-apple.com",
            "server_port": 443
          },
          "private_key": "ILlA7oqf6yPoxQb1PB31nlx09ZB9cHGxOwyVcuVEhkU",
          "short_id": [
            "429e7bbfcca430c4"
          ]
        }
      },
      "multiplex": {
        "enabled": true,
        "padding": true,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vmess",
      "tag": "5izubguu-in",
      "listen": "::",
      "listen_port": 61703,
      "sniff": true,
      "sniff_override_destination": true,
      "transport": {
        "type": "ws",
        "path": "/u4yE78Ky",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      },
      "users": [
        {
          "uuid": "4074d046-1726-49cc-ad45-272b2c664f29",
          "alterId": 0
        }
      ],
      "tls": {
        "enabled": true,
        "server_name": "bing.com",
        "certificate_path": "/etc/sing-box/bing.com.crt",
        "key_path": "/etc/sing-box/bing.com.key"
      },
      "multiplex": {
        "enabled": true,
        "padding": false,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vmess",
      "tag": "c4yndhs8-in",
      "listen": "::",
      "listen_port": 39720,
      "sniff": true,
      "sniff_override_destination": true,
      "transport": {
        "type": "ws",
        "path": "/FQgD78nq",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      },
      "users": [
        {
          "uuid": "7e26c9f6-f993-4865-8488-6b02e634a6d7",
          "alterId": 0
        }
      ],
      "multiplex": {
        "enabled": true,
        "padding": false,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "hysteria2",
      "tag": "d5tlrad8-in",
      "listen": "::",// 表示监听所有可用的 IPv6 地址。如果需要监听所有 IPv4 地址，可以使用 "0.0.0.0"
      "listen_port": 61970,//监听端口。
      "sniff": true,//启用协议探测。
      "sniff_override_destination": true,//用探测出的域名覆盖连接目标地址。
      "up_mbps": 50,//支持的速率，默认不限制。与 `ignore_client_bandwidth` 冲突。
      "down_mbps": 100,
      "obfs": {
        "type": "salamander",//QUIC 流量混淆器类型，仅可设为 `salamander`。
        "password": "41nPRX39cSt6"//QUIC 流量混淆器密码.
      },
      "users": [
        {
          "name": "EQUR9AK+",//Hysteria 用户
          "password": "WqzGKnmZ9UaB"//认证密码。
        }
      ],
      "ignore_client_bandwidth": false,//命令客户端使用 BBR 拥塞控制算法而不是 Hysteria CC。
      "masquerade": "https://www.bing.com",//HTTP3 服务器认证失败时的行为。
      "tls": {
        "enabled": true,
        "server_name": "bing.com",//用于验证返回证书上的主机名，除非设置不安全。它还包含在 ClientHello 中以支持虚拟主机，除非它是 IP 地址。
        "alpn": [//支持的应用层协议协商列表，按优先顺序排列。
          "h3"
        ],
        "certificate_path": "/etc/sing-box/bing.com.crt",//服务器 PEM 证书路径。
        "key_path": "/etc/sing-box/bing.com.key"//服务器 PEM 私钥路径。
      }
    },
    {
      "type": "tuic",
      "tag": "t4ue2qpp-in",
      "listen": "::",
      "listen_port": 34927,
      "sniff": true,
      "sniff_override_destination": true,
      "users": [
        {
          "name": "haFh/5Qu",
          "uuid": "1f1f2aeb-5ed4-4a0f-ae4a-374da3b569f2",
          "password": "NYWqN/95TIpT"
        }
      ],
      "congestion_control": "bbr",
      "auth_timeout": "3s",
      "zero_rtt_handshake": false,
      "heartbeat": "10s",
      "tls": {
        "enabled": true,
        "server_name": "bing.com",
        "alpn": [
          "h3"
        ],
        "certificate_path": "/etc/sing-box/bing.com.crt",
        "key_path": "/etc/sing-box/bing.com.key"
      }
    },
    {
      "type": "shadowsocks",
      "tag": "w4cjbm8c-in",
      "listen": "::",
      "listen_port": 46728,
      "sniff": true,
      "sniff_override_destination": true,
      "method": "2022-blake3-aes-256-gcm",
      "password": "yEjWgksYvlvzqQY0etf6BUikYk4nfO0M8rqFERDKpgM=",
      "multiplex": {
        "enabled": true,
        "padding": true
      }
    },
    {
      "type": "vless",
      "tag": "u6hkt31b-in",
      "listen": "::",
      "listen_port": 61728,
      "sniff": true,
      "sniff_override_destination": true,
      "users": [
        {
          "uuid": "30864c64-c409-492e-9879-4027145103ad",
          "flow": "xtls-rprx-vision"
        }
      ],
      "tls": {
        "enabled": true,
        "server_name": "icloud.cdn-apple.com",
        "reality": {
          "enabled": true,
          "handshake": {
            "server": "icloud.cdn-apple.com",
            "server_port": 443
          },
          "private_key": "QL3XrF--rM2ZszeWGt6k-vJhLngSKCGwPwTg5Nd0Rm4",
          "short_id": [
            "4cae74ba0ee703cc"
          ]
        }
      }
    },
    {
      "type": "vmess",
      "tag": "pr6p7aht-in",
      "listen": "::",
      "listen_port": 21878,
      "sniff": true,
      "sniff_override_destination": true,
      "transport": {
        "type": "ws",
        "path": "/fCf5EnoN",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      },
      "users": [
        {
          "uuid": "ff2360f1-aa10-48a5-b95a-8a26d2de2d8a",
          "alterId": 0
        }
      ],
      "tls": {
        "enabled": true,
        "server_name": "bing.com",
        "certificate_path": "/etc/sing-box/bing.com.crt",
        "key_path": "/etc/sing-box/bing.com.key"
      },
      "multiplex": {
        "enabled": true,
        "padding": false
      }
    },
    {
      "type": "vmess",
      "tag": "fmcaegoh-in",
      "listen": "::",
      "listen_port": 2514,
      "sniff": true,
      "sniff_override_destination": true,
      "transport": {
        "type": "ws",
        "path": "/PPiEXZH7",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      },
      "users": [
        {
          "uuid": "b2f63134-1dbc-4ce5-b37b-5587b01e4f7d",
          "alterId": 0
        }
      ],
      "multiplex": {
        "enabled": true,
        "padding": false
      }
    }
  ],
  "outbounds": [
    {
      "type": "direct",
      "tag": "direct"
    }
  ],
  "route": {
    "rules": [
      {
        "inbound": ["eu3pkop2-in"],
        "outbound": "direct"
      },
      {
        "inbound": ["gthagvb4-in"],
        "outbound": "direct"
      },
      {
        "inbound": ["5izubguu-in"],
        "outbound": "direct"
      },
      {
        "inbound": ["c4yndhs8-in"],
        "outbound": "direct"
      },
      {
        "inbound": ["d5tlrad8-in"],
        "outbound": "direct"
      },
      {
        "inbound": ["t4ue2qpp-in"],
        "outbound": "direct"
      },
      {
        "inbound": ["w4cjbm8c-in"],
        "outbound": "direct"
      },
      {
        "inbound": ["u6hkt31b-in"],
        "outbound": "direct"
      },
      {
        "inbound": ["pr6p7aht-in"],
        "outbound": "direct"
      },
      {
        "inbound": ["fmcaegoh-in"],
        "outbound": "direct"
      }
    ]
  }
}
```

### Windows 端使用

两种使用方式，直接使用sing-box内核运行，搭配clash api 管理节点；或者在V2ranN中自定义内核使用；

> 启动命令

CMD命令窗口，配置文件目录运行

`sing-box run  或者指定配置  sing-box run -c config_client.json`

> bat 启动，建立txt，修改名字和后缀 start.bat ，记得配置要保持默认名：config.json，或者路径中指定一下也行；

`@echo off chcp 65001 > nul  cd /d %~dp0  :: 检查脚本是否以管理员权限运行 net session >nul 2>&1 if %errorLevel% == 0 (     echo 管理员权限已检测到。正在以提升的权限运行 sing-box.exe...     sing-box.exe run ) else (     echo 错误：此脚本需要管理员权限才能运行。     echo 请以管理员身份重新运行此脚本。 )  pause`

> 管理界面，要是在公网上，一定要记得鉴权加密码；

`127.0.0.1:9090`

> 注意点：  
> 1、需要把配置中节点 IP 一键替换成你自己服务器的，如果按照上面教程搭建好服务器的，就可以直接启动了。  
> 2、第一次启动可能会多次失败，原因在于下载分流文件时，节点选择 “auto” ，中间会有问题；如果你节点比较好，一般没问题，但是延迟比较高的话，可以选择分流文件下载的节点，要确保该节点可用性；  
> 3、参数： “download_detour”: “auto”，auto 修改为确保可用节点的 tag

### Windows 端配置文件

> config_win.json

```json
{
  "log": {
    "disabled": false,
    "level": "warn",
    "timestamp": true
  },
  "dns": {
    "servers": [
      {
        "tag": "default-dns",
        "address": "223.5.5.5",
        "detour": "direct-out"
      },
      {
        "tag": "system-dns",
        "address": "local",
        "detour": "direct-out"
      },
      {
        "tag": "block-dns",
        "address": "rcode://name_error"
      },
      {
        "tag": "google",
        "address": "https://dns.google/dns-query",
        "address_resolver": "default-dns",
        "address_strategy": "ipv4_only",
        "strategy": "ipv4_only",
        "client_subnet": "1.0.1.0"
      }
    ],
    "rules": [
      {
        "outbound": "any",
        "server": "default-dns"
      },
      {
        "query_type": "HTTPS",
        "server": "block-dns"
      },
      {
        "clash_mode": "direct",
        "server": "default-dns"
      },
      {
        "clash_mode": "global",
        "server": "google"
      },
      {
        "rule_set": "cnsite",
        "server": "default-dns"
      }, 
      {
        "rule_set": "cnsite-!cn", 
        "server": "google"
      }
    ],
    "strategy": "ipv4_only",
    "disable_cache": false,
    "disable_expire": false,
    "independent_cache": false,
    "final": "google"
  },
  "inbounds": [
    {
      "type": "tun", 
      "inet4_address": "172.19.0.1/30", 
      "inet6_address": "fd00::1/126", 
      "auto_route": true, 
      "strict_route": true, 
      "sniff": true, 
      "sniff_override_destination": true, 
      "domain_strategy": "prefer_ipv4"
    }
  ],
  "outbounds": [
    {
      "type": "shadowsocks",
      "tag": "ss-6336",
      "server": "000.000.000.000", 
      "server_port": 37065,
      "method": "2022-blake3-aes-256-gcm", 
      "password": "ZBdsPBMPLM4vkK7yoS7jQjZmIoqmet3PWhrzxwLharY=",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vless",
      "tag": "vless-8620",
      "server": "000.000.000.000", 
      "server_port": 60316,
      "uuid": "695dcbdb-1499-482d-9741-70c1cbac4710", 
      "flow": "",
      "tls": {
        "enabled": true,
        "server_name": "icloud.cdn-apple.com", 
        "utls": {
          "enabled": true,
          "fingerprint": "chrome"
        },
        "reality": {
          "enabled": true,
          "public_key": "K_wz6EbgSbq_Qr5udscvxJF9DtdSrkcWe8gXs7ynUQg",
          "short_id": "429e7bbfcca430c4"
        }
      },
      "multiplex": {
        "enabled": true,
        "protocol": "smux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-2995",
      "server": "000.000.000.000", 
      "server_port": 61703,
      "uuid": "4074d046-1726-49cc-ad45-272b2c664f29",
      "transport": {
        "type": "ws",
        "path": "/u4yE78Ky",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com"
      },
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-6046",
      "server": "000.000.000.000", 
      "server_port": 39720,
      "uuid": "7e26c9f6-f993-4865-8488-6b02e634a6d7",
      "transport": {
        "type": "ws",
        "path": "/FQgD78nq",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "hysteria2",
      "tag": "Hysteria2-1060",
      "server": "000.000.000.000", 
      "server_port": 61970,
      "up_mbps": 500, 
      "down_mbps": 500,
      "obfs": {
        "type": "salamander",
        "password": "41nPRX39cSt6"
      },
      "password": "WqzGKnmZ9UaB",
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com", 
        "alpn": [
          "h3"
        ]
      }
    },
    {
      "type": "tuic",
      "tag": "tuic-5564",
      "server": "000.000.000.000", 
      "server_port": 34927,
      "uuid": "1f1f2aeb-5ed4-4a0f-ae4a-374da3b569f2", 
      "password": "NYWqN/95TIpT", 
      "congestion_control": "bbr",
      "udp_relay_mode": "native",
      "zero_rtt_handshake": false,
      "heartbeat": "10s",
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com", 
        "alpn": [
          "h3"
        ]
      }
    },
    {
      "type": "shadowsocks",
      "tag": "ss-5455",
      "server": "000.000.000.000", 
      "server_port": 46728,
      "method": "2022-blake3-aes-256-gcm", 
      "password": "yEjWgksYvlvzqQY0etf6BUikYk4nfO0M8rqFERDKpgM=",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true
      }
    },
    {
      "type": "vless",
      "tag": "vless-9777",
      "server": "000.000.000.000", 
      "server_port": 61728,
      "uuid": "30864c64-c409-492e-9879-4027145103ad", 
      "flow": "xtls-rprx-vision",
      "tls": {
        "enabled": true,
        "server_name": "icloud.cdn-apple.com", 
        "utls": {
          "enabled": true,
          "fingerprint": "chrome"
        },
        "reality": {
          "enabled": true,
          "public_key": "-cPRCULJ4T6yZUR-Cmyuwon_HyMKQY6_6KWVCV4wbDc",
          "short_id": "4cae74ba0ee703cc"
        }
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-1219",
      "server": "000.000.000.000", 
      "server_port": 21878,
      "uuid": "ff2360f1-aa10-48a5-b95a-8a26d2de2d8a",
      "transport": {
        "type": "ws",
        "path": "/fCf5EnoN",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com"
      },
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-7569",
      "server": "000.000.000.000", 
      "server_port": 2514,
      "uuid": "b2f63134-1dbc-4ce5-b37b-5587b01e4f7d",
      "transport": {
        "type": "ws",
        "path": "/PPiEXZH7",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false
      }
    },
    {
      "type": "urltest",
      "tag": "auto",
      "outbounds": [
        "ss-6336", 
        "vless-8620", 
        "vmess-2995", 
        "vmess-6046", 
        "Hysteria2-1060", 
        "tuic-5564", 
        "ss-5455", 
        "vless-9777", 
        "vmess-1219", 
        "vmess-7569"
      ],
      "url": "https://www.gstatic.com/generate_204",
      "interval": "1m",
      "tolerance": 50,
      "interrupt_exist_connections": false
    },
    {
      "type": "selector",
      "tag": "select",
      "outbounds": [
        "ss-6336", 
        "vless-8620", 
        "vmess-2995", 
        "vmess-6046", 
        "Hysteria2-1060", 
        "tuic-5564", 
        "ss-5455", 
        "vless-9777", 
        "vmess-1219", 
        "vmess-7569", 
        "auto"
      ],
      "default": "auto",
      "interrupt_exist_connections": false
    },
    {
      "type": "direct",
      "tag": "direct-out",
      "routing_mark": 100
    },
    {
      "type": "block",
      "tag": "block-out"
    },
    {
      "type": "dns",
      "tag": "dns-out"
    }
  ],
  "route": {
    "rules": [
      {
        "protocol": "dns",
        "outbound": "dns-out"
      },
      {
        "protocol": "quic",
        "outbound": "block-out"
      },
      {
        "clash_mode": "block",
        "outbound": "block-out"
      },
      {
        "clash_mode": "direct",
        "outbound": "direct-out"
      },
      {
        "clash_mode": "global",
        "outbound": "select"
      },
      {
        "rule_set": [
          "cnip",
          "cnsite"
        ],
        "outbound": "direct-out"
      }, 
      {
        "rule_set": "cnsite-!cn", 
        "outbound": "select"
      }
    ],
    "rule_set": [
      {
        "type": "remote", 
        "tag": "cnsite-!cn", 
        "format": "binary", 
        "url": "https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-geolocation-!cn.srs", 
        "download_detour": "auto"
      }, 
      {
        "type": "remote",
        "tag": "cnip",
        "format": "binary",
        "url": "https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo-lite/geoip/cn.srs",
        "download_detour": "auto"
      },
      {
        "type": "remote",
        "tag": "cnsite",
        "format": "binary",
        "url": "https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo-lite/geosite/cn.srs",
        "download_detour": "auto"
      }
    ],
    "auto_detect_interface": true,
    "final": "select"
  },
  "experimental": {
    "cache_file": {
      "enabled": true, 
      "path": "cache.db", 
      "store_fakeip": true
    },
    "clash_api": {
      "external_controller": "127.0.0.1:9090",
      "external_ui": "ui", 
      "external_ui_download_url": "", 
      "external_ui_download_detour": "auto",
      "default_mode": "rule"
    }
  },
  "ntp": {
    "enabled": true,
    "server": "time.apple.com",
    "server_port": 123,
    "interval": "30m",
    "detour": "direct-out"
  }
}
```

### iOS 端使用

iOS 端的配置文件和 Windows 客户端其实基本相同，只有一个入站方式，ios 必须时 tun 模式；

> 注意点同上，参考win；

> APP设置问题

ios sing-box app 客户端可以保持默认设置；  
当然也可以把所以设置选项打开，需要注意两点；

1. No Default Route 不要打开，会改变配置的路由方式，造成 DNS 泄露；
    
2. includeAllNetworks 这个设置，与入站中 “stack” 值为 “system” 和 “mixed” 冲突，可以选择 gvisor 这个参数，或者直接注释。
    

### iOS 端配置文件

> config_phone.json

```json
{
  "log": {
    "disabled": false,
    "level": "warn",
    "timestamp": true
  },
  "dns": {
    "servers": [
      {
        "tag": "default-dns",
        "address": "223.5.5.5",
        "detour": "direct-out"
      },
      {
        "tag": "system-dns",
        "address": "local",
        "detour": "direct-out"
      },
      {
        "tag": "block-dns",
        "address": "rcode://name_error"
      },
      {
        "tag": "google",
        "address": "https://dns.google/dns-query",
        "address_resolver": "default-dns",
        "address_strategy": "ipv4_only",
        "strategy": "ipv4_only",
        "client_subnet": "1.0.1.0"
      }
    ],
    "rules": [
      {
        "outbound": "any",
        "server": "default-dns"
      },
      {
        "query_type": "HTTPS",
        "server": "block-dns"
      },
      {
        "clash_mode": "direct",
        "server": "default-dns"
      },
      {
        "clash_mode": "global",
        "server": "google"
      },
      {
        "rule_set": "cnsite",
        "server": "default-dns"
      }, 
      {
        "rule_set": "cnsite-!cn", 
        "server": "google"
      }
    ],
    "strategy": "ipv4_only",
    "disable_cache": false,
    "disable_expire": false,
    "independent_cache": false,
    "final": "google"
  },
  "inbounds": [
    {
      "type": "tun",
      "tag": "tun-in",
      "inet4_address": "172.19.0.1/30",
      "inet6_address": "fd00::1/126",
      "mtu": 9000,
      "gso": true,
      "auto_route": true,
      "strict_route": true,
      "stack": "gvisor",
      "sniff": true,
      "sniff_override_destination": true
    }
  ],
  "outbounds": [
    {
      "type": "shadowsocks",
      "tag": "ss-6336",
      "server": "000.000.000.000", 
      "server_port": 37065,
      "method": "2022-blake3-aes-256-gcm", 
      "password": "ZBdsPBMPLM4vkK7yoS7jQjZmIoqmet3PWhrzxwLharY=",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vless",
      "tag": "vless-8620",
      "server": "000.000.000.000", 
      "server_port": 60316,
      "uuid": "695dcbdb-1499-482d-9741-70c1cbac4710", 
      "flow": "",
      "tls": {
        "enabled": true,
        "server_name": "icloud.cdn-apple.com", 
        "utls": {
          "enabled": true,
          "fingerprint": "chrome"
        },
        "reality": {
          "enabled": true,
          "public_key": "K_wz6EbgSbq_Qr5udscvxJF9DtdSrkcWe8gXs7ynUQg",
          "short_id": "429e7bbfcca430c4"
        }
      },
      "multiplex": {
        "enabled": true,
        "protocol": "smux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-2995",
      "server": "000.000.000.000", 
      "server_port": 61703,
      "uuid": "4074d046-1726-49cc-ad45-272b2c664f29",
      "transport": {
        "type": "ws",
        "path": "/u4yE78Ky",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com"
      },
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-6046",
      "server": "000.000.000.000", 
      "server_port": 39720,
      "uuid": "7e26c9f6-f993-4865-8488-6b02e634a6d7",
      "transport": {
        "type": "ws",
        "path": "/FQgD78nq",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "hysteria2",
      "tag": "Hysteria2-1060",
      "server": "000.000.000.000", 
      "server_port": 61970,
      "up_mbps": 500, 
      "down_mbps": 500,
      "obfs": {
        "type": "salamander",
        "password": "41nPRX39cSt6"
      },
      "password": "WqzGKnmZ9UaB",
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com", 
        "alpn": [
          "h3"
        ]
      }
    },
    {
      "type": "tuic",
      "tag": "tuic-5564",
      "server": "000.000.000.000", 
      "server_port": 34927,
      "uuid": "1f1f2aeb-5ed4-4a0f-ae4a-374da3b569f2", 
      "password": "NYWqN/95TIpT", 
      "congestion_control": "bbr",
      "udp_relay_mode": "native",
      "zero_rtt_handshake": false,
      "heartbeat": "10s",
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com", 
        "alpn": [
          "h3"
        ]
      }
    },
    {
      "type": "shadowsocks",
      "tag": "ss-5455",
      "server": "000.000.000.000", 
      "server_port": 46728,
      "method": "2022-blake3-aes-256-gcm", 
      "password": "yEjWgksYvlvzqQY0etf6BUikYk4nfO0M8rqFERDKpgM=",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true
      }
    },
    {
      "type": "vless",
      "tag": "vless-9777",
      "server": "000.000.000.000", 
      "server_port": 61728,
      "uuid": "30864c64-c409-492e-9879-4027145103ad", 
      "flow": "xtls-rprx-vision",
      "tls": {
        "enabled": true,
        "server_name": "icloud.cdn-apple.com", 
        "utls": {
          "enabled": true,
          "fingerprint": "chrome"
        },
        "reality": {
          "enabled": true,
          "public_key": "-cPRCULJ4T6yZUR-Cmyuwon_HyMKQY6_6KWVCV4wbDc",
          "short_id": "4cae74ba0ee703cc"
        }
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-1219",
      "server": "000.000.000.000", 
      "server_port": 21878,
      "uuid": "ff2360f1-aa10-48a5-b95a-8a26d2de2d8a",
      "transport": {
        "type": "ws",
        "path": "/fCf5EnoN",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com"
      },
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-7569",
      "server": "000.000.000.000", 
      "server_port": 2514,
      "uuid": "b2f63134-1dbc-4ce5-b37b-5587b01e4f7d",
      "transport": {
        "type": "ws",
        "path": "/PPiEXZH7",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false
      }
    },
    {
      "type": "urltest",
      "tag": "auto",
      "outbounds": [
        "ss-6336", 
        "vless-8620", 
        "vmess-2995", 
        "vmess-6046", 
        "Hysteria2-1060", 
        "tuic-5564", 
        "ss-5455", 
        "vless-9777", 
        "vmess-1219", 
        "vmess-7569"
      ],
      "url": "https://www.gstatic.com/generate_204",
      "interval": "1m",
      "tolerance": 50,
      "interrupt_exist_connections": false
    },
    {
      "type": "selector",
      "tag": "select",
      "outbounds": [
        "ss-6336", 
        "vless-8620", 
        "vmess-2995", 
        "vmess-6046", 
        "Hysteria2-1060", 
        "tuic-5564", 
        "ss-5455", 
        "vless-9777", 
        "vmess-1219", 
        "vmess-7569", 
        "auto"
      ],
      "default": "auto",
      "interrupt_exist_connections": false
    },
    {
      "type": "direct",
      "tag": "direct-out",
      "routing_mark": 100
    },
    {
      "type": "block",
      "tag": "block-out"
    },
    {
      "type": "dns",
      "tag": "dns-out"
    }
  ],
  "route": {
    "rules": [
      {
        "protocol": "dns",
        "outbound": "dns-out"
      },
      {
        "protocol": "quic",
        "outbound": "block-out"
      },
      {
        "clash_mode": "block",
        "outbound": "block-out"
      },
      {
        "clash_mode": "direct",
        "outbound": "direct-out"
      },
      {
        "clash_mode": "global",
        "outbound": "select"
      },
      {
        "rule_set": [
          "cnip",
          "cnsite"
        ],
        "outbound": "direct-out"
      }, 
      {
        "rule_set": "cnsite-!cn", 
        "outbound": "select"
      }
    ],
    "rule_set": [
      {
        "type": "remote", 
        "tag": "cnsite-!cn", 
        "format": "binary", 
        "url": "https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-geolocation-!cn.srs", 
        "download_detour": "auto"
      }, 
      {
        "type": "remote",
        "tag": "cnip",
        "format": "binary",
        "url": "https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo-lite/geoip/cn.srs",
        "download_detour": "auto"
      },
      {
        "type": "remote",
        "tag": "cnsite",
        "format": "binary",
        "url": "https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo-lite/geosite/cn.srs",
        "download_detour": "auto"
      }
    ],
    "auto_detect_interface": true,
    "final": "select"
  },
  "experimental": {
    "cache_file": {
      "enabled": true, 
      "path": "cache.db", 
      "store_fakeip": true
    },
    "clash_api": {
      "external_controller": "127.0.0.1:9090",
      "external_ui": "ui", 
      "external_ui_download_url": "", 
      "external_ui_download_detour": "auto",
      "default_mode": "rule"
    }
  },
  "ntp": {
    "enabled": true,
    "server": "time.apple.com",
    "server_port": 123,
    "interval": "30m",
    "detour": "direct-out"
  }
}
```

## 应用分流，包含ai解锁、流媒体解锁配置

上面的所有配置算是比较简单的，这里再给大家贴一个分流的配置；可以根据自己实际情况修改，作为一个参考配置，基于 Windows 端配置修改，ios 也通用。

> 注意点同上，参考win；

> config_win_应用分流.json

```json
{
  "log": {
    "disabled": false,
    "level": "warn",
    "timestamp": true
  },
  "dns": {
    "servers": [
      {
        "tag": "default-dns",
        "address": "223.5.5.5",
        "detour": "direct-out"
      },
      {
        "tag": "system-dns",
        "address": "local",
        "detour": "direct-out"
      },
      {
        "tag": "block-dns",
        "address": "rcode://name_error"
      },
      {
        "tag": "google",
        "address": "https://dns.google/dns-query",
        "address_resolver": "default-dns",
        "address_strategy": "ipv4_only",
        "strategy": "ipv4_only",
        "client_subnet": "1.0.1.0"
      }
    ],
    "rules": [
      {
        "outbound": "any",
        "server": "default-dns"
      },
      {
        "query_type": "HTTPS",
        "server": "block-dns"
      },
      {
        "clash_mode": "direct",
        "server": "default-dns"
      },
      {
        "clash_mode": "global",
        "server": "google"
      },
      {
        "rule_set": "cnsite",
        "server": "default-dns"
      }, 
      {
        "rule_set": "cnsite-!cn", 
        "server": "google"
      }
    ],
    "strategy": "ipv4_only",
    "disable_cache": false,
    "disable_expire": false,
    "independent_cache": false,
    "final": "google"
  },
  "inbounds": [
    {
      "type": "tun", 
      "inet4_address": "172.19.0.1/30", 
      "inet6_address": "fd00::1/126", 
      "auto_route": true, 
      "strict_route": true, 
      "sniff": true, 
      "sniff_override_destination": true, 
      "domain_strategy": "prefer_ipv4"
    }
  ],
  "outbounds": [
    {
      "type": "shadowsocks",
      "tag": "ss-解锁节点",				//解锁节点，可以选择解锁流媒体，或者AI类的。
      "server": "000.000.000.000", 
      "server_port": 37065,
      "method": "2022-blake3-aes-256-gcm", 
      "password": "ZBdsPBMPLM4vkK7yoS7jQjZmIoqmet3PWhrzxwLharY=",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "shadowsocks",
      "tag": "ss-6336",
      "server": "000.000.000.000", 
      "server_port": 37065,
      "method": "2022-blake3-aes-256-gcm", 
      "password": "ZBdsPBMPLM4vkK7yoS7jQjZmIoqmet3PWhrzxwLharY=",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vless",
      "tag": "vless-8620",
      "server": "000.000.000.000", 
      "server_port": 60316,
      "uuid": "695dcbdb-1499-482d-9741-70c1cbac4710", 
      "flow": "",
      "tls": {
        "enabled": true,
        "server_name": "icloud.cdn-apple.com", 
        "utls": {
          "enabled": true,
          "fingerprint": "chrome"
        },
        "reality": {
          "enabled": true,
          "public_key": "K_wz6EbgSbq_Qr5udscvxJF9DtdSrkcWe8gXs7ynUQg",
          "short_id": "429e7bbfcca430c4"
        }
      },
      "multiplex": {
        "enabled": true,
        "protocol": "smux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-2995",
      "server": "000.000.000.000", 
      "server_port": 61703,
      "uuid": "4074d046-1726-49cc-ad45-272b2c664f29",
      "transport": {
        "type": "ws",
        "path": "/u4yE78Ky",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com"
      },
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-6046",
      "server": "000.000.000.000", 
      "server_port": 39720,
      "uuid": "7e26c9f6-f993-4865-8488-6b02e634a6d7",
      "transport": {
        "type": "ws",
        "path": "/FQgD78nq",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false,
        "brutal": {
          "enabled": true,
          "up_mbps": 500,
          "down_mbps": 500
        }
      }
    },
    {
      "type": "hysteria2",
      "tag": "Hysteria2-1060",
      "server": "000.000.000.000", 
      "server_port": 61970,
      "up_mbps": 500, 
      "down_mbps": 500,
      "obfs": {
        "type": "salamander",
        "password": "41nPRX39cSt6"
      },
      "password": "WqzGKnmZ9UaB",
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com", 
        "alpn": [
          "h3"
        ]
      }
    },
    {
      "type": "tuic",
      "tag": "tuic-5564",
      "server": "000.000.000.000", 
      "server_port": 34927,
      "uuid": "1f1f2aeb-5ed4-4a0f-ae4a-374da3b569f2", 
      "password": "NYWqN/95TIpT", 
      "congestion_control": "bbr",
      "udp_relay_mode": "native",
      "zero_rtt_handshake": false,
      "heartbeat": "10s",
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com", 
        "alpn": [
          "h3"
        ]
      }
    },
    {
      "type": "shadowsocks",
      "tag": "ss-5455",
      "server": "000.000.000.000", 
      "server_port": 46728,
      "method": "2022-blake3-aes-256-gcm", 
      "password": "yEjWgksYvlvzqQY0etf6BUikYk4nfO0M8rqFERDKpgM=",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": true
      }
    },
    {
      "type": "vless",
      "tag": "vless-9777",
      "server": "000.000.000.000", 
      "server_port": 61728,
      "uuid": "30864c64-c409-492e-9879-4027145103ad", 
      "flow": "xtls-rprx-vision",
      "tls": {
        "enabled": true,
        "server_name": "icloud.cdn-apple.com", 
        "utls": {
          "enabled": true,
          "fingerprint": "chrome"
        },
        "reality": {
          "enabled": true,
          "public_key": "-cPRCULJ4T6yZUR-Cmyuwon_HyMKQY6_6KWVCV4wbDc",
          "short_id": "4cae74ba0ee703cc"
        }
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-1219",
      "server": "000.000.000.000", 
      "server_port": 21878,
      "uuid": "ff2360f1-aa10-48a5-b95a-8a26d2de2d8a",
      "transport": {
        "type": "ws",
        "path": "/fCf5EnoN",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com"
      },
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false
      }
    },
    {
      "type": "vmess",
      "tag": "vmess-7569",
      "server": "000.000.000.000", 
      "server_port": 2514,
      "uuid": "b2f63134-1dbc-4ce5-b37b-5587b01e4f7d",
      "transport": {
        "type": "ws",
        "path": "/PPiEXZH7",
        "max_early_data": 2048,
        "early_data_header_name": "Sec-WebSocket-Protocol"
      }, 
      "security": "auto",
      "alter_id": 0,
      "packet_encoding": "xudp",
      "multiplex": {
        "enabled": true,
        "protocol": "h2mux",
        "max_connections": 1,
        "min_streams": 4,
        "padding": false
      }
    },
    {
      "type": "urltest",
      "tag": "auto",
      "outbounds": [
        "ss-解锁节点", 
        "ss-6336", 
        "vless-8620", 
        "vmess-2995", 
        "vmess-6046", 
        "Hysteria2-1060", 
        "tuic-5564", 
        "ss-5455", 
        "vless-9777", 
        "vmess-1219", 
        "vmess-7569"
      ],
      "url": "https://www.gstatic.com/generate_204",
      "interval": "1m",
      "tolerance": 50,
      "interrupt_exist_connections": false
    },
    {
      "type": "selector",
      "tag": "select",
      "outbounds": [
        "ss-解锁节点", 
        "ss-6336", 
        "vless-8620", 
        "vmess-2995", 
        "vmess-6046", 
        "Hysteria2-1060", 
        "tuic-5564", 
        "ss-5455", 
        "vless-9777", 
        "vmess-1219", 
        "vmess-7569", 
        "auto"
      ],
      "default": "auto",
      "interrupt_exist_connections": false
    },
    {
      "type": "selector",
      "tag": "解锁节点",
      "outbounds": [
        "ss-解锁节点", 
        "ss-6336", 
        "vless-8620", 
        "vmess-2995", 
        "vmess-6046", 
        "Hysteria2-1060", 
        "tuic-5564", 
        "ss-5455", 
        "vless-9777", 
        "vmess-1219", 
        "vmess-7569", 
        "auto"
      ],
      "default": "ss-解锁节点",
      "interrupt_exist_connections": false
    },
    {
      "type": "direct",
      "tag": "direct-out",
      "routing_mark": 100
    },
    {
      "type": "block",
      "tag": "block-out"
    },
    {
      "type": "dns",
      "tag": "dns-out"
    }
  ],
  "route": {
    "rules": [
      {
        "protocol": "dns",
        "outbound": "dns-out"
      },
      {
        "protocol": "quic",
        "outbound": "block-out"
      },
      {
        "clash_mode": "block",
        "outbound": "block-out"
      },
      {
        "clash_mode": "direct",
        "outbound": "direct-out"
      },
      {
        "clash_mode": "global",
        "outbound": "select"
      },
      {
        "rule_set": [
          "geosite-netflix",
          "geosite-disney",
          "geosite-youtube",
          "geosite-google",
          "geosite-spotify",
          "geosite-reddit"
        ],
        "outbound": "select"
      }, 
      {
        "rule_set": [
          "geosite-openai"
        ],
        "outbound": "解锁节点"
      },
      {
        "rule_set": [
          "cnip",
          "cnsite"
        ],
        "outbound": "direct-out"
      }, 
      {
        "rule_set": "cnsite-!cn", 
        "outbound": "select"
      }
    ],
    "rule_set": [
      {
        "type": "remote", 
        "tag": "cnsite-!cn", 
        "format": "binary", 
        "url": "https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-geolocation-!cn.srs", 
        "download_detour": "auto"
      }, 
      {
        "type": "remote",
        "tag": "cnip",
        "format": "binary",
        "url": "https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo-lite/geoip/cn.srs",
        "download_detour": "auto"
      },
      {
        "type": "remote",
        "tag": "cnsite",
        "format": "binary",
        "url": "https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo-lite/geosite/cn.srs",
        "download_detour": "auto"
      },
      {
        "tag": "geosite-openai",
        "type": "remote",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/SagerNet/sing-geosite/rule-set/geosite-openai.srs",
        "download_detour": "auto"
      },
      {
        "tag": "geosite-netflix",
        "type": "remote",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/SagerNet/sing-geosite/rule-set/geosite-netflix.srs",
        "download_detour": "auto"
      },
      {
        "tag": "geosite-disney",
        "type": "remote",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/SagerNet/sing-geosite/rule-set/geosite-disney.srs",
        "download_detour": "auto"
      },
      {
        "tag": "geosite-youtube",
        "type": "remote",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/SagerNet/sing-geosite/rule-set/geosite-youtube.srs",
        "download_detour": "auto"
      },
      {
        "tag": "geosite-google",
        "type": "remote",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/SagerNet/sing-geosite/rule-set/geosite-google.srs",
        "download_detour": "auto"
      },
      {
        "tag": "geosite-spotify",
        "type": "remote",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/SagerNet/sing-geosite/rule-set/geosite-spotify.srs",
        "download_detour": "auto"
      },
      {
        "tag": "geosite-reddit",
        "type": "remote",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/SagerNet/sing-geosite/rule-set/geosite-reddit.srs",
        "download_detour": "auto"
      }
    ],
    "auto_detect_interface": true,
    "final": "select"
  },
  "experimental": {
    "cache_file": {
      "enabled": true, 
      "path": "cache.db", 
      "store_fakeip": true
    },
    "clash_api": {
      "external_controller": "127.0.0.1:9090",
      "external_ui": "ui", 
      "external_ui_download_url": "", 
      "external_ui_download_detour": "auto",
      "default_mode": "rule"
    }
  },
  "ntp": {
    "enabled": true,
    "server": "time.apple.com",
    "server_port": 123,
    "interval": "30m",
    "detour": "direct-out"
  }
}
```

## 安卓端、旁路由、软路由等等

因为没有相关设备，就没有测试了。以上配置主要分为服务端和客户端，所以配置文件都是通用的。比如旁路由作为网关的话，配置文件就采用客户端类型格式，修改一下入站的写法就行，大家可以自行修改到其他平台。