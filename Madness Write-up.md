# TryHackMe Madness Room Write-Up

## Task 1: Enumeration

### 1.1 Find open ports

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/c083d588-7275-4e60-b32c-973196763787)

Lets proceed with 80 port, since room description says that we should brute ssh :)

### 1.2 Find any info at web

Image logo is brokne and has an interesting name. Lets download it.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/5c0b6c76-d43a-4f84-95a6-064169ac1a9d)

Manipulated a bit with this image and here we are. First magic bytes were for PNG, but actually this is JPG file :)

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/16ef20ea-ab84-4753-8818-227055cfeb65)

Hm. Lets use BurpSuite and try to capture more info

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/cfbceefb-3270-4efb-9a92-649db0b9ddb3)

Created such modified request

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/fa8efc36-315b-4167-8973-0971722d573d)

Find this one 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/43e4caa9-bb94-4539-ade5-3a57089fdfce)

Trying to decode this, but I can't find any. I waisted about an hour and recall that steghide asked for passphrase! 
I used this command `steghide extract -sf try1.jpg `

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/acb31d16-f75d-49d8-ab39-c6d6c674a4b3)

Finally I found username

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/d6ba09e3-b259-4627-a72f-6f5b36771c6c)

But where I can find the password? I waisted a lot of time, but didn't find anything at the machine. Finally I decided to search awailable write-up. And I found that password is at room image!!!.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/786fb011-38ae-4d97-87b0-c7bc936455b3)

And here is password

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/87ad04c4-eb43-48b0-868b-4d62677b84df)

Let's connect via ssh. Another issue and another hint was taken :(

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/e5a21ced-d830-4bca-af53-8fe2811f6f63)

We need to decipher this one 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/3a58b8ba-c1b4-4f1d-b8fb-da0ee259ce8d)

## Task 2: Find the flags

We are in

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/0e329826-d7cf-4fe6-9270-82028ded8930)

First flag is already available

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/caced35f-23dc-4af7-9119-b014b48e9133)

Looking forward for privelege escalation. Found interesting program with SUID bit set `screen4.5.0`. Searched a bit and find a couple of exploits. I used this [one](https://www.exploit-db.com/exploits/41154).

After fun of this script I checked whoami

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/43a3402f-fc57-4b6c-9605-6f38872efeb5)

Root flag

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/d2f62ce3-e7a1-409b-a668-3926a5bd325b)

*Note: This write-up is intended for educational purposes and highlights the steps taken during the Madness [TryHackMe](https://tryhackme.com/room/madness#) room.*
