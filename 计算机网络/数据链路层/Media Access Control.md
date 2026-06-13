## channel allocation

```
OSI Model              LAN Model
-------------          --------------
network layer          network layer   
-------------          --------------
data link layer  -->   LLC sublayer

					   MAC sublayer
-------------          --------------        
physical layer         physical layer
-------------          --------------
```

### Channel partitioning
#### TDMA
#### FDMA
#### CDMA

### Random-access
no a priori coordination among nodes.
	how to detect collisions
	how to recover from collisions (delayed retransmissions)
#### ALOHA
##### pure ALOHA
**the ==throughput== for pure ALOHA is $$S = G \times e^{-2G}$$
S is throughput, G is offered load.(frames attempt to send per seconds, including new frames and retransmitted frames)
>[!note]+ why $e^{-2G}$
> the danger period is 2 times of frame transmission time.


#### CSMA
#### CSMA/CD

##### 1-persistent CSMA/CD
##### p-persistent CSMA/CD
#### CSMA/CA

### Controlled-access protocols
#### Reservation
#### pooling
#### token passing


