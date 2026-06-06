# Ethernet Frame

Ethernet II structure
```
| Destination MAC | Source MAC | EtherType | Payload |
| 6 bytes         | 6 bytes    | 2 bytes   | variable |
```

if have VLAN:
```
| Destination MAC | Source MAC | VLAN Tag | EtherType | Payload |
| 6B              | 6B         | 4B       | 2B        | variable |
```

if IPv4, the payload loading the IPv4 package.
IPv4 header:
```
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version|  IHL  |   DSCP/ECN    |        Total Length         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Identification        | Flags |   Fragment Offset   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      TTL      |   Protocol    |        Header Checksum      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Source IP Address                     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Destination IP Address                   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options, if any ...                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                         Payload                             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```
and the TCP package:
```
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Data  |       |U|A|P|R|S|F|                                 |
|Offset |Reserve|R|C|S|S|Y|I|            Window Size          |
|       |       |G|K|H|T|N|N|                                 |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options, if any ...                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                         TCP Payload                         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```
## example
```
34c93d1de1e7f033e57acfed0800 
450000347bc740007d0645090a0006ca0a3e21ec
01bb0dcd7e8204765806bbc480120839838e0000
020405b40103030201010402
```
is a hex stream of ethernet frame captured by wireshark.
split:
```
34 c9 3d 1d e1 e7   f0 33 e5 7a cf ed
08 00
```
is the ethernet header:
```
destination MAC: 34-c9-3d-1d-e1-e7
source MAC:      f0-33-e5-7a-cf-ed
type:            0800  -> IPv4
```

```
45 00 00 34
7b c7 40 00
7d 06 45 09
0a 00 06 ca
0a 3e 21 ec
```
is the IPv4 header. means:
```
version: 4
header_length: 5 (in units of 4B) -> 20B total
total_length: 34H = 52B

idenfication: 7bc7H
flag: 010 -> MF = 0, DF = 1 (Don't fragment)
fragment offset: 0

survival time: 7dH
protocal: 06H -> 6:TCP, 17:UDP
header checksum: 4509H

source IP address:      0a 00 06 ca -> 10.0.6.202
destination IP address: oa 3e 21 ec -> 10.62.33.236
```

```
01 bb 0d cd
7e 82 04 76
58 06 bb c4
80 12 08 39
83 8e 00 00

02 04 05 b4
01 03 03 02
01 01 04 02
```
is the TCP header. means:
```
source port: 01bb -> 443
destination port: 0dcd -> 3533

sequence number: 7e 82 04 76
ack number: 58 06 bb c4

data offset: 8 (4 bit) -> total TCP header length: 8*4B = 32B
reserve: 000000 (6 bit)
6 control bit: (6)
	URG 0
	ACK 1
	PSH 0
	RST 0
	SYN 1
	FIN 0
window size: 0839H
check sum: 838e
urgen pointer: 0000
```