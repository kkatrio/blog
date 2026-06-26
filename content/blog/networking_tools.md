---
title: "Networking tools"
date: 2019-02-04T12:40:59+02:00
draft: false
---

### active connections
```
ss -tu   (-t tcp, -u udp)
```

### where the server is listening
```
ss -tunpl (-n do not resolve DNS, -p PID)
```

### scan local network
```
nmap -n 192.168.1.0/24
```

### debug by slowing down
```
tc qdisc add dev <interface> root netem delay 500ms
```
bring back
```
tc qdisc del dev <interface> root netem
```
show current settings
```
tc qdisc show
tc class show dev DEV
tc filter show dev DEV
```

### capture packets
```
tcpdump -i <interface>
```

### tshark goodness
[using-tshark-watch-and-inspect-network-traffic](https://www.linuxjournal.com/content/using-tshark-watch-and-inspect-network-traffic)
