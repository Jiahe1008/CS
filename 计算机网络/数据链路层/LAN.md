## Ethernet

Ethernet is a **wired Local Area Network(LAN)** technique. **IEEE 802.3**

**transmission media**

**MAC address**

[[CSMA#^c5cf03|CSMA/CD]]

**MAC frame**
	[[ethernet frame]]
	the minimum MAC ethernet frame is 64B. 
	![[Pasted image 20260613212303.png|500]]
## Wi-Fi

Wi-Fi is a **wireless Local Area Network(LAN)** technique. **IEEE 802.11b**

**Basic Service Set(BSS)**
	BSSID 不一定等于 AP 设备所有接口的 MAC。
		*有线口 MAC*  
		*2.4G 无线 MAC*  
		*5G 无线 MAC*  
		*访客网络 BSSID*  
		*主网络 BSSID*
		...
	一个物理 AP 可能对应多个 BSSID。
**Access Point(AP)**
	every AP has a unique **Service Set Identifier(SSID)**

**Distribution System(DS)**

**MAC frame**
data frame
```
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-
frame   |   duration  | add1 | add2 | add3 | sequence | fragment  | add4  | data..
control |             |      |      |      | number   | number    |       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-
2B       2B            6B     6B     6B     2B                     6B      ...
```
frame control:
```
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-
protocal  | type  | sub type  | to | from| more    | retry|...
version   |       |           | DS | DS  | fragment|      |...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-
2b         2b      4b          1b   1b    1b        1b     1b    ... (1b)
``` 

**control frame**
	1. RTS
	2. CTS
	3. ACK
**[[CSMA#^88d364|CSMA/CA]]**


## VLAN
