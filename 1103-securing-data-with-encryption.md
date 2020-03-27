# 110.3. Securing data with encryption

**Weight:** 3

**Description:** The candidate should be able to use public key techniques to secure data and communication.

**Key Knowledge Areas:**

* Perform basic OpenSSH 2 client configuration and usage
* Understand the role of OpenSSH 2 server host keys
* Perform basic GnuPG configuration, usage and revocation
* Understand SSH port tunnels \(including X11 tunnels\)

**Terms and Utilities:**

* ssh
* ssh-keygen
* ssh-agent
* ssh-add
* ~/.ssh/id\_rsa and id\_rsa.pub
* ~/.ssh/id\_dsa and id\_dsa.pub
* /etc/ssh/ssh\_host\_rsa\_key and ssh\_host\_rsa\_key.pub
* /etc/ssh/ssh\_host\_dsa\_key and ssh\_host\_dsa\_key.pub
* ~/.ssh/authorized\_keys
* ssh\_known\_hosts
* gpg
* ~/.gnupg/

First lets start about some concepts.

## Cryptography

 Cryptography is a method of using advanced mathematical principles in storing and transmitting data in a particular form so that only those whom it is intended can read and process it. Encryption is a key concept in cryptography

* **Encryption** :In cryptography, encryption is the process of encoding a message or information in such a way that only authorized parties can access it and those who are not authorized cannot.
* **Decryption**: The conversion of encrypted data into its original form is called Decryption. It is generally a reverse process of encryption.

### Symmetric vs. asymmetric encryption

Encryption algorithms are often divided into two categories, known as symmetric and asymmetric encryption.

#### Symmetric Encryption

![](.gitbook/assets/securedata-symetric.jpg)

Symmetric encryption is the oldest and best-known technique. A secret key, which can be a number, a word, or just a string of random letters, is applied to the text of a message to change the content in a particular way. This might be as simple as shifting each letter by a number of places in the alphabet. As long as both sender and recipient know the secret key, they can encrypt and decrypt all messages that use this key.

#### Asymmetric Encryption

The problem with secret keys is exchanging them over the Internet or a large network while preventing them from falling into the wrong hands. Anyone who knows the secret key can decrypt the message. One answer is asymmetric encryption, in which there are two related keys--a key pair. A public key is made freely available to anyone who might want to send you a message. A second, private key is kept secret, so that only you know it.

![](.gitbook/assets/securedata-asymetric.jpg)

Any message \(text, binary files, or documents\) that are encrypted by using the public key can only be decrypted by applying the same algorithm, but by using the matching private key. Any message that is encrypted by using the private key can only be decrypted by using the matching public key.  
  
This means that you do not have to worry about passing public keys over the Internet \(the keys are supposed to be public\).

> A problem with asymmetric encryption, however, is that it is slower than symmetric encryption. It requires far more processing power to both encrypt and decrypt the content of the message.

With that introduction lets talk about SSH.

## Whats is SSH?

The SSH protocol \(also referred to as Secure Shell\) is a method for secure remote login from one computer to another. It provides several alternative options for strong authentication, and it protects the communications security and integrity with strong encryption. It is a secure alternative to the non-protected login protocols \(such as telnet, rlogin\) and insecure file transfer methods \(such as FTP\).

Typical uses of the SSH protocol are:

* providing secure access for users and automated processes
* interactive and automated file transfers
* issuing remote commands
* managing network infrastructure and other mission-critical system components.

### How does the ssh protocol work?

The way SSH works is by making use of a client-server model to allow for authentication of two remote systems and encryption of the data that passes between them.

SSH operates on TCP port 22 by default \(though this can be changed if needed\). The host \(server\) listens on port 22 \(or any other SSH assigned port\) for incoming connections.

SSH provides multiple mechanisms for authenticating the server and the client. Two of the commonly used authentication mechanism are password based, and key based authentication. Although password based authentication is also secure, its advisable to use key based authentication instead.

![](.gitbook/assets/ssh-howitworks.jpg)

the connection is established by the SSH client connecting to the SSH server. The SSH client drives the connection setup process and uses public key cryptography to verify the identity of the SSH server. After the setup phase the SSH protocol uses strong symmetric encryption and hashing algorithms to ensure the privacy and integrity of the data that is exchanged between the client and server.

### What is OpenSSH?

OpenSSH is a free, open source implementation of the SSH \(Secure Shell\) protocols. Open OpenSSH is so popular among system administrators because of its multi-platform capability and very useful nice features.

![](.gitbook/assets/openssh-logo.png)

All communications and user credentials using OpenSSH are encrypted, they are also protected from man in the middle attacks. If a third party tries to intercept our connection, OpenSSH detects it and informs us about that.

{% hint style="success" %}
We use Ubuntu16-1 as ssh server and Ubuntu16-2 as client.
{% endhint %}

#### /etc/ssh

OpenSSH has two different sets of configuration files: one for client programs \(ssh, scp, and sftp\) and one for the server daemon \(sshd\).

```text
root@ubuntu16-1:~# ls -1 /etc/ssh
moduli
ssh_config
sshd_config
ssh_host_dsa_key
ssh_host_dsa_key.pub
ssh_host_ecdsa_key
ssh_host_ecdsa_key.pub
ssh_host_ed25519_key
ssh_host_ed25519_key.pub
ssh_host_rsa_key
ssh_host_rsa_key.pub
ssh_import_id
```

The`sshd_config`is the ssh  **daemon**\(or ssh server process\) configuration file, Whereas, the `ssh_config` file is the ssh client configuration file. The client configuration file only has bearing on when you use the ssh command to connect to another ssh host . As you can see there are public keys and private keys here with different algorithems and they can be used by SSH to encrypt the session.

## ssh client configurations

Till now we have understood how ssh works. As we mentioned when ssh connection is started, the public key of ssh server is tranfered to the client\(stored in ./ssh/known\_hosts\) and the client will use it to continue negotiation with the server and user will be required to get authenticated by sending username and password.

Lets start by connecting toUbuntu16-1 from Ubuntu16-2 and see the keys:

```text
user1@ubuntu16-2:~$ ssh user1@192.168.52.146
The authenticity of host '192.168.52.146 (192.168.52.146)' can't be established.
ECDSA key fingerprint is SHA256:GV/PpX9YGvMZTAbuz6w3zBDreokesZHhVSM1zrXmHLw.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.52.146' (ECDSA) to the list of known hosts.
user1@192.168.52.146's password: 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 15:51:55 2020 from 192.168.52.133
user1@ubuntu16-1:~$ 
user1@ubuntu16-1:~$ cat /etc/ssh/ssh_host_ecdsa_key.pub 
ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIjnKq9Wr0C2faQCf4+gcqPN4bOMFyx1nywTjLS/mh/S30V0r/mvy9cvfWvA2LY/y7zqxg+/gMvELQznikQiaTo= root@server1

```

```text
user1@ubuntu16-2:~$ tree .ssh/
.ssh/
└── known_hosts

0 directories, 1 file
user1@ubuntu16-2:~$ cat .ssh/known_hosts 
|1|gD2R1tW6jKBSyL1A0XpynmG8Vok=|1X2nzVcwHLQGT5T8FOUxeCejtvQ= ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIjnKq9Wr0C2faQCf4+gcqPN4bOMFyx1nywTjLS/mh/S30V0r/mvy9cvfWvA2LY/y7zqxg+/gMvELQznikQiaTo=

```

### Configuring SSH Key Based authentication

Its possible to omit entring user name and password and get connected to the ssh server using client public and private key.

![](.gitbook/assets/ssh-keybasedauth.jpg)

Now lets generate public and private keys for client and copy client public key to the server.

### ssh-keygen

ssh-keygen - creates a key pair for public key authentication:

```text
user1@ubuntu16-2:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user1/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user1/.ssh/id_rsa.
Your public key has been saved in /home/user1/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:rer76yQU+8Mmrg33xCA51RtnpTUVHT1cVMX7kB2+aUI user1@ubuntu16-2
The key's randomart image is:
+---[RSA 2048]----+
|            +.+*@|
|       .   + . ++|
|      o o +   .o+|
|     o o *   Eoo.|
|    + + S . .  .+|
|     + = .   . +.|
|    . + X     o  |
|     = O .       |
|    .o*+=.       |
+----[SHA256]-----+
user1@ubuntu16-2:~$ 
user1@ubuntu16-2:~$ tree .ssh
.ssh
├── id_rsa
├── id_rsa.pub
└── known_hosts

0 directories, 3 files
user1@ubuntu16-2:~$ cat .ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC818rxUXbwnwwxtFhUKvgtZV+Ygao1nUEHcaBvYEsXsBa4hQcV0+ITPEMHfk0zUag3sKyQZWckKmREK+lpiF+7Nrw83eKxjgHdwZC0ibxPTenklZNSBEMZMBDeq8H3bKoAfuyczX0IfVDA2Iyebsg2KYIIZQ/Otw7hAm2IH3perUqzeYliLhIYb0Gd3jyOpl4VMaPb2p+f5+fG87MnzjDplyorruhZyUcv8CMUc6XZ3dJjeiSsNNCRKLEb6Cm6msQxuCUq+Xz1n0+ay6fsaJbbhzFwNvbRH2YSzBg5BmtBXVt68U6XM3SzynWAqQaDS44Cuv3M1q88baTlOTjRkFRZ user1@ubuntu16-2
user1@ubuntu16-2:~$ 


```

We haven't set passphrase in our demonstration but if we set we would be asked to enter it when we copy it to the server. 

### ssh-copy-id

we use ssh-copy-id - configures a public key as authorized on a server 

```text
user1@ubuntu16-2:~$ ssh-copy-id -i .ssh/id_rsa.pub user1@192.168.52.146
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ".ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
user1@192.168.52.146's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'user1@192.168.52.146'"
and check to make sure that only the key(s) you wanted were added.
```

Now lets take a look the server side:

```text
user1@ubuntu16-1:~$ cat .ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC818rxUXbwnwwxtFhUKvgtZV+Ygao1nUEHcaBvYEsXsBa4hQcV0+ITPEMHfk0zUag3sKyQZWckKmREK+lpiF+7Nrw83eKxjgHdwZC0ibxPTenklZNSBEMZMBDeq8H3bKoAfuyczX0IfVDA2Iyebsg2KYIIZQ/Otw7hAm2IH3perUqzeYliLhIYb0Gd3jyOpl4VMaPb2p+f5+fG87MnzjDplyorruhZyUcv8CMUc6XZ3dJjeiSsNNCRKLEb6Cm6msQxuCUq+Xz1n0+ay6fsaJbbhzFwNvbRH2YSzBg5BmtBXVt68U6XM3SzynWAqQaDS44Cuv3M1q88baTlOTjRkFRZ user1@ubuntu16-2
```

now lets check the result from the client:

```text
user1@ubuntu16-2:~$ ssh user1@192.168.52.146
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 15:57:29 2020 from 192.168.52.133
user1@ubuntu16-1:~$ 

```

and it seems okey.We can copy and paste the keys for other users if you like, but do not forget that these keys give power to users to login with out the password.

#### Why use passphrase? Why it is for?

We have configured a password less ssh connection using key based authentication. But what would happened if our system compromised? An evil hacker would be able to get connected to other servers using key based authentication without knowning the passwords.

Passphrase can help us to avoid this kinds of security issues by requiring a passphrase at the beginning of every ssh key-based authentication. So let clear previous authorized\_key, and start:

```text
user1@ubuntu16-1:~$ 
user1@ubuntu16-1:~$ vim .ssh/authorized_keys 
user1@ubuntu16-1:~$ cat .ssh/authorized_keys 
user1@ubuntu16-1:~$ exit
logout
Connection to 192.168.52.146 closed.
```

Now generate a new key pairs with passphrase on the client \(Let it over write current private and public key\):

```text
user1@ubuntu16-2:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user1/.ssh/id_rsa): 
/home/user1/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user1/.ssh/id_rsa.
Your public key has been saved in /home/user1/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:NYUt7TiSY2pBH7jtmZqGDdV545CRZxTFvOg3KJ3trxo user1@ubuntu16-2
The key's randomart image is:
+---[RSA 2048]----+
|       . ooOo    |
|      o + =.=    |
|     . = Oo= .   |
|      + %.*.o    |
|     . =S@ *     |
|    . o = * +    |
|     = o .Eo .   |
|    . =    ..    |
|     .    ...o.  |
+----[SHA256]-----+
user1@ubuntu16-2:~$ 
user1@ubuntu16-2:~$ tree .ssh/
.ssh/
├── id_rsa
├── id_rsa.pub
└── known_hosts

0 directories, 3 files
user1@ubuntu16-2:~$ cat .ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDjF4K2XDLQIysT9QvwQKFFvJ6LeBN3XsmEO9cTZfnRPhPjKOpcwvyCPaFJwXpiSXLE+RUjy2lwghZ6sOIXezGG9+oqkVegBOJ9RonQfvg5nUCr/Khx+dT/5ZV8JEjJVYqWnrgxKiKgzFHfzInE3qKG4kN2yuanomqIPFGQs0Mk/ShmYCPEEDIxyapRWkSJMa1OeS4/Elk1gGcna0TwgNVF8zmGkg5JO3ruwia2uSbdDWxA2vVtae2qA02lFh0Gb/LJO8vR24MAwlyMMHU2UdszA4eWqQBrZtpKmQ0A4plT8EUkh2cZHgaUeMVloWDmFRyiU2LIFgC5AacnwRIFTtTD user1@ubuntu16-2
user1@ubuntu16-2:~$ 
```

Now lets tranfer our new public key to the server:

```text
user1@ubuntu16-2:~$ ssh-copy-id -i .ssh/id_rsa.pub user1@192.168.52.146
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ".ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
user1@192.168.52.146's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'user1@192.168.52.146'"
and check to make sure that only the key(s) you wanted were added.
```

Lets check the key we have copied on the server:

```text
user1@ubuntu16-1:~$ cat .ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDjF4K2XDLQIysT9QvwQKFFvJ6LeBN3XsmEO9cTZfnRPhPjKOpcwvyCPaFJwXpiSXLE+RUjy2lwghZ6sOIXezGG9+oqkVegBOJ9RonQfvg5nUCr/Khx+dT/5ZV8JEjJVYqWnrgxKiKgzFHfzInE3qKG4kN2yuanomqIPFGQs0Mk/ShmYCPEEDIxyapRWkSJMa1OeS4/Elk1gGcna0TwgNVF8zmGkg5JO3ruwia2uSbdDWxA2vVtae2qA02lFh0Gb/LJO8vR24MAwlyMMHU2UdszA4eWqQBrZtpKmQ0A4plT8EUkh2cZHgaUeMVloWDmFRyiU2LIFgC5AacnwRIFTtTD user1@ubuntu16-2
```

Now when we ssh to the remote server \(ubuntu16-1\) from our client\(ubuntu16-2\), we are asked to enter our local key passphrase intead of remote user account password:

```text
user1@ubuntu16-2:~$ ssh user1@192.168.52.146
Enter passphrase for key '/home/user1/.ssh/id_rsa': 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 16:19:57 2020 from 192.168.52.133

```

lets exit and ssh again and again:

```text
user1@ubuntu16-1:~$ exit
logout
Connection to 192.168.52.146 closed.

user1@ubuntu16-2:~$ ssh user1@192.168.52.146
Enter passphrase for key '/home/user1/.ssh/id_rsa': 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 16:30:00 2020 from 192.168.52.133
user1@ubuntu16-1:~$ exit
logout
Connection to 192.168.52.146 closed.

```

as you can see each time we are asked to enter passphrase and that was what we were seeking for inorder to stop a hacker if our system get compromised. There is way to stick passphrase to the current user session and keept if for next ssh connections inorder to avoid entering passphrase again and again.

### ssh-agent

 The **ssh**-**agent** is a helper program that keeps track of user's identity keys and their passphrases. The **agent** can then use the keys to log into other servers without having the user type in a password or passphrase again. This implements a form of single sign-on \(SSO\). The SSH agent is used for SSH public key authentication.

```text
user1@ubuntu16-2:~$ ssh-agent /bin/bash 
user1@ubuntu16-2:~$ 
```

### ssh-add

By default, the agent uses SSH keys stored in the `.ssh` directory under the user's home directory. The **ssh-add** command is used for adding identities to the agent. In the simplest form, just run if without argument to add the default files `~/.ssh/id_rsa`, `.ssh/id_dsa`, `~/.ssh/id_ecdsa`, `~/.ssh/id_ed25519`, and `~/.ssh/identity`. Otherwise, give it the name of the private key file to add as an argument.

```text
user1@ubuntu16-2:~$ ssh-add 
Enter passphrase for /home/user1/.ssh/id_rsa: 
Identity added: /home/user1/.ssh/id_rsa (/home/user1/.ssh/id_rsa)

user1@ubuntu16-2:~$ ssh-add -l
2048 SHA256:NYUt7TiSY2pBH7jtmZqGDdV545CRZxTFvOg3KJ3trxo /home/user1/.ssh/id_rsa (RSA)
user1@ubuntu16-2:~$ 
```

`-l`  will list private keys currently accessible to the agent:

And then we can login to OpenSSH server \(ubuntu16-1\) without any password or passphrase again and again:

```text
user1@ubuntu16-2:~$ ssh user1@192.168.52.146
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 16:31:26 2020 from 192.168.52.133
user1@ubuntu16-1:~$ exit
logout

```

Until we exit from the bash that uses associated key with that, then we would need to enter passphrase again.



.

.

.

[https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)

[https://hackernoon.com/symmetric-and-asymmetric-encryption-5122f9ec65b1](https://hackernoon.com/symmetric-and-asymmetric-encryption-5122f9ec65b1)

[https://economictimes.indiatimes.com/definition/decryption](https://economictimes.indiatimes.com/definition/decryption)

[https://support.microsoft.com/en-us/help/246071/description-of-symmetric-and-asymmetric-encryption](https://support.microsoft.com/en-us/help/246071/description-of-symmetric-and-asymmetric-encryption)

[https://www.ssh.com/ssh/protocol](https://www.ssh.com/ssh/protocol)

[https://www.hivelocity.net/kb/what-is-openssh/](https://www.hivelocity.net/kb/what-is-openssh/)

.

