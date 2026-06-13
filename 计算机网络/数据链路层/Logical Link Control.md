
### Frame

**bit stuffing**
	在帧首部设置计数字段，记录该帧包含的总字节数(包含自身的1B)

**byte/char stuffing**
	insert  a character: **ESC** before spetial character( FLAG, ESC,...)  to enable **transparent transmission**

**zero bit padding**
	insert a 0 after every 5 continuous 1:  1111111111  ->  111110111110

## Error Control 
#### Error detection

**Parity Check**
1. single 
2. 2-demension

**[[Cyclic Redundancy Check]]** CRC
#### Error connection

**Hamming Distance**

**Hamming Code**

## [[reliable transmission control]]

## [[HDLC]]

## [[PPP]]



