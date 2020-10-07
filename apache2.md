# Apache Web Server 2
## Check Version

`httpd -v`

## Security
### Remove Apache Version and Operating System from Error Report

1. Open Apache2 config file:
 /etc/httpd/conf/httpd.conf on RHEL/CentOS/Fedora
 /etc/apache2/apache2.conf on Debian/Ubuntu

2. Add or Alter Option Statements/:

	ServerSignature Off
	ServerTokens Prod

3. Restart Apache2 Service

`service {httpd|apache2} restart'

`systemctl restart apache2.service`

### Disable Directory Listing 
1. Open Apache2 config file.
2. Modify or Make entry -  

Remember that if one option in declaration begins with '-' or '+' all options in the declaration must

	<Directory /var/www/html>
		Options -Indexes
	</Directory>

3. Restart Apache.


