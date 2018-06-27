---
layout: post
title: "Plex Media Server"
date: 2016-03-19 16:25:06 -0700
comments: false
---

Plex with Samba
Samba Install



Use apt-get to pull the samba packages:

```
$ sudo apt-get update
$ sudo apt-get install samba
```

Lets create a samba user:

```
$ useradd USER
$ passwd USER
$ smbpasswd -a USER
```

Now edit the config file:

```
$ sudo vim /etc/samba/smb.conf
```

Insert the following lines:

```
#this allows everyone to view the network share
[SMABA UNSECURE]
comment=PLEX-SAMBA
path= /location/to/the/folder/you/want/to/share
browsable=yes
writable=yes
guest ok=yes
admin users= adminuser

#this only allows the smbuser to access the directory
[SAMBA PASSWORD PROMPT]
path = /location/to/the/folder/you/want/to/share
valid users = smbuser
browseable = yes
writeable = yes
read only = no
guest ok = yes
```

Run 'testparm' to verify the config works:

```
$ testparm
```

Restart samba

```
$ sudo service smbd restart
```

Plex Install

```
$ wget https://downloads.plex.tv/plex-media-server/0.9.14.6.1620-e0b7243/plexmediaserver_0.9.14.6.1620-e0b7243_amd64.deb
$ sudo dpkg -i plexmediaserver_0.9.14.6.1620-e0b7243_amd64.deb
```

