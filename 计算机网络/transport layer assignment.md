## 1
1.
```
destination MAC: 34-c9-3d-1d-e1-e7
source MAC:      f0-33-e5-7a-cf-ed
```

2.
```
source IP address:      0a 00 06 ca -> 10.0.6.202
destination IP address: oa 3e 21 ec -> 10.62.33.236
```
3.
```
TCP
```
4.
```
source port: 49179  动态，私有，临时端口
destination port: 3533 registered port 
```


## 2
1. 在一个RTT内，能够传输$10^9*0.1/8$ = 12500000B数据，而$2^{23} < 12500000 < 2^{24}$, 所以window至少需要24位。
2. 30秒能够传输 $30 * 10^9 / 8$ = 3750000000 $\in [2^{31}, 2^{32}]$ 所以需要32位。
## 3
(a) 1-5
(b) 6-15
(c) detected by a triple dulicate ACK
(d) timeout
(e) 32
(f) 21
(g) 10
(h) 6th
(i)ssthresh = congestion window size = 4
(j) ssthresh = 16, congestion window = 1
(k)1+2+4+8+16+17 =  48
## 4
(a) 1024 = $2^{10}$  need 10 RTTs
(b) 20RTTs
(c) T = 50ms $* $ 20 = 1s
	bandwidth usage = $\frac{10 * 10^6 *8}{10^9} = 8\%$
    throughput: 80Mbps	


