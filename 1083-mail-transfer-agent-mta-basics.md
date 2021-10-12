# 108.3. Mail Transfer Agent (MTA) basics

**Weight:** 3

**Description: **Candidates should be aware of the commonly available MTA programs and be able to perform basic forward and alias configuration on a client host. Other configuration files are not covered.

**Key Knowledge Areas:**

* Create e-mail aliases
* Configure e-mail forwarding
* Knowledge of commonly available MTA programs (postfix, sendmail, qmail, exim) (no configuration)

**Terms and Utilities:**

* \~/.forward
* sendmail emulation layer commands
* newaliases
* mail
* mailq
* postfix
* sendmail
* exim
* qmail

### MTAs

Mail transfer agents deliver mail between users and between systems. Most Internet mail uses the Simple Mail Transfer Protocol (SMTP), but local mail may be transferred through other possibilities. There are different kinds of MTAs available and each distribution might have one of them as default. As an administrator we can chose the right MTA based on our needs.

### sendmail

Sendmail is the oldest Linux MTA. It was originally derived from the delivermail program that was used on the ARPANET in 1979. sendmail is big  and it is not easy to configure. Over the years many vulnerabilities have been found  in sendmail although  it's a bit enhanced now.

#### Other mail transfer agents

In response to security issues with sendmail, several other mail transfer agents were developed during the 1990’s. Postfix is perhaps the most popular, but qmail and exim are also widely used.

### qmail

Qmail is the one of the most secure Linux mail server software solutions on the market today. Although unsupported, and not currently in development (Qmail hasn't been updated since 1997), Qmail has a large fan base. 

Qmail is also faster, and scales better with higher mail loads than Sendmail. However, Qmail is not easy to configure, or easy to extend.

> qmail is not a GPL software it is public domain.

### exim

Exim has been out since 1995, and growing in popularity ever since. The biggest strength of Exim is it's almost infinite level of customization. Exim supports the ability for a server administrator to create a custom ruleset that handles incoming and outgoing emails in any particular manner.(authentication, access control list)

Although Exim was not designed for performance, Exim can be configured to run as a high performance mail server. Exim is an excellent MTA if you need to create a complex or custom mail configuration. 

### postfix

Postfix is possibly the fastest growing MTA on the market today. Postfix is extremely popular because of it's performance, and it's past security history. It is far harder (or almost impossible) to compromise the root user on a server that runs Postfix, than for instance Sendmail or Exim. 

Postfix also runs faster with less system resources than most other MTAs (or at least, with standard configurations).Standard configurations are easy to create, but if you need a unique setup, it can be a pain with Postfix. 

### sendmail emulation layer

 As sendmail has been around so long , no matter which email server we use, it comes with send mail emulation layer. In other words, all of other MTAs include tools which are backward compatible.  so if we type `sendmail `or `mailq `commands they  respond and act as if sendmail is installed.

### Mail aliases <a href="mail-aliases" id="mail-aliases"></a>

Sometimes you might want all the mail for a user to go to some other place. For example, you may have a server farm and want all the root mail to go to a central system administrator. Or you may want to create a mailing list where mail goes to several people. To do this, we use aliases that allow us to define one or more destinations for a given user name. 

mail aliases are located in `/etc/aliases . `You must first become root before modifying this file: (CentOS7, output has been truncate)

```
[root@centos7-1 ~]# cat /etc/aliases
#
#  Aliases in this file will NOT be expanded in the header from
#  Mail, but WILL be visible over networks or from /bin/mail.
#
#	>>>>>>>>>>	The program "newaliases" must be run after
#	>> NOTE >>	this file is updated for any changes to
#	>>>>>>>>>>	show through to sendmail.
#

# Basic system aliases -- these MUST be present.
mailer-daemon:	postmaster
postmaster:	root

# General redirections for pseudo accounts.
bin:		root
daemon:		root
adm:		root
lp:		root
sync:		root
shutdown:	root
halt:		root
mail:		root
ftp:		root
nobody:		root
amandabackup:		root
xfs:		root
gdm:		root


lpic1:  root     ###<---------


# trap decode to catch security attacks
decode:		root

# Person who should get root's mail
#root:		marc
root:    payam   ###<-----------
```

So it if there is a message for `"lpic1"` it will be sent to the `root `user. 

The last line tell that `payam` is reading `root` emails  . This let `payam `to read `root `emails without login with root.

### newaliases

 Modifications to the `/etc/aliases` file are not complete until the `newaliases` command is run to build `/etc/aliases.db`.

```
[root@centos7-1 ~]# newaliases
[root@centos7-1 ~]# 
```

### mail command

There are plenty of ways for sending email while using GUI , using browser or with an email client. But options are limited when it comes to command line interface.

   The `mail` command is an old standby that can be used to script the sending of mail as well as receive and manage your incoming mail. (CentOS: install mailx)

| mail command example                                | usage                     |
| --------------------------------------------------- | ------------------------- |
| mail -s “subject” user1@domain.com                  | sending an email          |
| mail -s “subject” user1@domain.com < /root/test.txt | send an email from a file |
| mail -s “subject” user1@domain.com -a /path/to/file | add an attachment         |

> It also can send email from within the scripts like 'echo -e "email content" | mail -s "email subject" "example@example.com"'

We can use `mail` interactively to send messages by passing a list of addressees, or with no arguments you can use it to look at your incoming mail.

```
[user1@centos7-1 ~]$ mail lpic1@localhost
Subject: test lpic1
Hi this is my first email.
I am sending it to lpic1 email address.
EOT
```

Because of aliases we have modified, Any email which is sent to lpic1 email address would be forwarded to root,  as we have define payam as a person who should get root's emails :

```
[payam@centos7-1 ~]$ mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/payam": 1 message 1 new
>N  1 user1@centos7-1.loca  Wed Feb 19 05:10  19/683   "test lpic1"
& 1
Message  1:
From user1@centos7-1.localdomain  Wed Feb 19 05:10:35 2020
Return-Path: <user1@centos7-1.localdomain>
X-Original-To: lpic1@localhost
Delivered-To: lpic1@localhost.localdomain
Date: Wed, 19 Feb 2020 05:10:35 -0500
To: lpic1@localhost.localdomain
Subject: test lpic1
User-Agent: Heirloom mailx 12.5 7/5/10
Content-Type: text/plain; charset=us-ascii
From: user1@centos7-1.localdomain
Status: R

Hi this is my first email.
I am sending it to lpic1 email address.

& d
& q
```

> Whenever a mail is sent, initially the mail command calls the mail binary, which in turns connects to the local MTA to send the mail to its destination. The local MTA is a locally running smtp server that accepts mails on port 25.

### \~/.forward

The aliases file must be managed by a system administrator. Individual users can enable forwarding of their own mail using a .forward file in their own home directory. You can put anything in your .forward file that is allowed on the right side of the aliases file. The file contains plain text and does not need to be compiled. When mail is destined for you, sendmail checks for a .forward file in your home directory and processes the entries the same way it processes aliases.

```
[user1@centos7-1 ~]$ pwd
/home/user1
[user1@centos7-1 ~]$ ls -la | grep -i forward
[user1@centos7-1 ~]$ vi .forward
[user1@centos7-1 ~]$ cat .forward 
user2
```

now lets end an email to user1 and check its mail box:

```
[payam@centos7-1 ~]$ mail user1@localhost
Subject: mail to user1
Hello!!!
EOT
```

```
[user1@centos7-1 ~]$ mail
No mail for user1
```

```
[user2@centos7-1 ~]$ mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/user2": 1 message 1 new
>N  1 payam@centos7-1.loca  Wed Feb 19 06:33  21/771   "mail to user1"
& 1
Message  1:
From payam@centos7-1.localdomain  Wed Feb 19 06:33:28 2020
Return-Path: <payam@centos7-1.localdomain>
X-Original-To: user1@localhost
Delivered-To: user2@centos7-1.localdomain
Delivered-To: user1@localhost.localdomain
Date: Wed, 19 Feb 2020 06:33:27 -0500
To: user1@localhost.localdomain
Subject: mail to user1
User-Agent: Heirloom mailx 12.5 7/5/10
Content-Type: text/plain; charset=us-ascii
From: payam@centos7-1.localdomain
Status: R

Hello!!!

& d
& q
[user2@centos7-1 ~]$ 
```

### mailq

 Linux mail handling uses a store-and-forward model. You have already seen that your incoming mail is stored in a file in /var/mail until you read it. Outgoing mail is also stored until a receiving server connection is available. You use the `mailq` command to see what mail is queued.

```
[root@centos7-1 ~]# mailq
Mail queue is empty
[root@centos7-1 ~]# mailq
-Queue ID- --Size-- ----Arrival Time---- -Sender/Recipient-------
E085461E470C      473 Wed Feb 19 06:39:07  payam@centos7-1.localdomain
(Host or domain name not found. Name service error for name=gmail.com type=MX: Host not found, try again)
                                         test@gmail.com

-- 0 Kbytes in 1 Request.
```

Obviously in a health system mailq should be always empty.

.

.

.

[https://en.wikipedia.org/wiki/Message_transfer_agent](https://en.wikipedia.org/wiki/Message_transfer_agent)

[https://developer.ibm.com/tutorials/l-lpic1-108-3/](https://developer.ibm.com/tutorials/l-lpic1-108-3/)

[https://jadi.gitbooks.io/lpic1/content/1083\_mail_transfer_agent_mta_basics.html](https://jadi.gitbooks.io/lpic1/content/1083\_mail_transfer_agent_mta_basics.html)

[http://linuxconsultant.info/tutorials/linux-mail-server-software.html](http://linuxconsultant.info/tutorials/linux-mail-server-software.html)

[https://hoststud.com/resources/comparison-between-mtas-postfix-vs-exim-vs-sendmail.158/](https://hoststud.com/resources/comparison-between-mtas-postfix-vs-exim-vs-sendmail.158/)

with the special thanks of shawn powers.
