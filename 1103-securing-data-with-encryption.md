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
We use CentOS7 as ssh server and Ubuntu 16 as client.
{% endhint %}

#### /etc/ssh

OpenSSH has two different sets of configuration files: one for client programs \(ssh, scp, and sftp\) and one for the server daemon \(sshd\).

```text
[root@centos7-1 ~]# ls -1 /etc/ssh
moduli
ssh_config
sshd_config
ssh_host_ecdsa_key
ssh_host_ecdsa_key.pub
ssh_host_ed25519_key
ssh_host_ed25519_key.pub
ssh_host_rsa_key
ssh_host_rsa_key.pub
```

The`sshd_config`is the ssh  **daemon**\(or ssh server process\) configuration file, Whereas, the `ssh_config` file is the ssh client configuration file. The client configuration file only has bearing on when you use the ssh command to connect to another ssh host . As you can see there are public keys and private keys here with different algorithems and they can be used by SSH to encrypt the session.

## ssh client configurations

Till now we have understood how ssh works. As we mentioned when ssh connection is started, the public key of ssh server is tranfered to the client\(stored in ./ssh/known\_hosts\) and the client will use it to continue negotiation with the server and user will be required to get authenticated by sending username and password.

Lets start by connecting to Centos7-1 from ubuntu and see the keys:

```text
user1@ubuntu16-1:~$ ssh 192.168.52.137
The authenticity of host '192.168.52.137 (192.168.52.137)' can't be established.
ECDSA key fingerprint is SHA256:QtfM2iXh5pxZeFdAUXEBEnRXNSP40MWIhnSYvpOBMoY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.52.137' (ECDSA) to the list of known hosts.
user1@192.168.52.137's password: 
Last login: Sun Jan 12 21:33:13 2020
[user1@centos7-1 ~]$ 
[user1@centos7-1 ~]$ cat /etc/ssh/ssh_host_ecdsa_key.pub 
ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKuy5+nza9QN5cqDE2E7jJLEdqDrIkOprS8n/HP7Cj3V31kx4rOShL61zjuevHROlt4niShqS1wy584SGBMmHgg=
```

```text
user1@ubuntu16-1:~$ cat .ssh/known_hosts 
|1|dsPtZ+AslxquZV2cOGGCKL/lARQ=|GyaxhGWOxDR/EtMyLjkdbajtK10= ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKuy5+nza9QN5cqDE2E7jJLEdqDrIkOprS8n/HP7Cj3V31kx4rOShL61zjuevHROlt4niShqS1wy584SGBMmHgg=
```

### Configuring SSH Key Based authentication

Its possible to omit entring user name and password and get connected to the ssh server using client public and private key.

![](.gitbook/assets/ssh-keybasedauth.jpg)

Now lets generate public and private keys for client and copy client public key to the server.

### ssh-keygen

ssh-keygen - creates a key pair for public key authentication:

```text
user1@ubuntu16-1:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user1/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user1/.ssh/id_rsa.
Your public key has been saved in /home/user1/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:7fzHSMYAEMzQK3qP/yWksLyfLV6mf9nzmEBQsNTDmWU user1@ubuntu16-1
The key's randomart image is:
+---[RSA 2048]----+
|    .=ooo+.+E    |
|      +..o*.     |
|       .o. .     |
|    . .  o.      |
|   ...  S oo     |
|  ...o o +  +    |
|   .oo. + == o   |
|    ..o* ooo+oo  |
|    .+*++.  ++.  |
+----[SHA256]-----+
user1@ubuntu16-1:~$ 
user1@ubuntu16-1:~$ tree .ssh/
.ssh/
├── id_rsa
├── id_rsa.pub
└── known_hosts

0 directories, 3 files
user1@ubuntu16-1:~$ cat .ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC3CdIwtXbhX/Uxr5qC3RWc6V5EtczvgMWkD8oWMlnc994dVMJoSuyPeUrXG3j/iemhaHrrh5jvILEmZDiEy5njQxXOMFwW5qa6PyaaARbpOwc+Axc88uzEolrPbt9aLAbxNWLYjkgFNNdOmQCz2UNynF3Tx+mMEWWTdEPDlPMtkH6xDc5G6uMzSzhrt8ndLJoWXezh27UbU/Gq2wh6odXZvnQg5vlsKTFuOaWjLbjz1+fll40RcWTIkJnhLSUqjrv4eE+PKVhNOKWws9ncAKxHon6nd6Kc3oyQvZMjVbZ2N+htSwwtt5KtVPuRHhyMvD1wg7xgzokhJpc/+plno9wp user1@ubuntu16-1

```

We haven't set passphrase in our demonstration but if we set we would be asked to enter it when we copy it to the server. 

### ssh-copy-id

we use ssh-copy-id - configures a public key as authorized on a server 

```text
user1@ubuntu16-1:~$ ssh-copy-id -i .ssh/id_rsa.pub user1@192.168.52.137
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ".ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
user1@192.168.52.137's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'user1@192.168.52.137'"
and check to make sure that only the key(s) you wanted were added.
```

Now lets take a look the server side:

```text
[root@centos7-1 ~]# cat ~user1/.ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC3CdIwtXbhX/Uxr5qC3RWc6V5EtczvgMWkD8oWMlnc994dVMJoSuyPeUrXG3j/iemhaHrrh5jvILEmZDiEy5njQxXOMFwW5qa6PyaaARbpOwc+Axc88uzEolrPbt9aLAbxNWLYjkgFNNdOmQCz2UNynF3Tx+mMEWWTdEPDlPMtkH6xDc5G6uMzSzhrt8ndLJoWXezh27UbU/Gq2wh6odXZvnQg5vlsKTFuOaWjLbjz1+fll40RcWTIkJnhLSUqjrv4eE+PKVhNOKWws9ncAKxHon6nd6Kc3oyQvZMjVbZ2N+htSwwtt5KtVPuRHhyMvD1wg7xgzokhJpc/+plno9wp user1@ubuntu16-1
```

now lets check the result from the client:

```text
user1@ubuntu16-1:~$ ssh user1@192.168.52.137
Last login: Fri Mar 27 04:40:38 2020 from 192.168.52.146
[user1@centos7-1 ~]$ 
```

and it seems okey.We can copy and paste the keys for other users if you like, but do not forget that these keys give power to users to login with out the password.

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

