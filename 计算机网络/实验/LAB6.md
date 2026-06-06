## Q6-1.1
![[Pasted image 20260520175902.png]]
## Q6-1.2
![[Pasted image 20260520175921.png]]

![[Pasted image 20260520175941.png]]
![[Pasted image 20260520180004.png]]
![[Pasted image 20260520180014.png]]
![[Pasted image 20260520180025.png]]
![[Pasted image 20260520180038.png]]
## Q6-1.3
![[Pasted image 20260520180014.png]]
![[Pasted image 20260520180025.png]]
![[Pasted image 20260520180038.png]]
## Q6-1.4
![[Pasted image 20260520180014.png]]
![[Pasted image 20260520180025.png]]
![[Pasted image 20260520180038.png]]
## Q6-1.5
![[Pasted image 20260520180014.png]]
![[Pasted image 20260520180025.png]]
![[Pasted image 20260520180038.png]]
## Q6-1.6
![[Pasted image 20260520180148.png]]
## Q6-1.7
![[Pasted image 20260520180151.png]]
## Q6-1.8
![[Pasted image 20260520180210.png]]
## Q6-1.9
![[Pasted image 20260520181536.png]]
- **Hello** — 发现邻居
- **DD（Database Description）** — 交换 LSDB 摘要
- **LSR（Link State Request）** — 请求缺少的 LSA
- **LSU（Link State Update）** — 发送完整 LSA
- **LSAck（Link State Acknowledgment）** — 确认收到
## Q6-1.10
当新路由器（如 RTC、RTD）加入 OSPF 后，LSA 会被泛洪传播：

- RTD 生成新 LSA → 传给 RTC → RTC 作为 ABR 生成 Type 3 Summary LSA → 传给 RTB → RTB 再转给 RTA
- RTA 收到新 LSA 后重新运行 SPF 算法更新路由表
## Q6-1.11
|接口|区域|包类型|
|---|---|---|
|GE0/0/0（连RTA，Area 1）|Area 1|Hello、DD、LSR、LSU、LSAck|
|GE0/0/1（连RTC，Area 0）|Area 0|Hello、DD、LSR、LSU、LSAck|
## Q6-1.12
![[Pasted image 20260520181648.png]]
## Q6-1.13
![[Pasted image 20260520181650.png]]
## Q6-1.14
![[Pasted image 20260520181736.png]]
## Q6-1.15
- RTA 在 Area 1 生成 **Type 1 Router LSA**
- RTB（ABR）收到后，生成 **Type 3 Summary LSA** 发入 Area 0
- RTC（ABR）收到 Area 0 的 Type 3 LSA，再生成新的 **Type 3 Summary LSA** 发入 Area 2
- RTD 收到，学到 Area 1 的路由
## Q6-1.16
- RTD 生成 **Type 1 Router LSA** 在 Area 2 内泛洪
- RTC（ABR）转换为 **Type 3 Summary LSA** 进入 Area 0
- RTB（ABR）再转换为 **Type 3 Summary LSA** 进入 Area 1
- RTA 学到 Area 2 的路由（包括 192.168.30.0 和 192.168.200.0）