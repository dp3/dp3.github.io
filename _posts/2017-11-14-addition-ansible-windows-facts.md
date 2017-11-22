---
layout: post
title: "Additional Ansible Windows Facts"
date: 2017-11-14 11:37:06 -0700
comments: false
---
Ansible is a great tool to manage anyhost Linux or Windows. However it is missing some key tools and features. Here is a playbook and instructions to gather all installed programs on a Windows host.

Start with creating a new role, I named mine facts.

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

Now create the ```facts/files/getFacts.ps1``` file and add the follwoing below. This file will be copied to each Windows host and run return a dictionary of all installed programs.
```
#sourced from https://hindenes.com/trondsworking/2016/11/05/using-ansible-as-a-software-inventory-db-for-your-windows-nodes/
$packages = Get-WmiObject -Class Win32_Product
$returnpackages = @{}
foreach ($package in $packages)
{
            $subpackagedictionary = @{"Name" = $Package.Name; "Version" = $Package.Version; "Caption" = $Package.Caption;}
                $returnpackages.Add($Package.Name, $subpackagedictionary)
}
#Write-Host $returnpackages."Google Chrome".Name
#Write-Host $returnpackages."Google Chrome".Version
$returnpackages
```

For debugging purposes you may want to create JSON files from the returned dictionary. To do so create a ```facts/templates/dumpall.j2``` and add the following.
```
  {{ vars_hack | to_json }}

```

Create the ```facts\files\app.py``` and add the following below. This files erases passwords dumped in the the json files.
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
    host_name = str(file).replace('.json','')  ##I am using host ips, to use hostnames use str(file).split(".")[3]
    with open(os.path.join(tempfolder, file),'r+') as data_file:
        data = json.load(data_file)
        data['ansible_password'] = ''
        data['ansible_password_ad'] = ''
        data_file.close()
    with open(tempfolder+'/'+file, 'w+') as outfile:
      json.dump(data, outfile, sort_keys=True, indent=2, separators=(',', ': '))
    outfile.close()
```

Create one last file the vars file for the role. Create ```vars/main.yml``` and add the following.

```
json_files: '../roles/facts/files/data/json/'
host_program_csv: '../roles/facts/files/data/programs'
```

Now install the redis service.
For Centos 7
```
  yum install redis
  systemctl enable redis
  systemctl start redis
```

Generate a hash for redis
``` 
echo "redis-password-string" | sha256sum 
```

Edit the redis config file in ```/etc/redis.conf```and find the line *requirepass* and insert the genrated hash

Edit your base ansible.cfg usualy in the first directory of your ansible setup and add the line

```
fact_caching_connection = <redis-ip>:6379:0:<redis-hash>

```
