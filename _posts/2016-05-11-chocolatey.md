---
layout: post
title: "Chocolatey \"The Windows\" Package Manager"
date: 2016-05-10 10:10:06 -0700
comments: false
---
Install Chocolatey via PowerShell

```
iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1')) 
```

Install via Command Prompt

```
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
```

Create a repo

```
 choco new <project name>
```

Edit the <project name>.nuspect file and the install, uninstall files under the tools directory
