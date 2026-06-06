## Q1-1.1
![[Pasted image 20260424153105.png]]
## Q1-1.2
![[屏幕截图 2026-04-24 152753.png|601]]
广播风暴不仅会耗尽交换机的资源 ，甚至会导致实验环境和管理软件（如 Wireshark 和 eNSP）产生严重滞后。即使在物理链路上中断了环路，由于产生的数据包基数巨大，软件依然需要大量时间来处理累积的残留报文。
## Q1-1.3

LSW1占用在4%， LSW2平均占用3%，两交换机峰值CPU占用100%
![[Pasted image 20260424153754.png]]
![[Pasted image 20260424153803.png]]

## Q1-1.4
![[Pasted image 20260424153510.png]]
偏移的mac地址`5489-98f6-4d76`
该 MAC 地址在 **`GE0/0/1`** (原始端口 / Original-Port)、**`GE0/0/23`** 和 **`GE0/0/24`** 之间发生漂移。

## Q1-2.1
![[Pasted image 20260424160155.png]]
## Q1-2.2
![[42f3d2b22ed43d6144beda0be870ba3f.jpg]]![[bd735d1b5be0051a29d1d4777a0b990b.jpg|601]]
## Q1-2.3
![[屏幕截图 2026-04-24 163701.png]]
## Q1-2.4
![[Pasted image 20260424165424.png]]
两种类型。
## Q1-2.5
01:80:c2:00:00:00
## Q1-2.6
![[Pasted image 20260424165734.png]]
GE 0/0/21 LSW3
不是root bridge
是根端口
发给了LSW1交换机（之后再由LSW1送给根桥LSW2）
## Q1-2.7
![[Pasted image 20260424170539.png]]
![[Pasted image 20260424170713.png]]
只有根桥初始化了TC flag，下游designated bridge 将其传递给剩余的网络。
## Q1-2.8
![[Pasted image 20260424170923.png]]
Both Root Bridge and Non-Root Bridge (specifically, the upstream Designated Bridge that received the TCN) can send this to acknowledge receipt.
## Q1-2.9
最主要的是配置BPDUs包。不，TC和TCA都是0.
## Q1-2.10
- **下级上报 (生成 TCN)**：当非根桥 (Non-root bridge) 检测到网络拓扑发生变化时，它会从自己的**根端口 (Root Port)** 向上游发送 **TCN BPDU**，目标是通知根桥。
- **逐级确认与转发 (TCA 回执)**：上游交换机收到该 TCN 报文后，会向该下级回复一个带有 **TCA（拓扑改变确认）标志位**的配置 BPDU 作为“已阅”回执，让下级停止发送 TCN。同时，上游交换机会继续将 TCN 报文沿着自己的根端口向上传递，直到抵达根桥。
- **顶层发布命令 (下发 TC)**：根桥 (Root Bridge) 最终收到 TCN 报告后，会在其周期性下发的配置 BPDU 中设置 **TC（拓扑改变）标志位**，并将其向全网泛洪 (Flood) 广播。
- **全网刷新 (加速老化)**：全网所有收到带有 TC 标志位报文的交换机，都会将其 MAC 地址表的老化时间大幅缩短（通常从默认的 300 秒缩短至 15 秒）。这迫使交换机快速清空旧的、失效的表项，并基于新的拓扑重新学习 MAC 地址。
## Q1-2.11
没有。
## Q1-2.12
仅在 GE 0/0/21 端口上抓到了 Ping 通信。在 GE 0/0/22, 23, 24 端口上没有抓到任何 Ping 流量。

## Q1-3.1
![[Pasted image 20260424174653.png]]

## Q1-3.2
LSW3为根桥，优先级为4096
## Q1-3.3
![[屏幕截图 2026-04-24 174650.png]]


