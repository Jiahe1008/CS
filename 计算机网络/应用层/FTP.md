File Transfer Protocol

use C/S model

Based on [[TCP]]

FTP server process:
1. main process
2. slave process

use well known port **21**

use two parallel TCP connection: control  , data
**control connection**

**data connection**
	PORT
		the server connection to the host actively by port **20**
	PASV
		host send PASV command to server, server response with a random port.