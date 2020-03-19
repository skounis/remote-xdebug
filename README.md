# Remote Xdebug
Debug a remote server with XDebug.

## Overview 
The IDE (e.g. PHPStorm) is runnung in the local machine and is listeing for XDebug connections from a remote server. 

XDebug port: `9000` 

## Establish and test an SSH tunnel. 
Redirecting all requests from the server that come to port `9000` to the local machine on port `9000`

```
ssh -R 9000:localhost:9000 server_ip_hostname_or_config_alias
```
If the XDebug runs within a Docker containter in the server setup a the tunnel with the command:

```
ssh -R \*:9000:localhost:9000 server_ip_hostname_or_config_alias
```
Which makes the tunnel open to the public. 

### Allow Remote connection 
> By default, OpenSSH only allows connecting to remote forwarded ports from the server host
Edit the `sshd_config`
```
sudo joe /etc/ssh/sshd_config
```
Set
```
GatewayPorts yes
```
And restart SSH
```
sudo service ssh restart
```
Confirm that port `9000` accepts connection from any host

Run
```
sudo netstat -plnt
```
And confirm that you get `0.0.0.0` in the Local Address.
```
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:9000            0.0.0.0:*               LISTEN      49998/sshd: skounis 
```
### Test the tunnel 
In the local machine start a server that is listening to port `9000`
```
nc -l 9000
```

In the server connect to port `9000` and send some messages (text) 
```
nc localhost 2399
```

Any character we type should apear in the terminal in the local machine. 

## Debug with PHPStorm
### Deployment Server
Go to "Preferences > Build, Execution, Deployment > Deployment". 

Create a new server 
![New Server](https://github.com/skounis/remote-xdebug/blob/master/xdebug-phpstorm-00.png)

And set

Connection
![Connection](https://github.com/skounis/remote-xdebug/blob/master/xdebug-phpstorm-01.png)

Mappings
![Mappings](https://github.com/skounis/remote-xdebug/blob/master/xdebug-phpstorm-02.png)


### PHP Server
Go to "Preferences > Languages & Frameworks > PHP > Servers". 

Create a new server 
![New Server](https://github.com/skounis/remote-xdebug/blob/master/xdebug-phpstorm-03.png)

#### Local files
You need to have an exact copy of the files in the local filesystem. 
1. Set the IP of the server 
2. Map the files by using absolute paths for local and remote folders. 

#### Validate the server 
Select "Run > Web Server Debug Validation"
1. Select Remote Web Server
2. Set the path to the folder that is server by the webserver
3. Select the Deployment server 
4. Press "Validate" 

![Validate](https://github.com/skounis/remote-xdebug/blob/master/xdebug-phpstorm-04.png)

### Listen for connections
Select the PHP Server and start listening for connection.

![Validate](https://github.com/skounis/remote-xdebug/blob/master/xdebug-phpstorm-05.png)

### Browser 
Install a plugin that sets the debug session 
-  Chrome: https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc

Visit the public URL with the plugin enabled.

PHPStorm expects the cookie value `XDEBUG_SESSION=PHPSTORM;` in the request header. 

## References
- https://help.ubidots.com/en/articles/937233-sending-tcp-udp-packets-using-netcat
- https://crosp.net/blog/software-development/web/php/understanding-and-using-xdebug-with-phpstorm-and-magento-remotely/
