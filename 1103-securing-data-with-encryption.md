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

### Cryptography

 Cryptography is a method of using advanced mathematical principles in storing and transmitting data in a particular form so that only those whom it is intended can read and process it. Encryption is a key concept in cryptography

* **Encryption** :In cryptography, encryption is the process of encoding a message or information in such a way that only authorized parties can access it and those who are not authorized cannot.
* **Decryption**: The conversion of encrypted data into its original form is called Decryption. It is generally a reverse process of encryption.

### Symmetric vs. asymmetric encryption

Encryption algorithms are often divided into two categories, known as symmetric and asymmetric encryption.

#### Symmetric Encryption

Symmetric encryption is the oldest and best-known technique. A secret key, which can be a number, a word, or just a string of random letters, is applied to the text of a message to change the content in a particular way. This might be as simple as shifting each letter by a number of places in the alphabet. As long as both sender and recipient know the secret key, they can encrypt and decrypt all messages that use this key.  


#### Asymmetric Encryption

The problem with secret keys is exchanging them over the Internet or a large network while preventing them from falling into the wrong hands. Anyone who knows the secret key can decrypt the message. One answer is asymmetric encryption, in which there are two related keys--a key pair. A public key is made freely available to anyone who might want to send you a message. A second, private key is kept secret, so that only you know it.  
  
Any message \(text, binary files, or documents\) that are encrypted by using the public key can only be decrypted by applying the same algorithm, but by using the matching private key. Any message that is encrypted by using the private key can only be decrypted by using the matching public key.  
  
  
This means that you do not have to worry about passing public keys over the Internet \(the keys are supposed to be public\). A problem with asymmetric encryption, however, is that it is slower than symmetric encryption. It requires far more processing power to both encrypt and decrypt the content of the message.  




.

.

.

[https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)

[https://hackernoon.com/symmetric-and-asymmetric-encryption-5122f9ec65b1](https://hackernoon.com/symmetric-and-asymmetric-encryption-5122f9ec65b1)

[https://economictimes.indiatimes.com/definition/decryption](https://economictimes.indiatimes.com/definition/decryption)

.

