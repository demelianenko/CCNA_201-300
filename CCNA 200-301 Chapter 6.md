# CCNA 200-301 Chapter 6

## password versus secret

```
enable password
```

Will store the password in plain text versus 

```
enable secret
```

 That will store the password in an encrypted matter.

## Console tty password

- use the line con 0 command to enter the console config mode
- use the password password-value sub command to se the value of the console password
- use the login subcommand to enable console security using plain text password.

same thing for telnet but its will be vty 0 15 instead of con 0

## Secure usermod

instead of 1 shared password for tty and console we will user username and passwords combo

to enable this feature you need to use the command

```
login local
```

use the command to remove any passwords remaining

```
no password
```

###  Setting up local login

- use the following command in global config to add a login locally 

  ```
  username "name" secret "password" 
  ```

- we want to configure console to use the local login, well need to use:

  ```
  line con 0
  ```

  ```
  login local
  ```

  optional to use no password to clean up any remaining passwords 

  for tty its would be the same commands but 

  ```
  line vty 0 15
  ```



## Setting up ssh login

set hostname using:

```
hostname "SW1"
```

```
ip domain-name "exmaple.com"
```

```
crypto key generate rsa
```

```
ip ssh version 2 
```

 afterword's setup local login (see above)

### Disable or enable remote protocols

use the following command defending on that protocol you wish to use

```
transport input all
transport input telnet
transport input ssh
```

