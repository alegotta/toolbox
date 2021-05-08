# Networking

Dependencies: `NetworkManager`, `nmap` (some commands require root permissions)

### Show IP Address

```bash
ip address
```

### Scan WiFi networks

```bash
nmcli -f "CHAN,BARS,SIGNAL,SSID,RATE,SECURITY" d wifi list | sort -n
```

### List hosts

##### Simple list

```bash
nmap -sn [ip-range]
```

##### Fast port scan

```bash
nmap -F [ip-range]
```

##### Port scan with service version

```bash
nmap -sV [ip-range]
```