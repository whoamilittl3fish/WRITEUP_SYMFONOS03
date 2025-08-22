# Symfonos 3 Walkthrough

This write-up demonstrates how to approach and exploit the **Symfonos 3** vulnerable machine using Kali Linux tools.  
The process follows a standard penetration testing methodology: reconnaissance, enumeration, exploitation, and privilege escalation.  

---

## Section 1: Reconnaissance (Information Gathering)

Once the Symfonos 3 VM is running, note the IP address it displays.  
This simulates the victim machine’s IP address in a real-world scenario.  

### Full Port Scan
Use **nmap** to scan all ports and detect services:  

```bash
nmap -sS -A -O -p- <target-ip>
```
With this, we can stealth scan SYN, collect information such as service + version, scan all port.
Options:
  -  sS: half-open scan with SYN (SERVER) -> SYN-ACK (CLIENT) -> RST (SERVER)
  -  -A: -sV, -O, --script=default, --traceroute
  -  sV: output information of service, version
  -   O: OS dection
  - -p-: scan all port (65535 ports)
  -  
  ![SCAN PORT](image.png)


