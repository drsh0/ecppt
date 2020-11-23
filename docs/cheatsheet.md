# eCPPTv2 Cheatsheet

## Network Security

### Information Gathering

#### WHOIS

* For maximum information visit the registrar's WHOIS service
* `whois`
  * `whois $domain`
  * `whois -h $whois_server $domain` - use custom registrar server

#### DNS

Common commands `dig`: `dig target.com [+short | PTR | MX | NS]`
Common commands `nslookup`: `nslookup --type=[PTR | MX | NS] target.com`

**Zone transfer** 

`dig axfr @target.com target.com` OR using `nslookup`:

```
nslookup
> server target.com
> ls -d target.com
```
**DNS Port Open**

`nmap -sS -p53 [NETBLOCK]` (TCP)
`nmap -sU -p53 [NETBLOCK]` (UDP)


#### Host Discovery

**fping**

`fping -a -g [IP + CIDR]`

**nmap**

`nmap -sn [IP + CIDR]`

---
### Scanning

**TCP Flag Packet Crafting with hping3**

Send SYN packet to specified port + IP 3 times

`hping3 -S <IP> -p <port> [-c 3]`

Estimate zombie traffic

`hping3 -S -r -p [port] [IP]`

Send packets via zombie host

`hping3 -a [ZombieIP] -S -p [TargetPort] [TargetIP]`

**nmap NSE**

Update NSE DB - `nmap --script-updatedb`

NSE Script Help - `nmap --script-help <script name>`

Useful NSE scripts:

```
- whois-domain
- smb-os-dicovery
- smb-enum-shares
- auth
- default
```
---
### Enumeration

#### SMB/NetBIOS

Gather all information about a NB target:

<i class="fa fa-windows fa-lg"></i>

`nbstat -A <target IP>`

Check for shares: `net view <IP>`

Browse shares: `net use <local drive letter> \\<IP>\<remote share>`

e.g. `net use K: \\192.168.1.11\C` to mount remote C: share to K:`

<i class="fa fa-linux fa-lg"></i>

`nbtscan -v <target IP/CIDR>`

List shares: `smbclient -L <IP>`

* check for `$` shares (hidden)
* check for `$IPC` shares (null session attack)

Mount share: `mount.cifs //<IP>/<remote share> /<localdir> user=_, pass=_`

## Appendix
