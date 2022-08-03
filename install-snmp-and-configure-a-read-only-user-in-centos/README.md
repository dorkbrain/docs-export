---
title: "Install SNMP and Configure a Read-Only User in CentOS"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

1. Install SNMP and helper utilities.
2. Create a read-only SNMP v3 account.
3. Enable/start SNMPD service and add firewall rule.
    
    #step 1
    yum install net-snmp net-snmp-utils
    
    #step 2
    net-snmp-create-v3-user -ro -A authpass -X encpass -a MD5 -x DES joeuser
    
    #step 3
    systemctl enable snmpd.service
    systemctl start snmpd.service
    firewall-cmd --zone=public --add-port 161/udp --permanent
    firewall-cmd --reload
