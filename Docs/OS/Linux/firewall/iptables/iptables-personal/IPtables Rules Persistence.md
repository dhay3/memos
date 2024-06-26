# IPtables Rules Persistence

ref

https://www.zsythink.net/archives/1517

https://www.linuxtopia.org/Linux_Firewall_iptables/x1824.html

## Digest

IPtables Rules 默认不是永久的，会在服务器重启会清空。为了持久化可以通过下面这种方式（为了方便管理可是编写一个 systemctl unit file）

iptables 组件中提供了 `iptables-save` 用于将 iptables rules 输出成文本格式

```
[root@localhost vagrant]# iptables -nvL INPUT
Chain INPUT (policy ACCEPT 171 packets, 12188 bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 DROP       all  --  *      *       192.168.100.100      0.0.0.0/0

[root@localhost vagrant]# iptables-save
# Generated by iptables-save v1.4.21 on Tue Oct 18 05:42:41 2022
*filter
:INPUT ACCEPT [269:19260]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [140:15208]
-A INPUT -s 192.168.100.100/32 -j DROP
COMMIT
# Completed on Tue Oct 18 05:42:41 2022
[root@localhost vagrant]# iptables-save > ~/iptables-rules
```

通过 `iptablse-restore `重新读取 `iptables-save` 保存的文本到内存中

```
[root@localhost vagrant]# iptables -nvL 
Chain INPUT (policy ACCEPT 31 packets, 2200 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 16 packets, 1712 bytes)
 pkts bytes target     prot opt in     out     source               destination         
[root@localhost vagrant]# iptables-restore < ~/iptables-rules 
[root@localhost vagrant]# iptables -nvL 
Chain INPUT (policy ACCEPT 7 packets, 472 bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 DROP       all  --  *      *       192.168.100.100      0.0.0.0/0           

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 4 packets, 528 bytes)
 pkts bytes target     prot opt in     out     source               destination
```

## iptables-save Stdout

https://unix.stackexchange.com/questions/222224/what-do-the-numbers-in-brackets-mean-on-the-iptables-save-output

使用 `iptables-save` 时会将当前的 counters 以 `[packets:bytes]`的格式显示。而在 chains 后面的是默认的 policy

```
*filter
:INPUT ACCEPT [29845:17929031]
:FORWARD DROP [0:0]
:OUTPUT ACCEPT [28819:10183341]
:DOCKER - [0:0]
:DOCKER-ISOLATION-STAGE-1 - [0:0]
:DOCKER-ISOLATION-STAGE-2 - [0:0]
:DOCKER-USER - [0:0]
```

但是在 user-defined chians 后面一个字段值为 `-`, 因为 user-defined chains 不支持 Policy

完整的格式为

```
 :<chain-name> <chain-policy> [<packet-counter>:<byte-counter>].
```

