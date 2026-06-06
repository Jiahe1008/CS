# ARP: Address Resolution Protocol

the ARP (and RARP) perform the translation between IP addresses and MAC layer addresses

### ARP Cache
maintaining a **ARP cache**:

| IP address | MAC address | Age |
| ---------- | ----------- | --- |
|            |             |     |
```bash
arp -a
```
can show the ARP cache table of the PC

### ARP request
if the mac address of the IP not in ARP Cache, then an ARP request will be broadcast.

broadcast. broadcast address: FF-FF-FF-FF-FF-FF
the router who has the corresponding IP will reply.
