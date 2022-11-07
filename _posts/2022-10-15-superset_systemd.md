---
layout: post
title: "Oni 3D Printer"
date: 2016-05-10 09:09:06 -0700
comments: false
---

Create the file superset.service at /lib/systemd/system/superset.service and insert the following. Note the user is sysadmin, the port is 8088 and superset is installed in the /opt/super python virtual environment

```
[Unit]
Description=Start Superset

[Service]
Type=simple
User=sysadmin
Group=sysadmin
Environment="FLASK_APP=superset"
ExecStart=/opt/super/bin/superset run -p 8088 --with-threads --reload --debugger --host 0.0.0.0

[Install]
WantedBy=multi-user.target
```

To start the service
```
systemctl daemon-reload
systemctl start superset
```
