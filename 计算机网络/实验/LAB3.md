## Q3-1.1
![[Pasted image 20260514153516.png]]
## Q3-1.2
![[屏幕截图 2026-05-14 153638.png]]
## Q3-1.3
在192.168.10.11上ping
![[Pasted image 20260514153929.png]]
在192.168.10.13上ping
![[Pasted image 20260514154130.png]]
## Q3-1.4
在192.168.30.11上ping
![[Pasted image 20260514154240.png]]
在192.168.30.13上ping
![[Pasted image 20260514154324.png]]
## Q3-1.5
在192.168.10.11上ping
![[Pasted image 20260514154751.png]]
在192.168.30.13上ping
![[Pasted image 20260514154826.png]]
通过VLANIF实现了跨VLAN传输
## Q3-1.6
![[Pasted image 20260514164220.png]]
路径经过 **192.168.10.1**（VLANIF 10，即 LSW1），说明 LSW1 在做三层路由转发。
## Q3-1.7
![[Pasted image 20260514164841.png]]
## Q3-1.8
![[屏幕截图 2026-05-14 165109.png]]

![[屏幕截图 2026-05-14 165123.png]]
去程回程都是VLANID = 30.
## Q3-2.1
![[Pasted image 20260516150927.png]]
## Q3-2.2
![[Pasted image 20260516151906.png]]
## Q3-2.3
![[Pasted image 20260516152006.png]]
能ping通（pc-10-1和pc-10-3）
## Q3-2.4
![[Pasted image 20260516152248.png]]
路径经过 **192.168.10.1（RTA 子接口）**，说明包被送到路由器再转发。
## Q3-2.5
![[Pasted image 20260516152710.png]]
连接RTA的接口没有抓到pc-10-1<->pc-10-3的包
## Q3-2.6
![[Pasted image 20260516152752.png]]
0-0-24上有包。来回程vlan id = 10
## Q3-2.7

![[Pasted image 20260516152839.png]]
![[Pasted image 20260516152956.png]]

![[Pasted image 20260516152923.png]]
来程为10，去程为30