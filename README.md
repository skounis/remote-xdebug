# Remote Xdebug
Debug a remote server with XDebug and PHPStorm

## Overview 
The IDE (e.g. PHPStorm) is runnung in the local machine and is listening for XDebug connections from a remote server. 

XDebug port: `9000` 

## Establish and test an SSH tunnel. 
Redirecting all requests from the server that come to port `9000` to the local machine on port `9000`

```
ssh -R 9000:localhost:9000 server_ip_hostname_or_config_alias
```
If the XDebug runs within a Docker container in the server, setup the tunnel with the command:

```
ssh -R \*:9000:localhost:9000 server_ip_hostname_or_config_alias
```
This makes the tunnel open to the public. 

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
nc localhost 9000
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
- [Sending TCP/UDP packets using Netcat](https://help.ubidots.com/en/articles/937233-sending-tcp-udp-packets-using-netcat)
- [Understanding and using Xdebug with PHPStorm and Magento remotely](https://crosp.net/blog/software-development/web/php/understanding-and-using-xdebug-with-phpstorm-and-magento-remotely/)
- [SSH Port Forwarding Example](https://www.ssh.com/ssh/tunneling/example)
- [Check listening ports](https://support.rackspace.com/how-to/checking-listening-ports-with-netstat/)
- [How to make ssh tunnel open to public?](https://superuser.com/questions/588591/how-to-make-ssh-tunnel-open-to-public)
- [SSH Tunnel - Local and Remote Port Forwarding Explained With Examples](https://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html)
- [sshd_config - SSH Server Configuration](https://www.ssh.com/ssh/sshd_config)
### PHPStorm 
- [Create new PhpStorm project from remote server with automatic sync of source files](https://www.webfoobar.com/node/92)
