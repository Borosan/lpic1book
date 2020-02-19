# 108.3. Mail Transfer Agent \(MTA\) basics

**Weight:** 3

**Description:** Candidates should be aware of the commonly available MTA programs and be able to perform basic forward and alias configuration on a client host. Other configuration files are not covered.

**Key Knowledge Areas:**

* Create e-mail aliases
* Configure e-mail forwarding
* Knowledge of commonly available MTA programs \(postfix, sendmail, qmail, exim\) \(no configuration\)

**Terms and Utilities:**

* ~/.forward
* sendmail emulation layer commands
* newaliases
* mail
* mailq
* postfix
* sendmail
* exim
* qmail

### MTAs

Mail transfer agents deliver mail between users and between systems. Most Internet mail uses the Simple Mail Transfer Protocol \(SMTP\), but local mail may be transferred through other possibilities. There are different kinds of MTAs available and each distribution might have one of them as default. As an administrator we can chose the right MTA based on our needs.

### sendmail

Sendmail is the oldest Linux MTA. It was originally derived from the delivermail program that was used on the ARPANET in 1979. sendmail is big  and it is not easy to configure. Over the years many vulnerabilities have been found  in sendmail although  it's a bit enhanced now.

#### Other mail transfer agents

In response to security issues with sendmail, several other mail transfer agents were developed during the 1990’s. Postfix is perhaps the most popular, but qmail and exim are also widely used.

### qmail

Qmail is the one of the most secure Linux mail server software solutions on the market today. Although unsupported, and not currently in development \(Qmail hasn't been updated since 1997\), Qmail has a large fan base. 

Qmail is also faster, and scales better with higher mail loads than Sendmail. However, Qmail is not easy to configure, or easy to extend.

> qmail is not a GPL software it is publicdomain.

### exim

Exim has been out since 1995, and growing in popularity ever since. The biggest strength of Exim is it's almost infinite level of customization. Exim supports the ability for a server administrator to create a custom ruleset that handles incoming and outgoing emails in any particular manner.\(authentication, access control list\)

Although Exim was not designed for performance, Exim can be configured to run as a high performance mail server. Exim is an excellent MTA if you need to create a complex or custom mail configuration. 

### postfix

Postfix is possibly the fastest growing MTA on the market today. Postfix is extremely popular because of it's performance, and it's past security history. It is far harder \(or almost impossible\) to compromise the root user on a server that runs Postfix, than for instance Sendmail or Exim. 

Postfix also runs faster with less system resources than most other MTAs \(or at least, with standard configurations\).Standard configurations are easy to create, but if you need a unique setup, it can be a pain with Postfix. 

### sendmail emulation layer

 As sendmail has been around so long , no matter which email server we use, it comes with send mail emulation layer. In other words, all of other MTAs include tools which are backward compatible.  so if we type `sendmail` or `mailq` commands they  respond and act as if sendmail is installed.









.

.

.

[https://en.wikipedia.org/wiki/Message\_transfer\_agent](https://en.wikipedia.org/wiki/Message_transfer_agent)

[https://developer.ibm.com/tutorials/l-lpic1-108-3/](https://developer.ibm.com/tutorials/l-lpic1-108-3/)

[https://jadi.gitbooks.io/lpic1/content/1083\_mail\_transfer\_agent\_mta\_basics.html](https://jadi.gitbooks.io/lpic1/content/1083_mail_transfer_agent_mta_basics.html)

[http://linuxconsultant.info/tutorials/linux-mail-server-software.html](http://linuxconsultant.info/tutorials/linux-mail-server-software.html)

[https://hoststud.com/resources/comparison-between-mtas-postfix-vs-exim-vs-sendmail.158/](https://hoststud.com/resources/comparison-between-mtas-postfix-vs-exim-vs-sendmail.158/)

with the special thanks of shawn powers.

