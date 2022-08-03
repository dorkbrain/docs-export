---
title: "Configure 2-factor authentication using Google Authenticator in CentOS"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

[https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2)

#!/bin/bash
yum -y groupinstall "Development Tools"
yum -y install pam-devel ntp bzip2 wget
systemctl start ntpd
systemctl enable ntpd
cd /opt
wget https://google-authenticator.googlecode.com/files/libpam-google-authenticator-1.0-source.tar.bz2
bunzip2 libpam-google-authenticator-1.0-source.tar.bz2
tar -xvf libpam-google-authenticator-1.0-source.tar
rm -f libpam-google-authenticator-1.0-source.tar
cd libpam-google-authenticator-1.0
make
make install
cd ~
google-authenticator -tdf --rate-limit=3 --rate-time=30 --window-size=17
sed -i '/#%PAM/a auth\\ \\ \\ \\ \\ \\ \\ required\\ \\ \\ \\ \\ pam\_google\_authenticator.so' /etc/pam.d/sshd
sed -i 's/#ChallengeResponseAuthentication\\ yes/ChallengeResponseAuthentication\\ yes/g' /etc/ssh/sshd\_config
sed -i 's/ChallengeResponseAuthentication\\ no/#ChallengeResponseAuthentication\\ no/g' /etc/ssh/sshd\_config
systemctl reload sshd
echo "Please scan QR code into google authenticator and copy safe codes, then test by opening a new ssh connection"
