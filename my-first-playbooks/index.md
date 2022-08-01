---
title: "My First Playbooks"
date: "2017-09-02"
categories: 
  - "ansible"
tags: 
  - "ansible"
---

This YML playbook tweaks system services how I normally have them in my VM lab:

\---
- hosts: basebuild
  remote\_user: root

  tasks:
  - name: Disable firewalld
    systemd:
      name: firewalld
      state: stopped
      enabled: no

  - name: Mask firewalld
    systemd:
      name: firewalld
      masked: yes

  - name: Disable selinux
    selinux:
      state: disabled

  - name: Disable IPv6 part 1
    sysctl:
      name: net.ipv6.conf.all.disable\_ipv6
      ignoreerrors: yes
      value: 1
      state: present
      sysctl\_set: yes
      reload: yes

  - name: Disable IPv6 part 2
    sysctl:
      name: net.ipv6.conf.default.disable\_ipv6
      ignoreerrors: yes
      value: 1
      state: present
      sysctl\_set: yes
      reload: yes

 

And this one installs/updates the additional software packages I install on all of my VM's

\---
- hosts: basebuild
  remote\_user: root

  tasks:
  - name: Install nano
    yum:
      name: nano
      state: latest

  - name: Install epel
    yum:
      name: epel-release
      state: latest

  - name: Install wget
    yum:
      name: wget
      state: latest

  - name: Install git
    yum:
      name: git
      state: latest

  - name: Install open-vm-tools
    yum:
      name: open-vm-tools
      state: latest

  - name: Start vmtoolsd
    systemd:
      name: vmtoolsd
      state: started
      enabled: yes
      masked: no
