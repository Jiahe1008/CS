
### format

email address:
```
local_part @ domain name
```

**MIME**
multipurpose Internet Mail Extensions

**convert non-ASCII to ASCII,** and then use SMTP. (push)
**convert ASCII to non-ASCII** （pull)


### protocol

sending protocol
**SMTP**
	Simple Mail Tranfer Protocol 
	C/S mode
	end to end, no transit email server.(TCP connect between Sender SMTP server and Receiver SMTP server)
	only support 7 bit ASCII transmission. so using **MIME** as an extension.
	use well known port **25** as destination port.


receiving protocol
**POP3**
	Post Office Protocol 
	current version POP3
	1. download and reserve
	2. download and delete
	**user agent** use pop3 address.

**IMAP**


**email service based on Web**