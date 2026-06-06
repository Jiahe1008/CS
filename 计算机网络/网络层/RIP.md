**Routing Information Protocal**

distributed, based on **distance-vector routing.**

use **hop** as distance. (max = 15)
the hop of direct attached network is 1. 

every **30s**, exchange distance vector via **Response Message(advertisement)** with the **neighbor routers** using **[[UDP]]** (port 520)

Each advertisement: route to up to 25 destination nets


