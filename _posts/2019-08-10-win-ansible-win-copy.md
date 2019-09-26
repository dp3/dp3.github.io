---
layout: post
title: "Ansible Windows win_copy is slow"
date: 2019-08-10 09:09:06 -0700
comments: false
---

I have found a few problems with using the Ansible module win_copy. The module is slow and unreliable thus causing multiple timeouts. I have found a more reliable and quicker solution by using using win_command with robocopy.

 
Here is an example on how I used win_copy in the past.  
```
- name: copy ms office dir to c drive
  win_copy:
    src:  /misc/filessoftware/chocolatey/msoffice2016/
    dest: C:\msoffice
  ignore_errors: yes
  when: hostvars[inventory_hostname]['ansible_getFacts']['Microsoft Office Professional Plus 2016'] is undefined
```

The new solution along with mounting a network share. 
```
- name: Transfer of msoffice2016
  win_command: '{{ item }}'
  with_items:
    -  'net use  s: \\nfs.fqdn.com\software /P:yes /user:{{ ansible_user_ad }}  {{ ansible_password_ad }}'
    -  'robocopy s:\msoffice2016 \msoffice2016 /E'
  ignore_errors: yes
  no_log: True 
  when: hostvars[inventory_hostname]['ansible_getFacts']['Microsoft Office Professional Plus 2016'] is undefined
```
