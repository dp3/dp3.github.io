---
layout: post
title: "Additional Ansible Windows Facts"
date: 2020-11-14 11:37:06 -0700
comments: false
---
Ansible is a great tool to manage anyhost Linux or Windows. However it is missing some key tools and features. Here is a playbook and instructions to gather all installed programs on a windows host. 

Start with creating a new role, I name mine facts. 

Create the ```facts\tasks\main.yml``` file and add the following

```
---
- name: create Scripts dir on remote host
  win_file:
    path: C:\Scripts
    state: directory 

- name: copy custom facts file
  win_copy:
    src: getFacts.ps1
    dest: C:\Scripts\

- name: gather extra facts
  setup:
    fact_path: C:\Scripts

- name: Set intermediate fact
  set_fact:
    vars_hack: "{{ hostvars[inventory_hostname] }}"

- name: set program facts
  set_fact:
    one_fact: ansible_getFacts
    var_hack:  "{{ hostvars[inventory_hostname] }}" 
           
#dont delete the entire dir, delete the file to update it. 
- name: delete json file 
  file:
    path: "{{ json_files  }}/{{ inventory_hostname }}.json"
    state: absent
  failed_when: false
  delegate_to: localhost

- name: Dump all vars for dedugging
  action: template src=templates/dumpall.j2 dest="{{json_files}}/{{ inventory_hostname }}.json"
  delegate_to: localhost

- name: run app.py to clean sensitive data from the json file
  script: app.py
  delegate_to: localhost
  ignore_errors: true
```
Create the ```facts\files\app.py``` and add the following.
```
#!/usr/bin/env python
# orginal found at https://hindenes.com/trondsworking/2016/11/05/using-ansible-as-a-software-inventory-db-for-your-windows-nodes/

import os
import rethinkdb
import json
import rethinkdb as r

from os import listdir
from os.path import isfile, join

import sys
reload(sys)
sys.setdefaultencoding('utf-8')

tempfolder = '../roles/facts/files/data/json/'


if not os.path.isdir(tempfolder):
    os.makedirs(tempfolder)

onlyfiles = [f for f in listdir(tempfolder) if isfile(join(tempfolder, f))]

for file in onlyfiles:
    host_name = str(file).replace('.json','') #str(file).split(".")[3] ##designed for hostnames too  I am using ips
    with open(os.path.join(tempfolder, file),'r+') as data_file:
        data = json.load(data_file)
        data['ansible_password'] = ''
        data['ansible_password_ad'] = ''
        data_file.close()
    with open(tempfolder+'/'+file, 'w+') as outfile:  
      json.dump(data, outfile, sort_keys=True, indent=2, separators=(',', ': '))
    outfile.close()
```


