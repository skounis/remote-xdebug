# Remote Xdebug
Debug a remote server with XDebug.

## Overview 
The IDE (e.g. PHPStorm) is runnung in the local machine and is listeing for XDebug connections from a remote server. 

XDebug port: `9000` 

### Establish and test an SSH tunnel. 
Redirecting all requests from the server that come to port 9900 to the local machine on port 9000

```
ssh -R 9000:localhost:9000 server_ip_hostname_or_config_alias
```

#### Test the tunnel 
In the local machine start a server that is listening to port `9000`
```
nc -l 9000
```

In the server connect to port `9000` and send some messages (text` 
```
nc localhost 2399
```

Any character we type should apear in the terminal in the local machine. 

## References
- https://help.ubidots.com/en/articles/937233-sending-tcp-udp-packets-using-netcat
- https://crosp.net/blog/software-development/web/php/understanding-and-using-xdebug-with-phpstorm-and-magento-remotely/
