**A noiseless 4-kHz channel is sampled every 1 msec. What is the maximum data rate?**
	sampled channel 1msec -> 1k Hz the noiseless: nyquist theorem: C = 2B = 2kb/s

**If a binary signal is sent over a 3-kHz channel whose signal-to-noise ratio is 20 dB, what is the maximum achievable data rate?**
	20dB -> $10\log_10(S/N) = 20 \rightarrow S/N = 100$ 
	$C = 2B\log2(101) = 2\times 3000 \times 6.64 = 39.84kb/s$ 

  **Compare the delay in sending an x-bit message over a k-hop path in a circuit switched network and in a (lightly loaded) packet-switched network. The circuit setup time is s sec, the propagation delay is d sec per hop, the packet size is p bits, and the data rate is b bps. Under what conditions does the packet network have a lower delay?**
	  circuit switched network:
		  1. setup time s
		  2. transmission delay = x/b
		  3. propogation delay = k\*d
	package-switched network:
		4. packages x/p
		5. transmission delay = p/b
		6. first arrive = (p/b + d)\*k
		7. after that ,every p/b arrive a package : p/b(x/p - 1)
	$$s \le \frac{(k-1)p}{b}$$

**Suppose that x bits of user data are to be transmitted over a k-hop path in a packet switched network as a series of packets, each containing p data bits and h header bits, with x>>p + h. The bit rate of the lines is b bps and the propagation delay is negligible. What value of p minimizes the total delay?**
	number of packages: x/p
	transmission delay = (p+h)/b
	propogation delay = ignored
	first arrive (p+h)k/b
	after that, take time: (p+h)/b \* (x/p - 1)
	total time: $$\frac{(p+h)k}{b} + \frac{(p+h)(\frac{x}{p}-1)}{b} = \frac{(p+h)(k+\frac{x}{p}-1)}{b}$$$$T = \frac{pk+x-p+hk+\frac{hx}{p}-h}{b}\rightarrow p = \sqrt{\frac{hx}{p-1}}$$

**A channel has a bit rate of 4 kbps and a propagation delay of 20 msec. For what range of frame sizes does stop-and-wait give an efficiency of at least 50 percent?**
	set frame sizes of S
	transmission delay $T_D = S/4000$
	propagation delay 20msec, then the RTT is 40 msec = 0.04 msec
	$$\frac{T_D}{T_D + RTT} \geq0.5$$
	means $T_D \geq RTT\rightarrow \frac{S}{4000} \geq 0.04\rightarrow S \geq 160$

**Consider an error-free 64-kbps satellite channel used to send 512-byte data frames in one direction, with very short acknowledgements coming back the other way. What is the maximum throughput for window sizes of 1, 7, 15, and 127? The earth-satellite propagation time is 270 msec**
	the throughput = efficiency \* channel rate
	transmission delay =$\frac{512 * 8}{64 * 1000} = 64$msec
	propogation time 270msec, RTT = 540msec
	the total T is 64 + 540 = 604 msec.
	604 / 64 = 9.
	so the efficiency of window size 15, 127 is 1, the throughput is 64kbps.
	beyond that, the efficiency of window size 1 is 0.1, throughput is 6.4kbps.
	the efficiency of window size 7 is 0.7, throughput is 44.8kbps.
	

**A 1-km-long, 10-Mbps CSMA/CD LAN (not 802.3) has a propagation speed of 200 m/µsec. Repeaters arenot allowed in this system. Data frames are 256 bits long, including 32 bits of header, checksum, and other overhead. The first bit slot after a successful transmission is reserved for the receiver to capture the channel in order to send a 32-bit acknowledgement frame. What is the effective data rate, excluding overhead, assuming that there are no collisions?**
	propogation delay $T_p = 1000/(200 * 10^6) = 5 \mu s$ 
	RTT = $10\mu s$ 
	transmission delay of data $T_t = 256 / 10^7 = 25.6\mu s$
	transmission delay of ack $T_A = 3.2 \mu s$
	effective data rate = $\frac{25.6}{3.2 + 25.6 + 10} * \frac{256 - 32}{256}*10 = 5.77Mbps$
	
**Consider building a CSMA/CD network running at 1 Gbps over a 1-km cable with no repeaters. The signal speed in the cable is 200,000 km/sec. What is the minimum frame size**
	propogation delay $T_t = 1k/200000k = 10k/2000000k = 5\mu s$
	RTT = $10 \mu s$
	minimum frame size = $1 * 10^9 *10 * 10^6 = 1024 * 10bit = 1280B$


