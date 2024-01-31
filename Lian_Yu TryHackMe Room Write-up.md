# Lian_Yu TryHackMe Room Write-Up

## Task 1: Enumeration and credentials searching

Added the IP to /etc/hosts and conducted an initial port scan:

1.1 Looking for open ports `nmap -sC -sV arrow.thm`. Discovered 3 open ports:

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/5605fe93-1ff0-4840-ac47-f39709a9ee36)

1.2 Looking for directories

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/16a62514-ee45-43a2-9733-6e0d9b8ee9ed)

Going to this page and viewing source code. Interesting, lets remember this.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/355894c7-d175-46a2-ab2f-991f5cf68619)

I spen some time on Fuzzing using this [dicts](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing). Finally I found the page and first answer

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/bbe2d9c4-ff5d-47ab-80a2-75adf3b5a73b)

1.3 Looking for filename

Viewed source code. `.ticket` might be file extension.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/1e88874a-ed13-4e99-9fbc-4cdc515db486)

Yep. I found it

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/dc774a4a-ab90-40ce-b6ef-c9bc4284e52a)

1.4 Finding ftp password

There are some encoded message

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/17b77c13-18a5-4c45-8d93-1f90bd81a143)

Lets try to decode it with CyberChef

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/00356a2b-1dd1-4d8e-ba81-4f8d127c5f52)

This answer looks like password and its the answer for next question.

1.5 Connect to ftp and find ssh pass. We found username at first directories fuzzing.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/7afb3103-8b31-4369-b6d0-5e00f8e11095)

Some images at directory. Downloaded them and analyzed.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/66c47653-85fd-4c5b-8e46-4e6542e781a3)

Possibly this is the file we are looking for.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/fb4c5393-57e4-45f2-a2cf-0e8b8080adc9)

I tried different tools to find hidden files, but finaly I decided to change first magic bytes to correspond .PNG format.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/1eeba687-27c9-4b26-891a-1b1a65341cc8)

Result

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/1bf334db-496a-4350-9805-eaa4780a49fe)

Hm, I think this is password for some hidden info. The guess was correct.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/0373865c-a5ce-4aae-8a3b-c95b707e013b)

We found the answer to the next question in this archive.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/2d2d02e8-8c8e-49d4-874d-56305c05555e)

Used the password to connect via ssh as slade (second user we found in /home directory).

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/fe280d85-2b3f-45f8-8a1d-31eb24659b6c)

## Task 2: Find the flags

2.1 User flag is inside user's folder.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/616a0d98-c1bb-4434-8265-71ee30f7c6f7)

2.2 Escalate priveleges. Lets try first guess.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/c2dae965-987a-4560-99ec-3a2de858e86a)

Easy breezy. Check [Gtfobins](https://gtfobins.github.io/gtfobins/pkexec/#sudo).

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/1ae7edab-05c4-40c4-b34f-ee5547302892)

Run this command.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/717fffc9-bd9c-4382-82bf-706837c8a9a0)

2.3 Root flag.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/6c5d9982-8d7c-4857-be3f-ce3fb61fdb69)

*Note: This write-up is intended for educational purposes and highlights the steps taken during the Lian_Yu [TryHackMe](https://tryhackme.com/room/lianyu#) room.*







