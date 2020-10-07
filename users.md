# Users
## Add a User

`adduser USER_NAME`

## Grant Admin Privileges

`usermod -a -G adm USER_NAME`

`usermod -a -G sudo USER_NAME`

## Lock User Login
### Lock user Password

`usermod -L USER_NAME`

`passwd -l USER_NAME`

`usermod -p "!" USER_NAME`

### Expire the account

Locking the user password will not fully disable/lock the user account. The account will still be accessible by SSH Keys or PAM modules besides pam_unix.

`chage -E0 USER_NAME`

### Change the Shell to NoLogin

`usermod -s /sbin/nologin USER_NAME`

## Get a List of All Users

Local User Information is stored in the /etc/passwd file. 

`cat /etc/passwd` or `getent passwd`

- User name
- Encrypted Password. (x means that the password is in the /etc/shadow file)
- UID User ID number
- GID User's Group ID Number
- GECOS Fullname of the User
- User home directory
- Login Shell (defaults to /bin/bash)

### Check UID Max and Min

`grep -E '^UID_MIN|^UID_MAX' /etc/login.defs`

Tells us all normal users should have UID between UID_MIN and UID_MAX

### List all Normal Users

`getent passwd {UID_MIN..UID_MAX}`

`eval getent passwd {$(awk '/^UID_MIN/ {print $2}' /etc/login.defs)..$(awk '/^UID_MAX/ {print $2}' /etc/login.defs)}`


