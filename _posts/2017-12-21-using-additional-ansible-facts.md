---
layout: post
title: "Using Additional Ansible Facts"
date: 2017-12-21 17:09:06 -0700
comments: false
---
This article is an addition to Additional Ansible Windows Facts.

To view the facts after running the role enter in the following in a terminal.

```
ansible <HOST or GROUP>  -m debug -a "var=ansible_getFacts"
```

To search for host for a signal program:

```
ansible <HOST or GROUP> -m debug -a "var=hostvars[inventory_hostname]['ansible_getFacts']['<PROGRAM NAME>']"
```

To check if a program is installed
1. Set the register variable 
```
- name: get variables
  debug: var=hostvars[inventory_hostname]['ansible_getFacts']['<PROGRAM NAME>']
    register: <PROGRAM NAME>
```
2. Next use the register with the ansible 'when' command. Below is an exaple that will run in cases of the program missing. 
```
  when: hostvars[inventory_hostname]['ansible_getFacts']['<PROGRAM NAME>'] is undefined
```

Complete Example 
```
- name: get variables
  debug: var=hostvars[inventory_hostname]['ansible_getFacts']['Microsoft Office Professional Plus 2016']
  register: msoffice_check

- name: create  ms office dir 
  win_file:
    path: C:\msoffice
    state: directory 
  when: hostvars[inventory_hostname]['ansible_getFacts']['Microsoft Office Professional Plus 2016'] is undefined
 
- name: copy ms office dir to c drive
  win_copy:
    src:  /misc/software/msoffice2016/
    dest: C:\msoffice
  ignore_errors: yes
  when: hostvars[inventory_hostname]['ansible_getFacts']['Microsoft Office Professional Plus 2016'] is undefined

- name:  Install msoffice
  win_command: choco install msoffice.20.16.1.nupkg -y
  args: 
    chdir: C:\msoffice
  ignore_errors: yes
  when: hostvars[inventory_hostname]['ansible_getFacts']['Microsoft Office Professional Plus 2016'] is undefined

- name: remove MS office dir
  win_file:
    path: C:\msoffice
    state: absent
  when: hostvars[inventory_hostname]['ansible_getFacts']['Microsoft Office Professional Plus 2016'] is defined
```
