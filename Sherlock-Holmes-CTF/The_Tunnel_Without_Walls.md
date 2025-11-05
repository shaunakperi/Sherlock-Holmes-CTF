# Sherlock Holmes CTF: The Tunnel Without Walls

<h2>Prompt:</h2>
A memory dump from a connected Linux machine reveals covert network connections, fake services, and unusual redirects. Holmes investigates further to uncover how the attacker is manipulating the entire network!
<br>
<br>
<h2>Difficulty</h2>
HARD
<br>
<br>
<h2>Question 1:</h2>
<h3>What is the Linux kernel version of the provided image? (string)</h3>
<br>
<br>
I started with a file called memdump.mem (4,294,436,992 bytes).
<br>
<br>
I went to my Volatility3 folder and made a copy of the file called memdump_work.mem so I wouldn’t change the original.
<br>
<Br>
Then I ran the Volatility3 banners plugin to find the Linux kernel version.
<bR>
<br>
It showed: Linux version 5.10.0-35-amd64.
<br>
<br>
5.10.0-35-amd64 is teh answer to question 1
<br>
<br>
<img src="The_Tunnel_Without_Walls/Question 1.png">

<h2>Question 2</h2>
<h3>The attacker connected over SSH and executed initial reconnaissance commands. What is the PID of the shell they used?</h3>
<br>
<br>
We need the PID of the shell the attacker used after connecting over SSH to run their first recon commands.
<br>
<br>
After figuring out which profile was needed, I downloaded the correct symbol file, put it in Volatility3’s symbols folder, and extracted the .json file.
<br>
<Br>
Then I ran the Linux.pstree plugin to view the process tree. In the output, I saw that bash with PID 13608 was a child of an SSH chain:
sshd(13585) -> sshd(13607) -> bash(13608)
<br>
<br>
PID 13608 is the attacker’s shell process.
<br>
<br>
13608 is the answer to question 2
<br>
<br>
<img src="The_Tunnel_Without_Walls/Question 2.png">

<h2>Question 3</h2>
<h3> After the initial information gathering, the attacker authenticated as a different user to escalate privileges. Identify and submit that user's credentials.</h3>
<br>
<Br>
I first used the linux.bash plugin to list executed commands and look for anything related to credentials.
<br>
<br>
I found this command: su jm, meaning the attacker tried to switch to the jm user.
<bR>
<Br>
I ran strings on memory and found this line:
jm:$1$jm$poAH2RyJp8ZllyUvIkxxd0:0:0:root:/root:/bin/bash
<br>
<br>
That string contains the password hash
<br>
<br>
Because it starts with $1$, it uses MD5-crypt. I then used hashcat to crack the hash, and it successfully resolved to:
WATSON0
<br>
<br>
jm:WATSON0 is the answer to question 3
<h2>Question 4</h2>
<h3>The attacker downloaded and executed code from Pastebin to install a rootkit. What is the full path of the malicious file?</h3>
<br>
<Br>
Since we know a rootkit was installed, I ran the linux.malware.check_modules plugin to look for suspicious kernel modules.
<br>
<Br>
The scan showed a suspicious module named Nullincrevenge, which matches the NULLINC name 
<br>
<Br>
I used the linux.pagecache.Files plugin to find where this malicious module was stored on disk. The output showed the file path of the rootkit:
<br>
<Br>
/usr/lib/modules/5.10.0-35-amd64/kernel/lib/Nullincrevenge.ko is the answer to question 4
<h2>Question 5</h2>
<h3>What is the email account of the alleged author of the malicious file?</h3>
<br>
<br>
We can try to extract it from memory using the linux.pagecache.inodePages plugin.
<br>
<br>
After dumping the inode pages, I ran strings on the output and found the "author" information embedded in the file, which included an email address.
<br>
<BR>
i-am-the@network.now is the answer to question 5
<h2>Question 6</h2>
<h3>The next step in the attack involved issuing commands to modify the network settings and installing a new package. What is the name and PID of the package?</h3>
<br>
<BR>
From the linux.bash plugin output, we see that the attacker installed the package dnsmasq.
<br>
<br>
Using that, we searched the previously saved /tmppstree.txt file for lines containing apt, dpkg, or dnsmasq.
<br>
<br>
In the results, there was a process tree entry for dnsmasq, which showed its PID as 38687.
<br>
<bR>
dnsmasq,38687 is the answer to question 6
<h2>Question 7</h2>
<h3>Clearly, the attacker's goal is to impersonate the entire network. One workstation was already tricked and got its new malicious network configuration. What is the workstation's hostname?</h3>
<br>
<br>
Using the linux.bash plugin and the iptables configuration, we can tell the LAN IP range is 192.168.211.0/24.
<br>
<br>
With that range in mind, we ran strings on the memdump file and used grep to search for IP-related data.
<bR>
<br>
From the results, we were able to see the hostname of the machine that was tricked into using the malicious network configuration.
<br>
<br>
Parallax-5-WS-3 is the answer to question 7
<h2>Question 8</h2>
<h3>After receiving the new malicious network configuration, the user accessed the City of CogWork-1 internal portal from this workstation. What is their username?</h3>
<br>
<Br>
I used strings again, but this time combined it with egrep and searched for things like user= and username= in URL parameters, since we know the user logged into the internal portal.
<br>
<br>
After scrolling through a lot of results, I eventually found a line that showed both a username and password field.
<br>
<Br>
From that, we learned the username of the person who accessed the City of CogWork-1 internal portal:
<bR>
<br>
mike.sullivan is the answer to question 8
<br>
<br>
<img src="The_Tunnel_Without_Walls/Question 8.png">
<h2>Question 9</h2>
<h3>Finally, the user updated the software to the latest version, as suggested on the internal portal, and fell victim to a supply chain attack. From which Web endpoint was the update downloaded?</h3>
<br>
<br>
We reused the previous search we did for Flag 7, which looked through memory using the LAN IP range.
<br>
<Br>
In that output, we found the web endpoint where the malicious update was downloaded: it pointed to AetherDesk.exe being pulled from a specific path.
<br>
<Br>
/win10/update/CogSoftware/AetherDesk-v74-77.exe is teh answer to question 9
<h2>Question 10</h2>
<h3>To perform this attack, the attacker redirected the original update domain to a malicious one. Identify the original domain and the final redirect IP address and port.</h3>
<br>
<br>
To find the original domain name, I reused the same search query from the previous flag.
From that output, I saw the domain updates.cogwork-1.net.
<br>
<Br>
To confirm, I extracted and checked the contents of /etc/dnsmasq.conf, which also listed updates.cogwork-1.net as the domain.
<br>
<Br>
I saw that the attacker opened, edited, and deleted /tmp/default.conf. I then recovered this file from memory and opened it (for example, with nano) to read the final IP:port value: 13.62.49.86:7477.
<br>
<br>
Combining the original domain and the redirect target gives us the final flag:
<br>
<br>
updates.cogwork-1.net,13.62.49.86:7477 is teh answer to question 10
