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

Also I use metasploit scanner `/smb/smb_ms17_010` and achieved this result:

```
[+] 10.10.118.190:445     - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard Evaluation 14393 x64 (64-bit)
```
I used more scans to find more information. 

```
msf6 >  nmap -sC -sV -T4 10.10.118.190
[*] exec:  nmap -sC -sV -T4 10.10.118.190

Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-10 15:12 UTC
Nmap scan report for ip-10-10-118-190.eu-west-1.compute.internal (10.10.118.190)
Host is up (0.0015s latency).
Not shown: 995 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server 2016 Standard Evaluation 14393 microsoft-ds
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-01-10T15:13:05+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=Relevant
| Not valid before: 2024-01-09T14:44:32
|_Not valid after:  2024-07-10T14:44:32
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2024-01-10T15:12:25+00:00
MAC Address: 02:8A:8D:BF:D2:95 (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-01-10T15:12:25
|_  start_date: 2024-01-10T14:44:51
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 1h35m59s, deviation: 3h34m39s, median: 0s
|_nbstat: NetBIOS name: RELEVANT, NetBIOS user: <unknown>, NetBIOS MAC: 028a8dbfd295 (unknown)
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: Relevant
|   NetBIOS computer name: RELEVANT\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-01-10T07:12:25-08:00
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 53.10 seconds
msf6 > nmap -p445 --script smb-vuln-ms17-010 10.10.118.190
[*] exec: nmap -p445 --script smb-vuln-ms17-010 10.10.118.190

Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-10 15:13 UTC
Nmap scan report for ip-10-10-118-190.eu-west-1.compute.internal (10.10.118.190)
Host is up (0.00023s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:8A:8D:BF:D2:95 (Unknown)

Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
```

After trying exploits for listed above CVEs without any results I started serching information about smb enumeration. [This] (https://medium.com/@taliyabilal765/smb-enumeration-guide-b2cb5cfb20e6) guide helps me a lot. The solution was much simplier than I thought.

First

```
└─# smbclient -L 10.10.134.13
Password for [WORKGROUP\root]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        nt4wrksv        Disk
```
Namespace potentially we can use. I tried this
```
└─# smbclient \\\\10.10.134.13\\nt4wrksv
Password for [WORKGROUP\root]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Jul 25 21:46:04 2020
  ..                                  D        0  Sat Jul 25 21:46:04 2020
  passwords.txt                       A       98  Sat Jul 25 15:15:33 2020

                7735807 blocks of size 4096. 4949988 blocks available
smb: \> 
```
Use mget to download this file. At file we can find:
```
[User Passwords - Encoded]
Qm9iIC0gIVBAJCRXMHJEITEyMw==
QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk
```
At first glance it looks like Base64 encoding.



