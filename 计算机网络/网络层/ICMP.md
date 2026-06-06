### Internet Control Message Protocal （ICMP）

Used to communicate IP status and error message between host and routers.

ICMP data is carried as the payload of an **IP** datagram

ICMP message not generate when:
1. ICMP message itself.
2. fragments except first fragment.
3. broadcast and [[IP#^7a2d81|multcast]].
4. specific IP (127.0.0.0, 0.0.0.0)
![[Pasted image 20260530183704.png|500]]

| ICMP type | description                     |
| --------- | ------------------------------- |
| 0         | echo reply (reply for **ping**) |
| 3         | **destination unreachable**     |
| 4         | source quench(obsolete)         |
| 5         |                                 |
| 8         |                                 |
| 11        |                                 |
| 12        |                                 |
| 13        |                                 |
| 14        |                                 |
| 15        |                                 |
| 16        |                                 |
| 17        |                                 |
| 18        |                                 |
for type 3 destionation unreachable:

| code | description                         |
| ---- | ----------------------------------- |
| 0    | net unreachable                     |
| 1    | host unreachable                    |
| 2    | protocal unreachable                |
| 3    | port unreachable                    |
| 4    | fragmentation needed DF set         |
| 5    | source route failed                 |
| 6    | destination network unknown         |
| 7    | destination host unknown            |
| 8    | source host isolated                |
| 9    | comm. with destn network prohibited |
| 10   | comm. With dest host prohibited     |
| 11   | network unreachable for service     |
| 12   | host unreachable for service        |
