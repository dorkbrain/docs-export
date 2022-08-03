---
title: "Nagios 4 Install on CentOS"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

yum -y install curl httpd php php-cli gcc glibc glibc-common gd gd-devel net-snmp openssl-devel wget unzip net-snmp net-snmp-utils

mkdir -p /usr/local/src/nagios
cd /usr/local/src/nagios
wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.3.2.tar.gz
wget https://nagios-plugins.org/download/nagios-plugins-2.2.1.tar.gz
for f in \*.tar.gz; do tar zxvf $f; done

useradd nagios
groupadd nagcmd
usermod -a -G nagcmd nagios
usermod -a -G nagcmd apache

cd /usr/local/src/nagios/nagios-4\*
./configure --with-command-group=nagcmd &&
make all &&
make install &&
make install-commandmode &&
make install-init &&
make install-config &&
make install-webconf

htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin

cd /usr/local/src/nagios/nagios-plugins-2\*
./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl &&
make all &&
make install

systemctl enable httpd
systemctl enable nagios
systemctl start httpd
systemctl start nagios
