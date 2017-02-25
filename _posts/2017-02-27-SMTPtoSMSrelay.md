---
layout: post
title: "SMTP to SMS relay"
date: 2017-02-25 11:12:06 -0700
comments: false
---

This script will send SMS via various smtp relays. 

```
import smtplib

# Use sms gateway provided by mobile carrier:
# alltel         number@message.Alltel.com 
# at&t:          number@mms.att.net
# boost:         number@myboostmobile.com
# google voice   number@@txt.voice.google.com (needs work)
# metro pcs:     number@mymetropcs.com
# sprint:        number@page.nextel.com
# straight talk  number@mypixmessages.com
# t-mobile:      number@tmomail.net
# us cellular:   number@mms.uscc.net  
# verizon:-       number@vtext.com
# virgin:        number@vmpix.com

server = smtplib.SMTP( "smtp.gmail.com", 587 )
server.ehlo()
server.starttls()
server.ehlo()
server.login( 'Google username', '<Google app password >' )


#server.sendmail( '<from>', '<number>@tmomail.net', 'Hello!' )

server.sendmail( '<sending phone number>', '<receiving phone numver>@<domain.com>', 'Leory Jenkins!!!!' )
```

