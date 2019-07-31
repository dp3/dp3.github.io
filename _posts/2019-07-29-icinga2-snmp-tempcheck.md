---
layout: post
title: "Icinga2 SNMP Temperature Check"
date: 2018-07-29 09:09:06 -0700
comments: false
---
After Icinga2 is all setup. 

1. Install nagios via yum or apt get 
```
  apt-get install nagios-plugins
```
or 
```
  yum install nagios-plugins-http.x86_64
```

2. Add the following ``` /etc/icinga2/conf.d/services.conf```
```
apply Service "Check temperature " for (config in host.vars.temperature) {
    import "generic-temperature-check"      
    check_command = "check_temp"
    vars.hostname = config
    assign where host.vars.temperature
}
```

3. Add the following to ``` /etc/icinga2/conf.d/commands.conf ``` 
```
object CheckCommand "check_temp" {
    import "plugin-check-command"
    command = [PluginDir + "/check_snmp"]
    arguments = {
    "-H" = "$hostname$"
    "-o" = "$temp_oid$"
    "-c" = "$temp_critical$"
    "-w" = "$temp_warning$"
    "-P" = "$temp_version$"
    "-C" = "$temp_community$"  
    }
 }
```

4. Edit the ``` /etc/icinga2/conf.d/hosts.conf``` file to include the new host to monitor. 
```
object Host "environment.fqdn.com" {
    import "generic-temperature-check"
    address="10.0.2.2"
    vars.hostname="environment.fqdn.com"
    vars.temperature = "environment.fqdn.com"
    vars.temp_critical= ["90","90"]
    vars.temp_warning= ["80","80"]
    /* APC temp oid codes, these may need to be changed  */
    vars.temp_oid = ["1.3.6.1.4.1.318.1.1.10.3.13.1.1.3.1", "1.3.6.1.4.1.318.1.1.10.3.13.1.1.3.2"]
    vars.temp_version="1"
    vars.temp_community="communityName"
    vars.notification["mail"] = {
    groups = [ "icingaadmins" ] 
    }
}
```
