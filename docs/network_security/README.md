# Network Security

## 1. Information Gathering

* Focus on **Business** 
  * people, products, services
* and **Infrastructure**
  * network, systems, domains, IPs
* **Passive** - do not let the other party know we are present
* **Active** - interact directly with target system

### Public Information
* web presence
  * DUNS and CAGE codes for govt contacts
* partners, 3rd parties
* job postings
* financials
  * SEC.gov EDGAR
  * crunchbase
  * inc.com
* harvesting
  * google dorks for files, handles
  * tool: FOCA, theHarvester
* cache + archive sites

### Infrastructures

Main goal is to retrieve data such as: domains, netblocks, IPs, mail servers, ISPs

#### Domains

* **WHOIS** - obtain as much information as possible
* **Domains**
  * PTR - Reverse DNS lookups will only work a PTR record is set to respond
  * MX - Mail server IP
  * Zone Transfers - misconfig that allows us to all DNS records for the zone inc. A records
    * requires NS of domain

* **IPs**
  * use domains found to resolve IPs
  * one IP can host multiple sub/domains
  * search engines available for this e.g. Bing: `ip:$IP`, Domaintools

* **Netblocks** **& AS**
  * Either a corp will own blocks organised within autonomous systems
  * Usually rented out by others so might instead get information on host e.g. Amazon, Digital Ocean

#### IPs

##### Host Discovery
Once we have IPs or if they are already provided check:

* host alive?
* IP has associated domains or hostnames?

ICMP is not the most reliable way as firewalls may block or filter ICMP.

* **Tools**
  * fping
  * nmap

##### DNS Discovery

If netblocks or IPs are known, they can be scanned via nmap for all DNS servers (port 53, both TCP and UDP)

### Tools

* [DNSDumpster](https://dnsdumpster.com/) - find hosts from domain
* DNSEnum - Obtain common DNS information + AXFR queries + subdomain wordlist
* DNSMap - sub domain brute forcing

## 2. Scanning

**PPS** = ports, protocols, services

### TCP/IP

* **Three Way Handshake** - SYN --> SYN+ACK --> ACK
* `hping` can be used to craft packets
* If crafted packet returns `RA` (reset + ack) then the port appears to be unused. `SA` flag (syn ack)  would suggest port is open. 

### `nmap` Deep Dive

_Note: most specific commands are added in the cheatsheet instead_

<!-- tabs:start -->
#### **TCP SYN**
* `-sS` - TCP SYN Scan - most popular; quick and accurate
  * send SYN packet
  * if SYN/ACK received then port is open. If RST-ACK received then it is closed
  * nmap then sends a RST to close the connection
  * if no response receied = "filtered"
#### **TCP Connect**
* `-sT` - fallback default if SYN scan doesn't work
  * relies on OS not packets
  * we reply with ACK and then RST-ACK

#### **UDP**
* `-sU` - UDP scan; takes some time
  * connection closed with ICMP port unreachable packet

#### **Idle**
* `-sI` - idle scan
  * use fragmentation ID header
  * target must use IP ID **incremental** generation
  * forge packet that is sent to target with source IP belonging to the zombie
  * SYN/ACK the zombie once the above is completed to see if IP ID **incremented by 2**
    * this would indicate that the port is open on target
  * If **incremented by 1** then the port appears to be closed.
  * need to use `-Pn` to ensure our machine doesn't send pings 

#### **Never DNS Resolution**
* `-n` - prevent DNS resolution; helps with stealth

#### **FTP Bounce**
* `-b` - exploit FTP `PORT` command
  * if FTP server is vulnerable, we can launch port scans from the FTP server

#### **IP Protocol Scan**
* `sO` - shows what IP protocols are supported instead of ports

#### **Misc**
* `-sN` - TCP null scan
* `-sF` - FIN scan
* `-sX` - Xmas scan
* `-sA` - TCP ACK Scan
  * used to map out firewall rules; usually would expect RST where there is no filtering
  * e.g. no reply from specific port means it was filtered

#### **Output**
* `-oN` - normal output stored in file
* `-oX` - XML output
* `-oG` - grepable output
<!-- tabs:end -->

### Service + OS Detection

1. Banner Grabbing
   * use banner seen when connecting via `ncat` etc. as service detection. FP prone.
2. Service Probing
   * use nmap `sV` to probe services based on signatures
3. OS Fingerprinting
   * passive - via packets received e.g. `p0f`
   * active - send packets and wait for reply (uses TCP/IP)

### Firewall + IDS Evasion

* can be done via **packet fragmentation** e.g. `nmap -sS -f [IP]`
* use **decoys** to make tracking our IP harder e.g. `nmap -sS -D <decoyIP1>,<decoyIP2>,ME <target>`
* modify **timing** using `-T[0-5]` to slow down or speed scans
* change **source port** if traffic is only allowed through specific ports e.g. `--source-port <port>`

## 3. Enumeration

After finding devices and resources on a network, gather more detailed info on them = enumeration.

### NetBIOS

NetBIOS = Network Basic Input Output System

* Purpose: Allow apps from diff. OS communicate with each other over LAN e.g. share printers and files
* Ports: U137, U138, T139
* SMB = Service Message Block: T445

**Name Service (U137)**
  
  * map NetBIOS (NB) name --> IP
  * [16char name]00-FF - the suffix tells us the service/type of the resource
  * WINS - Windows Internet Name Service maps names --> IP in Windows

**Datagram Service (U138)**

  * sends and receives messages from NB names inc. broadcasts. 

**Session Service (T139)**

  * allows two NB names to establish connection to exchange data
  * name resolved -> connection established -> session request -> session response

### SNMP

SNMP = Simple Network Management Protocol

* Ports: U161 General, U162 Trap Messages
* MIBs have definitions in which paths are traversed until the object identifier (OID) e.g. `1.3.6.1`

## 4. Sniffing & MITM

* **Passive Sniffing** - Wireshark
* **Active Sniffing**
  * MAC flooding - flood the CAM table so the switch operates as a hub
  * ARP poisoning
    * redirect traffic from victims to a specific machine

### ARP

- Address Resolution Protocol (ARP)
- match L3 with L2
- packets: ARP requests, ARP replies
- ARP table contains IP and MAC mappings
- If MAC is not found in table, a broadcast is sent. The machine with that IP replies with its MAC address
- **Attacks** 
  - host poisoning - use Gratuitous ARP reply packets to trick host A to send to host M instead of host B
  - gateway poisoning - use Gratuitous ARP reply packets to tell all hosts that machine M is the gateway. Can cause DoS if _M_ is not capable of handling high no. of packets.

### MITM