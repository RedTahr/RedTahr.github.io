# improving ones home network; for the children... or laughs

For children, education is still the best bet, as if they go rogue/out or switch to mobile data they're beyond your protection/control/safety-net anyway.

So this is mostly for my amusement and to prompt me to write more, and do some of these ideas I have for my home network.

If you've ended up here wanting actual information, then I'll direct you to [https://decentsecurity.com/] for helpful guidance.

## phase 1, simple things

Choose a DNS service to filter things, I use cleanbrowsing [https://cleanbrowsing.org/filters] as it performed better than opendns [https://hackernoon.com/dns-performance-comparison-google-quad9-opendns-norton-cleanbrowsing-and-yandex-d62d24e38f98] but mostly because it forces safe search on most search engines. My IAP has a filtering service I could pay for, and there are other services about too. If you're with a big telco they might not let you easily change your DNS settings in your router, so complain, buy another router, or look up how to telnet into your router...what could go wrong...

## phase 2, not so simple

To block more malware/adware in more things I setup pi-hole [https://pi-hole.net/], I followed Scott Helmes guide [https://scotthelme.co.uk/tag/pi-hole/] though I haven't done the DNS-over-HTTPS / Lets Encrypt thing yet, and had a vague go at setting the pi up as DHCP, but got something wrong, so just have it as the first DNS entry on my home router instead, plus i'm paranoid an apt-get upgrade will bork something on the pi, or have the pi burn out while i'm away and have to remotely talk my wife through getting the internet back working.
I've since written my own notes with a few additions, [https://gist.github.com/RedTahr/925a322baefb4e5b28318838b4a61b45], highlights I guess are the addition of ufw (uncomplicated firewall), fail2ban, log2ram and the dark theme.

The main issoe I've hit with pi-hole is that tracked links won't work, I've had to white list a few domains to let work surveys resolve via the tracked url; such as http(s)://po.st I think was one. But otherwise pi-hole is a very tidy solution, and makes mobile apps faster as ads just silently don't load, I support the sites/apps directly where I can.

## phase 3, log all the things

Been working my way through some splunk training, and have previously installed sysmon [https://github.com/SwiftOnSecurity/sysmon-config] config by Swift On Security, so now I want to get my router and the pi sending logs to splunk too.

To start with getting some localmachine windows logs into splunk is straight forward:
In Splunk > Settings > Data > Data Inputs and choosing things you'd like. Microsoft-Windows-Sysmon/Operational is the log (under available logs) that you'll want if you're after sysmon logs.

Then for pi-hole logs to splunk you'll probably need the splunk forward for ARM, there's a "pi-hole visualizer" [https://splunkbase.splunk.com/app/3023/#/details] or the "pi-hole app for splunk" [https://splunkbase.splunk.com/app/4116/], I haven't set these up yet.

Then your router...the router admin interface might have a "management > system log" type option, where you can configure the IP:port you want the logs to be sent to, then in Splunk, again go Settings > Data > Data Inputs and choose the appropriate input type, probably UDP, and configure it appropriately based on how you configured your router.

##### created 2019-04-23
##### updated 2019-08-20
##### change log [https://github.com/RedTahr/RedTahr.github.io/commits/master]
##### © Red Tahr
