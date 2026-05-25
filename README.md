# home-network-security-audit
Local network reconnaissance and host discovery utilizing Nmap on an Ubuntu Linux virtual environment to analyze port vulnerabilities and service footprints.

This project demonstrates foundational skills in network security, host discovery, and vulnerability assessment. Using an **Ubuntu Linux Virtual Machine** in a bridged network configuration, I conducted a full-subnet scan of a local residential network using **Nmap (Network Mapper)**. The objective was to discover active hosts, map open ports, fingerprint running network services, and analyze defensive postures across the infrastructure.

---

## Technical Skills Demonstrated
* Network Reconnaissance & Host Discovery
* Linux Command Line Interface (CLI)
* Virtualization Infrastructure Integration (Bridged Networking)
* Active Scanning & Port Fingerprinting (`-sV`)
* Security Analysis & Risk Mitigation

---

## Infrastructure Tools Used
* **Operating System:** Ubuntu Desktop (Virtual Machine)
* **Hypervisor:** VMware (Configured with Bridged Adapter)
* **Security Tool:** Nmap (Network Mapper)

---

## Step-by-Step Execution & Methodology

### Phase 1: Network Environment Preparation
To allow the Ubuntu VM to interact directly with the local network rather than being isolated behind the host machine, I configured the VMware network settings to use a **Bridged Adapter**. 

I verified the VM's local IP address and subnet assignment using the Linux terminal:
```bash
ip a
```
* **Result:** The virtual environment successfully pulled a local IP address of `192.168.1.183` on a `/24` subnet mask, establishing the target search space as `192.168.1.0/24`.

### Phase 2: Host Discovery & Service Fingerprinting
I executed a comprehensive service and version detection scan across the entire 256-IP address block. This allowed me to simultaneously find active endpoints and probe their active TCP connections.
```bash
nmap -sV -T4 -oN portfolio_scan.txt 192.168.1.0/24
```
* **`-sV`**: Enabled service version intensity detection.
* **`-T4`**: Applied an aggressive timing template for efficient execution.
* **`-oN`**: Directed the live output logs into a dedicated text file for documentation.

---

## Project Findings & Artifacts

### Sanitized Nmap Scan Output Logs
Below is the raw, sanitized output captured from the `portfolio_scan.txt` log file:

```text
# Nmap 7.94SVN scan initiated as: nmap -sV -T4 -oN portfolio_scan.txt 192.168.1.0/24
Nmap scan report for Target-Gateway.local (192.168.1.1)
Host is up (0.0068s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE  VERSION
53/tcp  open  domain   Cloudflare public DNS
80/tcp  open  http     lighttpd 1.4.59
443/tcp open  ssl/http lighttpd 1.4.59

Nmap scan report for IoT-Smart-Display.local (192.168.1.81)
Host is up (0.011s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
7000/tcp open  rtsp    AirTunes rtspd
9080/tcp open  glrpc?

Nmap scan report for Mobile-Endpoint-1.local (192.168.1.104)
Host is up (0.0093s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT      STATE SERVICE    VERSION
49152/tcp open  tcpwrapped
62078/tcp open  tcpwrapped

Nmap scan report for Mobile-Endpoint-2.local (192.168.1.147)
Host is up (0.0096s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT      STATE SERVICE    VERSION
49152/tcp open  tcpwrapped
62078/tcp open  tcpwrapped

Nmap scan report for Analyst-Audit-Workstation.local (192.168.1.183)
Host is up (0.0019s latency).
All 1000 scanned ports on Analyst-Audit-Workstation.local are in ignored states.
Not shown: 1000 closed tcp ports (conn-refused)

Nmap scan report for Mobile-Endpoint-3.local (192.168.1.236)
Host is up (0.014s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT      STATE SERVICE    VERSION
49152/tcp open  tcpwrapped
62078/tcp open  tcpwrapped

Service detection performed. Please report any incorrect results at https://nmap.org .
# Nmap done -- 256 IP addresses (6 hosts up) scanned in 161.38 seconds
```

---

## Security Analysis & Mitigation Strategies

Based on the scan findings, I analyzed the 6 active hosts and formulated the following risk assessments:

1. **Exposed Administrative Interfaces (Ports 80/443):** 
   * **Analysis:** The `Target-Gateway` (router) has open HTTP/HTTPS ports running `lighttpd 1.4.59`. If this web service allows external administrative access, it becomes a high-priority target for brute-force login attempts.
   * **Mitigation:** Ensure remote management WAN options are disabled. Change default administrative passwords immediately, and enforce HTTPS encryption exclusively across internal connections.

2. **Presence of "tcpwrapped" Services on Mobile Endpoints:**
   * **Analysis:** The three discovered mobile endpoints explicitly returned a `tcpwrapped` status on ports `49152` and `62078`. This confirms Nmap completed a brief TCP handshake, but the connection was immediately terminated by the host device prior to data payload processing.
   * **Security Significance:** This indicates an active, defensive network state. The endpoints are utilizing internal security controls, software firewalls, or TCP wrappers designed to detect unauthenticated network probing and sever the link to protect the device.

3. **Secure Posture of the Testing Workstation:**
   * **Analysis:** The testing workstation (`192.168.1.183`) safely returned `1000 closed tcp ports`. This proves that the local Linux instance maintains no open ears or exposed listener services, reducing its overall attack surface to zero on the subnet.

---

## Conclusion
This project successfully demonstrates the reconnaissance and host discovery phase of an operational network audit. It maps network asset visibility across 256 addresses and breaks down the behavioral responses of internal firewalls. Maintaining routine network audits ensures rogue devices are detected early and administrative portals remain fortified against external mapping.

