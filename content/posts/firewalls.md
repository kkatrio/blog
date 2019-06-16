---
title: "Firewalls"
date: 2019-06-16T22:36:47+03:00
---

## UFW

```
#!/bin/sh

#wget https://www.cloudflare.com/ips-v4 -O ips-v4
#wget https://www.cloudflare.com/ips-v6 -O ips-v6

for ip in `cat ips-v4`; do ufw allow from $ip to any port 80 proto tcp; done;
for ip in `cat ips-v4`; do ufw allow from $ip to any port 443 proto tcp; done;
for ip in `cat ips-v6`; do ufw allow from $ip to any port 80 proto tcp; done;
for ip in `cat ips-v6`; do ufw allow from $ip to any port 443 proto tcp; done;
```

and `ufw reload`. No need to disable the firewall to update its rules.
