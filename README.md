# Relevant
### Penetration Testing Challenge Solution

Firstly I used `nmap -sS -v -v -Pn 10.10.162.179`. 

Result: 
```
  PORT     STATE SERVICE       REASON
  80/tcp   open  http          syn-ack ttl 128
  135/tcp  open  msrpc         syn-ack ttl 128
  139/tcp  open  netbios-ssn   syn-ack ttl 128
  445/tcp  open  microsoft-ds  syn-ack ttl 128
  3389/tcp open  ms-wbt-server syn-ack ttl 128
  MAC Address: 02:FA:18:FA:35:01 (Unknown)
```

Used this command to enumerate RDP service `nmap --script "rdp-enum-encryption or rdp-vuln-ms12-020 or rdp-ntlm-info" -p 3389 -T4 10.10.162.179`.

Result:

```
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2024-01-09T18:52:43+00:00
| rdp-enum-encryption: 
|   Security layer
|     CredSSP (NLA): SUCCESS
|     CredSSP with Early User Auth: SUCCESS
|_    RDSTLS: SUCCESS
MAC Address: 02:FA:18:FA:35:01 (Unknown)
```
Possibly we can use **CVE:2018-0886**


