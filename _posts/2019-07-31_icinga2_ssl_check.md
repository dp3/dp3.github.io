---
layout: post
title: "Icinga2 SSL Check"
date: 2019-07-29 09:09:06 -0700
comments: false
---
1. Install nagios via yum or apt get 
```
  apt-get install
```
or 
```
  yum install
```

2. Add the following ``` /etc/icinga2/conf.d/services.conf```
```
  apply Service "Check SSL certificate for " for (config in host.vars.ssl_domains) {
    check_command = "check_ssl"
    vars.hostname = config
    vars.critical = "15"
    vars.warning = "30" 
    assign where host.vars.ssl_domains
  }
```

3. Add the following to ``` /etc/icinga2/conf.d/commands.conf ```  
```
  object CheckCommand "check_ssl" {
    import "plugin-check-command"
    command = [PluginDir + "/check_http"]
    arguments = {
      "-H" = "$hostname$"
      "-C" = "$critical$"
    }
  }
```

4. Edit the ``` /etc/icinga2/conf.d/hosts.conf``` 
```
object Host "webhost.fqdn.com" {
    import "generic-host"
    address = "10.0.2.1" 
    vars.disks["disk /"] = { disk_partitions = "/" }
    vars.os = "Linux"
    vars.remote_client = "webhost.fqgn.com" 
    vars.users_wgreater = 10
    vars.users_cgreater = 20 
    vars.ssl_domains = ["website.fqdn.com","websitetwo.fqdn.com"] 
    vars.notification["mail"] = {
    groups = [ "icingaadmins" ] 
    } 
}
```
