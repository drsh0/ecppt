# Network Security

## Information Gathering

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