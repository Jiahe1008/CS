## Q4-1.1
![[Pasted image 20260516162219.png]]
## Q4-1.2
![[Pasted image 20260516162605.png]]
## Q4-1.3
![[Pasted image 20260516162642.png]]

## Q4-1.4
![[屏幕截图 2026-05-16 162732.png]]

## Q4-1.5
![[Pasted image 20260516163127.png]]
## Q4-1.6
![[Pasted image 20260516163216.png]]
无法联通
## Q4-1.7
![[Pasted image 20260516163250.png]]


## Q4-1.8
## Q4-1.9
![[Pasted image 20260516163348.png]]
## Q4-1.10
![[Pasted image 20260516163425.png]]
## Q4-1.11
![[Pasted image 20260516163502.png]]

## Q4-1.12
![[Pasted image 20260516163525.png]]

## Q4-1.13
![[Pasted image 20260516163759.png]]
现在可以ping通了
## Q4-1.14
![[Pasted image 20260516163922.png]]
无法ping通
**Q4-1.14 ping 不通 loopback**，原因是：RTD 的静态路由只配了去 `192.168.10.0`，但 loopback 0 是 `192.168.200.100`，回程包从 RTD 发出时不知道怎么到 `192.168.10.0`——等等，实际上 RTD 已经有去 10.0 的路由了。问题在于 **RTA/RTB/RTC 没有去 192.168.200.0/24 的路由**，所以回包丢失。
## Q4-1.15
![[Pasted image 20260516164009.png]]

## Q4-1.16
![[Pasted image 20260516164114.png]]
## Q4-1.17
![[Pasted image 20260516164130.png]]
## Q4-1.18
![[Pasted image 20260516164132.png]]
## Q4-1.19
![[Pasted image 20260516164132.png]]
## Q4-1.20
![[Pasted image 20260516164334.png]]
现在可以ping通了。