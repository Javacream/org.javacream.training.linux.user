# Linux Network Debugging & Monitoring Cheat Sheet

## Interface & IP Information

### Show interfaces and IP addresses
```bash
ip addr
```

### Show routing table
```bash
ip route
```

### Show link/interface state
```bash
ip link
```

### Legacy alternatives
```bash
ifconfig
route -n
```

---

## Connectivity Testing

### Ping a host
```bash
ping google.com
ping 8.8.8.8
```

### Trace network path
```bash
traceroute google.com
```

Install if needed:
```bash
sudo apt install traceroute
```

### Faster/more detailed traceroute
```bash
mtr google.com
```

Install:
```bash
sudo apt install mtr
```

---

## DNS Debugging

### Query DNS
```bash
dig google.com
dig google.com +short
```

### Check specific DNS server
```bash
dig @8.8.8.8 google.com
```

### Alternative
```bash
nslookup google.com
```

---

## Open Ports & Connections

### Show listening ports
```bash
ss -tulnp
```

### Show active connections
```bash
ss -tp
```

### Check process using a port
```bash
sudo lsof -i :8080
```

---

## Packet Capture & Traffic Analysis

### Capture packets
```bash
sudo tcpdump -i eth0
```

### Capture traffic for a host
```bash
sudo tcpdump host 1.1.1.1
```

### Capture traffic for a port
```bash
sudo tcpdump port 443
```

### Save capture to file
```bash
sudo tcpdump -i eth0 -w capture.pcap
```

### Live bandwidth monitoring
```bash
iftop
```

Install:
```bash
sudo apt install iftop
```

### Per-process bandwidth
```bash
nethogs
```

---

## Real-Time Monitoring

### Network statistics
```bash
sar -n DEV 1
```

### Interface counters
```bash
ip -s link
```

### Continuous monitoring
```bash
watch -n 1 ss -s
```

### Monitor bandwidth usage
```bash
vnstat
```

---

## HTTP / API Debugging

### Test HTTP endpoint
```bash
curl -v https://example.com
```

### Show headers only
```bash
curl -I https://example.com
```

---

## Wireless Debugging

### Show Wi-Fi info
```bash
iwconfig
```

### Scan networks
```bash
sudo iwlist wlan0 scan
```

### Modern tool
```bash
nmcli device wifi list
```

---

## Firewall & NAT

### Show iptables rules
```bash
sudo iptables -L -n -v
```

### nftables
```bash
sudo nft list ruleset
```

### UFW status
```bash
sudo ufw status verbose
```

---

## Socket & Service Testing

### Test TCP connection
```bash
nc -vz host 443
```

### Listen on a port
```bash
nc -l 8080
```

### OpenSSL TLS debug
```bash
openssl s_client -connect google.com:443
```

---

## Performance & Throughput

### Network speed test between hosts

Server:
```bash
iperf3 -s
```

Client:
```bash
iperf3 -c SERVER_IP
```

---

## Logs & Diagnostics

### Kernel/network logs
```bash
dmesg | grep -i eth
```

### NetworkManager logs
```bash
journalctl -u NetworkManager
```

### Follow logs live
```bash
journalctl -f
```

---

## Useful One-Liners

### Top remote IPs connected to server
```bash
ss -tn | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr
```

### Find dropped packets
```bash
netstat -s | grep -i drop
```

### Monitor DNS queries live
```bash
sudo tcpdump -i any port 53
```

### Monitor HTTPS connections
```bash
sudo tcpdump -i any port 443
```

---

## Recommended Tool Stack

| Purpose | Tool |
|---|---|
| Interfaces/routes | `ip` |
| Connections/sockets | `ss` |
| Packet capture | `tcpdump` |
| DNS | `dig` |
| Connectivity path | `mtr` |
| HTTP/API | `curl` |
| Throughput | `iperf3` |
| Live traffic | `iftop` |
| Per-process traffic | `nethogs` |

---

## Typical Debug Workflow

1. Check interface:
```bash
ip addr
```

2. Check route:
```bash
ip route
```

3. Ping gateway:
```bash
ping GATEWAY_IP
```

4. Ping internet:
```bash
ping 8.8.8.8
```

5. Test DNS:
```bash
dig google.com
```

6. Check listening services:
```bash
ss -tulnp
```

7. Capture packets:
```bash
sudo tcpdump -i any
```

8. Trace route issues:
```bash
mtr google.com
```
