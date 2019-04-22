# improving ones home network; for the children... or laughs

For children, education is still the best bet, as if they go rogue/out or switch to mobile data they're beyond your protection/control/safety-net anyway.

So this is mostly for my amusement and to prompt me to write more, and do some of these ideas I have for my home network.

## phase 1, simple things

Choose a DNS service to filter things, I use OpenDNS [https://www.opendns.com/home-internet-security/], my IAP has a filtering service I could pay for, and there are other services about too. If you're with a big telco they might not let you easily change your DNS settings in your router, so complain, buy another router, or look up how to telnet into your router...what could go wrong...

## phase 2, not so simple

To block malware/adware in more things I setup pi-hole [https://pi-hole.net/], I followed Scott Helmes guide [https://scotthelme.co.uk/tag/pi-hole/] though I haven't done the DNS-over-HTTPS / Lets Encrypt thing yet, and had a vague go at setting the pi up as DHCP, but got something wrong, so just have it as the first DNS entry on my home router instead, plus i'm paranoid an apt-get upgrade will bork something on the pi, or have the pi burn out while i'm away and have to remotely talk my wife through getting the internet back working.

The main issoe I've hit with pi-hole is that tracked links won't work, I've had to white list a few domains to let work surveys resolve via the tracked url; such as http(s)://po.st I think was one. But otherwise pi-hole is a very tidy solution, and makes mobile apps faster as ads just silently don't load, I support the sites/apps directly where I can.

## phase 3, log all the things

Been working my way through some splunk training, and have previously installed sysmon [https://github.com/SwiftOnSecurity/sysmon-config] config by Swift On Security, so now I want to get my router and the pi sending logs to splunk too, thats the next step...

##### created 2019-04-23
##### change log [https://github.com/RedTahr/RedTahr.github.io/commits/master]
##### © Red Tahr