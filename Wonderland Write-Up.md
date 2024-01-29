# Wonderland TryHackMe Room Write-Up

## Task 1: Enumeration

Added the IP to /etc/hosts and conducted an initial port scan:

1.1 Looking for open ports `nmap -sC -sV wonderland.thm`. Discovered 2 open ports:

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/628992fb-940a-4567-8f3c-4cbebc0e37dd)

1.2 Looking for pages using dirb `dirb http://wonderland.thm`.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/b1d45a8c-31c8-494a-9ef3-81e4ce312a86)

Proceeded further

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/9cc476f5-685d-470d-8809-6ee597dad86e)

Hmm. I have a guess

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/14d084c9-b7fa-49cc-95e2-c6d78130149c)

I supposed that there are might be so info at page source. After viewing code I found some pair that looks like pair username:password.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/509a29d6-da51-42fb-abd2-62848500652f)

I tried to connect via ssh and here we are!

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/a715de49-1264-436b-b008-175793623231)

## Task 2: Find user flag

2.1 Looking for possibilities. Tried `sudo -l`

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/f8cd76a3-702e-4261-96c3-1fdaeb7a03ea)

Hmm. I can run script inside alice folder as a `rabbit` user. Lets look forward.

Script calls random.choice

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/aca2c932-54a9-4fda-8bd9-d7a330101d78)

In order to exploit this I copied random.py to /home/alice folder and added this code to `def choice` to obtain reverse shell

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/3ea9a95d-2ac5-4db8-afa8-7978b14ee5a9)

Here what I've got after next command

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/b878d5cc-cb05-45e3-8315-80bc6ef9328e)

Result

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/064e05be-450b-4462-898d-b85a660286fc)

2.2 Lets go ahead with rabbit user. There are some exutable file teaParty

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/c13d2ca1-4733-4b7e-b927-d816d3469571)

Lets run it

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/d9e6e5ae-7199-458f-8221-fb0441a11b6c)

Hm. Interesting result. I downloaded this file to my local machine for further analyze.







alice:HowDothTheLittleCrocodileImproveHisShiningTail
/usr/local/lib
/usr/local/lib/x86_64-linux-gnu
/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu

[+] Capabilities
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#capabilities
/usr/bin/perl5.26.1 = cap_setuid+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/perl = cap_setuid+ep

writable
 /dev/shm


