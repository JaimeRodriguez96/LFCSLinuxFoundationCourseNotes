# EMAIL SERVERS

Created: April 20, 2023 2:29 PM
Status: Not started

# **Learning Objectives**

By the end of this section, you should be able to:

- Illustrate how email flows from author to recipient.
- Configure a Postfix email server.
- Configure a Dovecot IMAP server.

# ****Email Overview****

Email programs and daemons have multiple roles and utilize various protocols.

**MUA (Mail User Agent)**
It is the main role of your email client. A MUA is where you read your email and compose emails to others. The MUA uses IMAP (Internet Message Access Protocol) or POP3 (Post Office Protocol) to download your email.

**MSP (Mail Submission Program)**
It is the role your email client has when you click 'Send'. The MSP submits your message to the MTA (Mail Transfer Agent) using SMTP (Simple Mail Transfer Protocol).

**MTA (Mail Transfer Agent)**
It is the main role of your email server. The MTA is responsible for starting the process of sending the message to the recipient. It does this by looking up the recipient and sending the message to their MTA using SMTP.

**MDA (Mail Delivery Agent)**
It is the role your email server takes when it receives an email for you. The MDA is responsible for storing the message for future retrieval. The MTA uses SMTP, LMTP (Local Mail Transfer Protocol), or another protocol to transfer the message to the MDA.

# ****SMTP, POP3 and IMAP Protocols****

**Simple Mail Transfer Protocol (SMTP)**

The Simple Mail Transfer Protocol (SMTP) is a TCP/IP protocol used as an Internet standard for electronic mail transmission.

It uses a plain "English" syntax such as **HELO**, **MAIL**, **RCPT**, **DATA**, or **QUIT**. SMTP is easily tested using **telnet**.

Sample mail exchange using telnet:

```bash
telnet localhost 25

Trying 127.0.0.1...
Connected to localhost.
Escape character is 'ˆ]'.
220 SERVER1 ESMTP Postfix (Ubuntu)

helo localhost
250 SERVER1

mail from:root@localhost
250 2.1.0 Ok

rcpt to:root@localhost
250 2.1.5 Ok

data
354 End data with <CR><LF>.<CR><LF>
This is neato
stuff ...

.
250 2.0.0 Ok: queued as 9C4DCEE3382

quit
221 2.0.0 Bye
```

**Post Office Protocol (POP3)**

The Post Office Protocol (POP) is one of the main protocols used by MUAs to fetch mail. By default, the protocol downloads the messages and deletes them from the server. It is simpler yet less flexible protocol.

**Internet Message Access Protocol (IMAP)**
The Internet Message Access Protocol (IMAP) is the other main protocol used by MUA to fetch mail. When using IMAP, the messages are managed on the server and left there. Copies are downloaded to the MUA. This protocol is more complex and more flexible than POP3.

# **Email Life Cycle**

The email life cycle is a fairly simple one.

1. You compose an email using your MUA.
2. Your MUA connects to your outbound MTA via SMTP, and sends the message to be delivered.
3. Your outbound MTA connects to the inbound MTA of the recipient via SMTP, and sends the message along. (Note: This step can happen more than once).
4. Once the message gets to the final destination MTA, it is delivered to the MDA. This can happen over SMTP, LMTP or other protocols.
5. The MDA stores the message (on disk as a file, or in a database, etc).
6. The recipient connects (via IMAP, POP3 or a similar protocol) to their email server, and fetches the message. The IMAP or POP daemon fetches the message out of the storage and sends it to the MUA.
7. The message is then read by the recipient.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/aunchapern97-Email-Lifecycle-eps-converted-to.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/aunchapern97-Email-Lifecycle-eps-converted-to.png)

**Email Life Cycle**

# ****MTA, MDA, MUA and IMAP/POP Implementations****

**MTA, MDA, MUA and IMAP/POP Implementations**

Mail Transfer Agent (MTA) Implementations
The Mail Transfer Agent (MTA) implementations use different software suites.

Sendmail was one of the first, if not the first SMTP implementation. Sendmail is difficult to configure with m4 macros. Older versions had security problems.

Exim is an alternative to Sendmail.

Postfix has an architecture characterized by separate binaries and division of privileges. It is a drop-in replacement for Sendmail.

# Mail Delivery Agents (MDA) Implementations

Many MTA software suites have components which act as Mail Delivery Agents (MDAs) as well. Some examples of these software suites include:

- Sendmail
- Postfix
- procmail
- Sieve
- Cyrus
- Spam Assassin.

When the MDA is activated to deliver a mail item to the local machine, there are some delivery options to be considered:

In postfix **`/etc/postfix/main.cf`** contains location information for the alias database. This has two common locations and names:

- **`/etc/aliases`** for system-wide redirection of mail
- **`~/.forward`** for user-configurable redirection of mail.

**`/etc/aliases**` has several options for its storage type; generally, data is stored in a **`.bdm`** or `**hash**` format as described by **`/etc/postfix/main.cf**.`

The database is built by running the following command: 

**`# postalias /etc/aliases`**

The format of the **`/etc/aliases`** and **`~/.forward`** files is: **`name: value1, value2, value3...`**

where **`name**` is the name the email was intended for initially (root, sysadmin). The **value** may be:

- a different email address
- a local file name, messages are appended
- a command, via the pipe
- **:include:/filename** to send to all the mail addresses in filename.

See **man 5 aliases** for more details.

**Mail User Agent (MUA) Implementations**

Mail User Agent (MUA) is used to manage a user's email. Some of the software suites used include:

- Thunderbird
- Mutt
- Evolution
- Outlook/Outlook Express.
- 

**IMAP/POP Implementations**

Mail programs use IMAP and POP protocols to access mail stored on remote computers. Some of the servers used for implementing IMAP and POP are the following:

- UW Reference implementation of the IMAP protocol.
- Cyrus IMAP Enterprise-ready IMAP. It's more complex and more difficult to configure.
- Dovecot Easy to use due to its powerful configuration options.
- Courier Designed to be "plug-and-play" installable and does not require any site-specific configuration.

# **Postfix Configuration**

Postfix uses the **`/etc/postfix`** directory for configuration files. The two most important configuration files are:

**`/etc/postfix/master.cf`**

**`/etc/postfix/main.cf`**

Within Postfix there is a process called master that controls all other Postfix processes that are involved in moving mail. The defaults in the **`/etc/postfix/master.cf**` file are usually sufficient.

The **`/etc/postfix/main.cf`** file contains the configuration parameters for mail processing. There are several hundred options that may be applied. In most cases, only a few mail parameters need to be altered in **`/etc/postfix/main.cf**.` The following are the usual candidates for customization:

- The domain name to use for outbound mail (**`myorigin`**).
- The domains to receive mail for (**`mydestination`**).
- The clients to allow relaying of mail (**`mynetworks`**).
- The destinations to relay mail to (**`relay_domains`**).
- The delivery method, indirect or direct (**`relayhost`**).

The **`postconf**` command can be used to customize **`/etc/postfix/main.conf**:`

**`# postconf -e 'inet_interfaces = all'`**

# ****Common Postfix Configuration****

**mynetworks**
SMTP servers which are trusted to blindly forward email.

**mynetworks_style**

Allows for dynamic creation of the **mynetworks** option.

- **mynetworks_style = class** Trust all hosts in the same A/B/C network class (not a good idea).
- **mynetworks_style = subnet** Trust all hosts in the same subnet as any interface on your host.
- **mynetworks_style = host** Trust only the local machine.

**inet_interfaces**
IP addresses to listen on for incoming connections.

**myorigin**
Domain that outbound email should appear to come from.

**mydestination**
Domains that are the final destination for messages.

**relayhost**
SMTP server to forward outbound email.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/vutiaeztxuxr-postfix.jpg](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/vutiaeztxuxr-postfix.jpg)

**Common Postfix Configuration**

# **Security Considerations**

Since SMTP is a clear-text protocol, security is a concern. Network authentication relies by default on a trustworthy subnet.

The default Postfix configuration is only marginally secure:

- There is no SSL/TLS encryption.
- The **mynetworks_style** directive allows for all hosts in the subnet to forward. Luckily, the default setting of the **inet_interfaces** directive allows only localhosts to connect.
- No spam filtering is enabled by default.

# **Postfix Authentication**

Postfix does not provide a Simple Authentication and Security Layer (SASL) mechanism itself, but requires a properly set up SASL implementation. The Postfix daemon can support the Cyrus SASL or the Dovecot SASL mechanisms.

To configure the SASL mechanism for Postfix using the Dovecot SASL system, you would do the following:

1. Configure Dovecot and the Dovecot SASL system.
2. Configure Postfix to use the Dovecot SASL system for authentication.
3. Configure Postfix to trust users who have properly authenticated.
4. Configure Postfix to enable trusted users to change their outbound envelope (From) address.

The Postfix options you will need to change in order to enable Dovecot SASL are the following:

**`smtpd_sasl_type = dovecot`**

**`smtpd_sasl_path = private/auth`**

The Postfix options which enable the SMTP SASL authentication are:

**`smtpd_sasl_auth_enable = yes`**

**`broken_sasl_auth_clients = yes`**

# **Postfix SASL**

Postfix SASL authentication with Dovecot can be enabled over a UNIX Domain Socket and over TCP.

To enable UNIX accounts to authenticate over a UNIX Domain Socket, edit the **`/etc/dovecot/conf.d/10-master.conf`** file.

```bash
service auth {
   unix_listener auth-userdb { }
   unix_listener /var/spool/postfix/private/auth { }
}
```

When testing plain-text SASL authentication, you will need to submit a **`base64**` encoded username and password. This string can be generated using the **`echo**` and **`base64**` commands:

**`$ echo -en "\0user\0password" | base64`**

The output from this command shows:

**`AHVzZXIAcGFzc3dvcmQ=`**

Copy that string and use this syntax to test the SMTP authentication:

**`AUTH PLAIN AHVzZXIAcGFzc3dvcmQ=`**

# **Postfix Security**

Postfix security requires SSL or StartTLS.

To enable optional TLS encryption, use the following options:

**`smtpd_tls_cert_file = /etc/postfix/server.pem`**

**`smtpd_tls_key_file = $smtpd_tls_cert_file`**

**`smtpd_tls_security_level = may`**

To enable authentication only after a TLS session has been established, use the following option:

**`smtpd_tls_auth_only = yes`**

# ****Monitoring Postfix****

**pflogsumm**
A Perl script which translates Postfix log files into a human-readable summary.

`qshape`

Prints the domains and ages of items in the Postfix queue.

The **`qshape**` command is useful for determining where emails are getting stuck in queues. The output of the command displays the distribution of the items in the Postfix queues by recipient domain. A command line option **-s** will list by sender domain:

**`$ qshape | head`**

The output from this command shows:

```bash
T 5 10 20 40 80 160 320 640 1280 1280+
      TOTAL 289 0  3  1  0  2   2   7  20   43   211
  gmail.com  12 0  0  0  0  0   0   0   0    0    12
hotmail.com   7 0  1  0  0  0   0   0   2    0     4
```

The columns in the output are:

- Recipient domain
- Total (T) items in the queue
- A time distribution in minutes of items in the queue. Time buckets are ranges: 0 to 5, 6 to 10, each bucket being twice as large as the prior bucket.

**`qshape**` is bundled with Postfix version 2.1 or later, but may be contained in a separate package, like the Fedora package **`postfix-perl-scripts`**. Consult your distribution for details.

You can also read the following document: *[Postfix Bottleneck Analysis](http://www.postfix.org/QSHAPE_README.html)*.

mailgraph

A package which creates RRD graphics of mail logs.

The [mailgraph](https://mailgraph.schweikert.ch/) tool lets you monitor trends in your email server.

Picture below illustrates monitoring trends with mailgraph.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/tqdtnzu844vy-Mailgraph-Example.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/tqdtnzu844vy-Mailgraph-Example.png)

**Monitoring Trends with mailgraph**

# **Reducing SPAM**

The open source tool SpamAssassin can be used as a [milter](https://en.wikipedia.org/wiki/Milter) (Mail filter: tied into the MTA), or a standalone MDA. SpamAssassin uses tests and other criteria to determine if a message is spam. This makes it more difficult for a spammer to circumvent the filter.

Make sure your MTA is not an open relay and is configured to only send outbound email for the proper hosts or authenticated users. This can be done by blocking or rejecting badly formed email, as well as by not accepting email from known spam hosts (blacklists).

Other tools that have been used to counteract spam are the Sender Policy Framework (SPF) and DomainKeys.

# **Email Aliases and Forwarding**

Another common configuration change made on email servers are aliases and forwarding.

Email address aliases are managed by the **`/etc/aliases`** file and the changes are applied by using the **`newaliases**` command.

Email can also be sent to a different MTA using forwarding maps or rules.

# **Advanced Email Software**

In addition to having just MTA software, there are full-suite messaging systems. These full-suite systems provide webmail, mobile mail, and function as MTAs:

- [Horde](https://trainingportal.linuxfoundation.org/learn/course/linux-networking-and-administration-lfs211/email-servers/ADDWEBSITEADDRESS)
- [Zimbra](https://trainingportal.linuxfoundation.org/learn/course/linux-networking-and-administration-lfs211/email-servers/ADDWEBSITEADDRESS)
- [Open-Xchange](https://trainingportal.linuxfoundation.org/learn/course/linux-networking-and-administration-lfs211/email-servers/ADDWEBSITEADDRESS).

# ****Dovecot****

What Is Dovecot?

Dovecot is an open-source IMAP/POP3 server. Dovecot is secure, easy to configure and is standards compliant.

The author of Dovecot has a 1000 EUR bounty for any [remote bug found](https://www.dovecot.org/security).

Dovecot Configuration

The **`doveconf**` utility parses the configuration file for both the Dovecot daemons and for debugging purposes. It cannot edit running configuration files, but you can check the syntax of your configuration files with the following command:

**`sudo doveconf -n`**

Examples of configuration files include:

**`/etc/dovecot/dovecot.conf`**

**`/etc/dovecot/conf.d/`**

Common Dovecot Setup

The common Dovecot setup binds to a specific IP address, defines the protocols to serve, applies password restrictions, accepts some clients with minor protocol issues, and points to user and password databases:

- **`listen = 10.20.34.111`** IP address to bind.
- **`protocols = imap pop3 lmtp`**Protocols to serve.
- **`disable_plaintext_auth = yes`**Default setting which disallows plaintext passwords.
- **`imap_client_workarounds pop3_client_workarounds`**Change these settings to support broken client implementations of the IMAP or POP3 protocols.
- [Password databases](https://doc.dovecot.org/configuration_manual/authentication/password_databases_passdb/)
- [User databases](https://doc.dovecot.org/configuration_manual/authentication/user_databases_userdb/)

Dovecot Security

Dovecot security includes an SASL authentication. This can act as an SASL provider for other systems.

The SSL/TLS configuration includes:

- **`conf.d/10-ssl.conf`**
- Share certificate between daemons
- Individual key pairs for each protocol or domain

Important SSL settings:

```bash
**ssl = yes**

**# suggested permissions root:root 0444ssl_cert = < cert-file >**

**# suggested permissions root:root 0400ssl_key = < private-key-file >**
```

For parameter options and additional configuration elements, please consult the [Dovecot SSL Configuration webpage](https://wiki.dovecot.org/SSL/DovecotConfiguration).

Troubleshooting Dovecot

By default, Dovecot logs using the syslog facility mail, the configuration of your syslog server determines the location.

IMAP and POP3 are plain-text protocols. Therefore, using telnet or the openssl client for troubleshooting is possible. To do so, run the following command:

**`$ telnet localhost imap`**

The output from this command shows

```bash
a1 LOGIN student student
a2 LIST "" "*"
a3 EXAMINE INBOX
a4 FETCH 1 BODY[]
a5 LOGOUT
```

A working email client is the easiest way to troubleshoot and test an IMAP or POP3 server. The **mutt** email client works well.

**`mutt -f imaps://user@mail.example.com:993/`**

Below you can see screenshot of mutt:

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/loomrirsu9xp-Mutt-Screen.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/loomrirsu9xp-Mutt-Screen.png)

**Troubleshooting Dovecot**