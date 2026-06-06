## Q2-1.1
## Q2-1.1
![[Pasted image 20260424214924.png]]

## Q2-1.2
| **Destination MAC** | **Source MAC** | **802.1Q VLAN Tag**    | **Length / Type** | **Data (Payload)** | **FCS (CRC)** |
| ------------------- | -------------- | ---------------------- | ----------------- | ------------------ | ------------- |
| 6 Bytes             | 6 Bytes        | **4 Bytes (Inserted)** | 2 Bytes           | 46 - 1500 Bytes    | 4 Bytes       |

## Q2-1.3
![[Pasted image 20260424215114.png]]

## Q2-1.4
![[Pasted image 20260424215630.png]]

## Q2-1.5
```bash
vlan 40
quit
port-group pgvlan30
group-member gigabitethernet 0/0/17 to gigabitethernet 0/0/20
port link-type access
port default vlan 40
quit
```
## Q2-1.6
![[Pasted image 20260425153329.png]]
![[Pasted image 20260425153352.png]]
在同一个VLAN，PC之间可以互相ping通
## Q2-1.7
在不同VLAN，PC之间不能ping通。不同 VLAN 之间不能直接互相 ping 通，是因为 VLAN 在数据链路层（二层）将网络划分成了相互隔离的广播域，导致底层的广播帧无法跨越边界 。
## Q2-2.1
![[Pasted image 20260425155015.png]]
## Q2-2.2

| laptop       | MAC Address    |
| ------------ | -------------- |
| PC-BOOK-10-1 | 5489-9810-32A7 |
| PC-BOOK-30-1 | 5489-98D5-539B |

## Q2-2.3
![[Pasted image 20260425155532.png]]

## Q2-2.4
![[Pasted image 20260425155755.png]]
在添加之前，两个PC只显示静态端口5和6.添加mac之后，mac地址也包含在了vlan 10（30）中。
## Q2-2.5
![[Pasted image 20260425155850.png]]
在PC-10-1成功ping通PC-10-18
## Q2-2.6
![[Pasted image 20260425155939.png]]
在30-1中成功ping通PC-30-18
## Q2-2.7
![[Pasted image 20260425160247.png]]
交换了两个主机的端口
## Q2-2.8
![[Pasted image 20260425160313.png]]

## Q2-2.9
![[Pasted image 20260425160332.png]]
## Q2-3.1
![[Pasted image 20260425161111.png]]
## Q2-3.2
![[Pasted image 20260425161105.png]]
## Q2-3.3
![[Pasted image 20260425162243.png]]
## Q2-3.4
![[Pasted image 20260425162201.png]]
## Q2-3.5
![[Pasted image 20260425162320.png]]
![[Pasted image 20260425162430.png]]
## Q2-3.6
![[Pasted image 20260425162413.png]]
![[Pasted image 20260425162456.png]]

## Q2-3.7
![[Pasted image 20260425162701.png]]
通过 `display vlan` 命令可以看出，GE 0/0/7 和 GE 0/0/8 均以 Untagged (UT) 形式成功加入了 VLAN 10 和 VLAN 30。
## Q2-4.1
![[Pasted image 20260425174701.png]]
## Q2-4.2
![[Pasted image 20260425174742.png]]
同VLAN不同交换机能ping通
## Q2-4.3
![[Pasted image 20260425174810.png]]

## Q2-4.4
![[Pasted image 20260425174843.png]]
## Q2-4.5
![[Pasted image 20260425175104.png]]
![[Pasted image 20260425175123.png]]
在 GE 0/0/24 端口捕获的以太网帧是带标签的帧。VLAN 标识符为 10 和 30（取决于是哪台 PC 发起的 Ping 操作）。
## Q2-4.6
![[Pasted image 20260425175212.png]]

在 GE 0/0/9 端口捕获的以太网帧是未标记帧。因为这是一个连接到终端设备的接入端口，所以在将帧转发到 PC 之前，交换机移除了 VLAN 标签。

