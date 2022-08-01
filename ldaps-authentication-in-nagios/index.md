---
title: "LDAPS Authentication in Nagios"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

These steps were performed running Nagios 4.3.1 on Fedora 25.

Install the LDAP module for Apache:

dnf install mod\_ldap

 

Edit /usr/local/nagios/etc/cgi.cfg to allow any user. You can enter a comma separated list of user names here instead of \* but this becomes quite cumbersome to manage. The \* says that anyone that passes the authentication requirements of the Apache auth modules will have full access like nagiosadmin.

authorized\_for\_system\_information=\*
authorized\_for\_configuration\_information=\*
authorized\_for\_system\_commands=\*
authorized\_for\_all\_services=\*
authorized\_for\_all\_hosts=\*
authorized\_for\_all\_service\_commands=\*
authorized\_for\_all\_host\_commands=\*

 

Edit /etc/httpd/conf.d/nagios.conf to authenticate against an LDAPS server. Below is an example using my config file. If you don't need secure communications replace LDAPS with LDAP. I've also allowed local file based authentication for the default nagiosadmin user in case the LDAP server goes down. It's listed as the first Require directive for this reason.

LoadModule ldap\_module modules/mod\_ldap.so
LoadModule authnz\_ldap\_module modules/mod\_authnz\_ldap.so
LDAPVerifyServerCert Off

ScriptAlias /nagios/cgi-bin "/usr/local/nagios/sbin"

<Directory "/usr/local/nagios/sbin">
#  SSLRequireSSL
   Options None
   AllowOverride None

   Options FollowSymLinks
   AuthBasicProvider file ldap
   AuthType Basic
   AuthUserFile "/usr/local/nagios/etc/htpasswd.users"
   AuthName "AD Login"
   AuthLDAPURL "ldaps://192.168.1.19/DC=db,DC=forest?sAMAccountName?sub"
   AuthLDAPBindDN "ldapuser@db.forest"
   AuthLDAPBindPassword "s0m3passw0rd"
   Require user nagiosadmin
   Require ldap-group CN=linux-admins,OU=LDAP Groups,DC=db,DC=forest
   Require ldap-group CN=nagios-users,OU=LDAP Groups,DC=db,DC=forest
</Directory>

Alias /nagios "/usr/local/nagios/share"

<Directory "/usr/local/nagios/share">
#  SSLRequireSSL
   Options None
   AllowOverride None

   Options FollowSymLinks
   AuthBasicProvider file ldap
   AuthType Basic
   AuthUserFile "/usr/local/nagios/etc/htpasswd.users"
   AuthName "AD Login"
   AuthLDAPURL "ldaps://192.168.1.19/DC=db,DC=forest?sAMAccountName?sub"
   AuthLDAPBindDN "ldapuser@db.forest"
   AuthLDAPBindPassword "s0m3passw0rd"
   Require user nagiosadmin
   Require ldap-group CN=linux-admins,OU=LDAP Groups,DC=db,DC=forest
   Require ldap-group CN=nagios-users,OU=LDAP Groups,DC=db,DC=forest
</Directory>

 

Restart Nagios and Apache:

systemctl restart nagios
systemctl restart httpd
