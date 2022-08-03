---
title: "Configure postfix to relay mail to another SMTP server with authentication"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

Install the library to authenticate with plain text

yum -y install cyrus-sasl-lib cyrus-sasl-plain

 

Edit /etc/postfix/main.cf to enable the mail relay

relayhost=\[smtp.server.fqdn\]:port#
smtp\_generic\_maps=hash:/etc/postfix/generic
smtp\_sasl\_auth\_enable=yes
smtp\_sasl\_password\_maps=hash:/etc/postfix/sasl-passwords
smtp\_sasl\_security\_options=

 

Store the username and password used to authenticate to the SMTP server

/etc/postfix/sasl-passwords
smtp.server.fqdn username:password

 

Optional: Configure address remapping by editing /etc/postfix/generic

localuser@hostname.localdomain newuser@newdomain.com

 

Compile the password file and optionally the user map, then tell postfix to reload the config

postmap /etc/postfix/sasl-passwords
postmap /etc/postfix/generic
postfix reload

 

You can monitor postfix activity by watching /var/log/maillog

tail -f /var/log/maillog

 

To view the outgoing queue

mailq

 

To force the sending of deferred emails or the resending of failed emails

postfix flush

 

To send a message from the command line

mail -s "Message Subject" -r from@domain.com to@domain.com
Message body
goes here
^D
