# Passowrdless Authenticaiton 
## EC2 Instances

### Using Public Key

```
ssh-copy-id -f "-o IdentityFile <PATH TO PEM FILE>" ubuntu@<INSTANCE-PUBLIC-IP>
```

- ssh-copy-id: This is the command used to copy your public key to a remote machine.
- -f: This flag forces the copying of keys, which can be useful if you have keys already set up and want to overwrite them.
- "-o IdentityFile <PATH TO PEM FILE>": This option specifies the identity file (private key) to use for the connection. The -o flag passes this option to the underlying ssh command.
- ubuntu@<INSTANCE-IP>: This is the username (ubuntu) and the IP address of the remote server you want to access.

Note: You must have the pem file on control node. If you don't have, then make/download it. 

### Using Password 

### Mannage node 

First do PasswordAuthenticaion on by:

```
sudo vim /etc/ssh/sshd_config.d/60-cloudimg-settings.conf 
 ```
Restart ssh by:

```
sudo systemctl restart ssh
```

Set the password

```
sudo  passwd ubuntu
```

## Control Node

First generate the key by 

```
ssh-keygen -t rsa -b 4096
```

Assign the previlage to the key 

```
chmod 600 /home/ubuntu/.ssh/id_rsa
```

Copy key to mannage node 

```
ssh-copy-id -i .ssh/id_rsa  ubuntu@<Ip-Address>
```

Run this 

```
ssh ubuntu@<Ip-Address>
```

Congratulations you are now on your node server.
