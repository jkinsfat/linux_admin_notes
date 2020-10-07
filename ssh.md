# SSH Login

`ssh user@<ip_address>`

#Running Remote Command
`ssh user@<ip_address> {'|"}YOUR_REMOTE_COMMAND{'|"}`

#Configuring Password-less Login

1. Check for existing SSH key pair (Would be sad to overwrite :( )

`ls -al ~/.ssh/id_*.pub`

2. Generate an RSA keypair
`ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"`

- Name the file to save the key (Prefer id_* to easily list with Regex)
- Enter a secure passphrase (or none if you wish to automate use)

3. Copy the Public Key to Server

`ssh-copy-id user@<ip_address>`

Enter the remote user's password.
Key is appended to the remote user's authorized_keys file. Connection is then closed.

 - Manually Copy 
`cat ~/.ssh/id_<key_name>.pub | ssh user@<ip_address> "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"

#Disable SSH Password Authentication

1. Login as user with sudo privileges or as root
`ssh sudo_user@<ip_address>` 

2. Open SSH config file and modify the following directives

/etc/ssh/sshd_config
`PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM no`

3. Save config file and restart the SSH service.
`sudo systemctl restart ssh` - On Ubuntu or Debian
`sudo systemctl restart sshd` - On CentOS or Fedora

#SSH Client-side Config File

```
Host hostname1
	SSH_OPTION value
	SSH_OPTION value

Host hostname2
	SSH_option value

Host *
	SSH_option value
```

- * matches zero or more characters. Host * matches all hosts, while 192.168.0.* matches hosts in the 192.168.0.0/24 subnet
- ? matches exactly one character. 8.10.0.? matches 8.10.0.[0-9] 
- ! Negates the match. Host 10.10.0.* !10.10.0.5 matches any host in the 10.10.0.0/24 subnet except 10.10.0.5

File read stanza by stanza. First matching stanza options override option declarations in later matches.

```
Host HOST_ALIAS
	HostName HOST_IP_ADDRESS
	User USER_NAME
	Port PORT_NUMBER
	IdentityFile SSH_PRIVATE_KEY_PATH
	LogLevel INFO
	Compression yes
```

Override options on command line:
- -o option to override single option declaration
- -F (configfile) Specify a different configuration file

#SSH Tunneling || SSH Port Forwarding
- Local Port Forwarding
	Forwards a connection from the client host to the SSH server host and then to the destination host port.

	`ssh -L [LOCAL_IP]LOCAL_PORT:DESTINATION:DESTINSTION_PORT [USER@]SSH_SERVER`

	- [LOCAL_IP:]LOCAL_PORT - The local machine ip and port number. When LOCAL_IP is omitted the ssh client binds on localhost.
	- DESTINATION:DESTINATION_PORT - The IP or hostname and the port of the destination machine.
	- [USER@]SERVER_IP - The remote SSH user and server IP address.

	Remember that any port number less than 1025 is privileged and can only be used by root. If your server is listening on a port other than 22, use the -p [PORT_NUMBER] option.
	
	EXAMPLE - A database on machine db001.host on an internal network, on port 3306, accessible from machine pub001.host. You want to connect using you local machine mysql client to the database server. 
	`ssh -L 3336:db001.host:3306 user@pub001.host`
	
	EXAMPLE - Connect to remote machine through VNC (Virtual Network Computing) which runs on the same private server. 
	`ssh -L 5901:127.0.0.1:5901 -N -f user@remote.host`
	
	The -f option tells the ssh command to run in the background.
	The -N option means do not execute a remote command. We use localhost because the VNC and  SSH server are running on the same host. 

	! AllowTcpForwarding must be set to yes to allow Local Port Forwarding !
- Remote Port Forwarding
	Forwards a port from the server host to the client host and then to the destination host port.
	Remote port forwarding is mostly used to give access to an internal service to someone on the outside.

	`ssh -R [REMOTE:]REMOTE+PORT:DESTINATION:DESTINATION_PORT [USER@]SSH_SERVER
	- [REMOTE:]REMOTE_PORT - the IP and the port number on the remote SSH server. An empty REMOTE means the remote SSH server will bind on all interfaces.
	- DESTINATION:DESTINATION_PORT - THe IP or hostname and the port of the destination machine.
	- [USER@]SERVER_IP - The remote SSH user and server IP address.	

	EXAMPLE - Use accessible SSH server to listen on port 8080 and tunnel all traffic from this port to your local machine on port 3000.
	`ssh -R 8080:127.0.0.1:3000 -N -f user@remote.host`
	
	! GatewayPorts must be set to yes to allow remote port forwarding.
- Dynamic Port Forwarding
Creates SOCKS proxy server which allows communication across a range of ports.

`ssh -D [LOCAL_IP:]LOCAL_PORT [USER@]SSH_SERVER`

	- [LOCAL_IP:]LOCAL_PORT - The local machine ip and port number. When LICAL)IP is omitted the ssh client binds on localhost. 
	- [USER@]SERVER_IP - The remote SSH user and server IP address. 

EXAMPLE - Create a SOCKS tunnel on port 9090:
`ssh -D 9090 -N -f user@remote.host`

