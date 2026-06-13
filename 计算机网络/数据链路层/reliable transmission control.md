

### Stop and Wait Protocol


### Sliding Window Protocol

**stop and wait protocal** is a sliding window protocol with windows size = 1.

channel utilization:$$U = \frac{t_f}{t_T} = \frac{t_{f}}{RTT + t_f} = \frac{1}{1+2\alpha},\,\,\,\,\,\,\,\, \alpha = \frac{t_{trans}}{t_{prop}}$$
	$t_f$ is the time of data transmission. $t_T$ is the time period between two data sending.
	the formula ignore the **ack receving time $t_A$**

#### Automatic Repeat reQuest ARQ

**Stop-and-Wait**
channel utilization: $$U = \frac{T_D}{T_D + RTT + T_A}$$
$T_D$ is the transmission time. $T_A$ is the time receiver receive deal with a ack frame.

**Go-Back-N**

sequence number : N, the window size should satisfy: $1< W_T < N$       
the sending window is N, receving window is 1.

**Selective Repeat**

sequence number: N, the window size(sending window and receving window)：
$$W_S + W_R \leq N ,\,\,\,\, W_S \geq W_R$$

channel utilization:
	1. $NT_D < T_D + RTT + T_A$:$$U = \frac{NT_D}{T_D + RTT + T_A}$$
	2. $NT_D \geq T_D + RTT + T_A$: $$U=1$$
	that means sender sending message uninterrupted(assume no error). the channel is fully used.


