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
    
<img width="977" height="780" alt="image" src="https://github.com/user-attachments/assets/e9cc88cf-161b-4864-8db6-756de097dfd3" />


## Section 2: Enumeration Results

From the **nmap** scan, we discovered the following open services:

- **Port 80 (HTTP)** → Apache httpd 2.4.25  
- **Port 21 (FTP)** → Version 1.3.5b  

### FTP Enumeration
- Tried searching for exploits related to **FTP 1.3.5b**. Found a possible **Remote Command Execution** exploit:  
  [Exploit-DB 49908](https://www.exploit-db.com/exploits/49908)  
- Attempted to log in using the **anonymous** account.  
- Login failed, and it seems `mod_copy` is required but not enabled or accessible.  
- At this stage, FTP exploitation was unsuccessful.  

### Web Enumeration
Using tools such as **Nikto**, **Gobuster**, and **Dirb**, we discovered the following directories on the web server:

<img width="887" height="690" alt="image" src="https://github.com/user-attachments/assets/26093403-721a-42a3-a049-b77507e10fb0" />


- `/index.html` → Default index page
- `/server-status` → Apache server status page (restricted)  
- `/cgi-bin/` → Common directory for scripts  
- `/gate/` → A potential custom or sensitive directory worth deeper investigation  

Next steps will involve analyzing the content of `/gate/` and exploring possible vulnerabilities in `/cgi-bin/`.  

### CGI-BIN Enumeration

The web server hosts an active **/cgi-bin/** directory.  
Older servers running **bash** with CGI are often vulnerable to the well-known **Shellshock** exploit.  

- Exploit reference: [Exploit-DB 34895](https://www.exploit-db.com/exploits/34895)  

Next step: Perform a directory scan specifically on `/cgi-bin/` to identify any accessible CGI scripts that may be exploitable.  

Example scan:
```bash
gobuster dir -u http://192.168.189.139/cgi-bin/ -w /usr/share/wordlists/dirb/common.txt
```
<img width="624" height="154" alt="image" src="https://github.com/user-attachments/assets/691dce90-34fa-4b1a-8b4a-ef59a8d92e01" />
<img width="625" height="186" alt="image" src="https://github.com/user-attachments/assets/fc74caac-fdbb-4c5f-aa6a-f9d438e6e387" />

