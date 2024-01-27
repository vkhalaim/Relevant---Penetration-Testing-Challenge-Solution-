# Empline TryHackMe Room Write-Up

## Task 1: Host enumeration

Added IP to /etc/hosts.

1.1 Looking for open ports `nmap -sC -sV empline.thm`. Found 3 open ports: 
![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/fd6d70f8-5877-4729-9c0c-f82ca5a3fa7f)

1.2 Looking for pages using dirb `dirb http://empline.thm`. No interesting pages found.

1.3 Navigate to the web application and find the vhost link associated with the job portal `job.empline.thm` and add this one to /etc/hosts
![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/b3bbf9b0-a372-454e-b81f-e56552a43778)

