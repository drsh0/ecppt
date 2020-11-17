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

##### Zone transfer: 

`dig axfr @target.com target.com` OR using `nslookup`:

```
nslookup
> server target.com
> ls -d target.com
```
##### DNS Port Open

`nmap -sS -p53 [NETBLOCK]` (TCP)
`nmap -sU -p53 [NETBLOCK]` (UDP)


#### Host Discovery

**fping**
`fping -a -g [IP + CIDR]`

**nmap**

`nmap -sn [IP + CIDR]`

## Appendix
