# Sherlock Holmes CTF: The Watchman's Residue

<h2>Prompt:</h2>
Holmes receives a breadcrumb from Dr. Nicole Vale - fragments from a string of cyber incidents across Cogwork-1. Each lead ends the same way: a digital calling card signed JM.
<br>
<br>
<h2>Difficulty</h2>
MEDIUM
<br>
<br>



<h2>Question 1</h2>
<h3>What was the IP address of the decommissioned machine used by the attacker to start a chat session with MSP-HELPDESK-AI? (IPv4 address)</h3>
<br>
<br>
We opened msp-helpdesk-ai day 5982 section 5 traffic.pcapng in Wireshark
<br>
<br>
filtered HTTP traffic with http && (http.request.uri contains "/chat" || http.host contains "helpdesk"), and inspected the results.
<br>
<br>
From that filter we identified two source IP addresses that stood out as the decommissioned machine used to start the chat.
<br>
<br>
10.0.69.45 is the answer for question 1
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 1.png">
<h2>Question 2</h2>
<h3>What was the hostname of the decommissioned machine? (string)</h3>
<br>
<br>
We filtered the same pcap in Wireshark with nbns and found a NetBIOS query showing the name WATSON-ALPHA-2<1>.
<br>
<br>
The packet’s source IP matches the IP you found earlier (10.0.69.45), confirming it’s the decommissioned machine.
<br>
<br>
WATSON-ALPHA-2 is the answer to question 2
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 1.png">
<h2>Question 3</h2>
<h3>What was the first message the attacker sent to the AI chatbot? (string)</h3>
<br>
<br>
I filtered the same pcap for packets from source IP 10.0.69.45 and inspected the first matching packet.
<br>
<br>
In the packet’s payload Wireshark decoded application/json and under the Member: content field the string value is "Hello Old Friend".
<br>
<br>
Hello Old Friend. is the answer to question 3
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 3.png">
<h2>Question 4</h2>
<h3>When did the attacker's prompt injection attack make MSP-HELPDESK-AI leak remote management tool info? (YYYY-MM-DD HH:MM:SS)</h3>
<br>
<br>
I filtered packets from the attacker and found their prompt-injection message sent at 2025-08-19 08:01:58 (EDT).
<br>
<br>
I then filtered traffic to the victim (ip.dst == 10.0.69.45) and followed the TCP stream for packet 2530; the MSP-HELPDESK-AI returned the leaked RMM credentials at 2025-08-19 12:02:06 (UTC timestamp converted to the requested format).
<br>
<br>
2025-08-19 12:02:06 is the answer for question 4
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 4.1.png">
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 4.2.png">
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 4.3.png">
<h2>Question 5</h2>
<h3>What is the Remote management tool Device ID and password? (IDwithoutspace:Password)</h3>
<br>
<br>
I inspected the AI’s response to the attacker’s prompt-injection and found the RMM credentials embedded in the walkthrough.
<br>
<br>
The AI listed RMM ID: 565 963 039 and Password: CogWork_Central_97&65.
<br>
<br>
565963039:CogWork_Central_97&65 is the answer to question 5
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 5.png">
<h2>Question 6</h2>
<h3>What was the last message the attacker sent to MSP-HELPDESK-AI? (string)</h3>
<br>
<br>
I filtered HTTP messages from the attacker with http.request.uri contains "/api/messages/send" && ip.src == 10.0.69.45
<br>
<Br>
opened the last matching packet, and inspected the Member: content JSON field
<br>
<br>
The String value in that field is JM WILL BE BACK, which is the attacker’s final message.
<br>
<Br>
JM WILL BE BACK is the answer to question 6
<br>
<Br>
<img src="The_Watchman's_Residue_Images/Question 6.png">
<h2>Question 7</h2>
<h3>When did the attacker remotely access Cogwork Central Workstation? (YYYY-MM-DD HH:MM:SS)</h3>
<br>
<Br>
I checked for SYNs from the attacker after the AI leaked RMM creds (after 2025-08-19 08:02:06 EDT) but found no successful connections in the pcap.
<br>
<Br>
Then I inspected the local files and opened Program Files\TeamViewer\Connections_incoming.txt
<br>
<Br>
This shows an incoming connection from James Moriarty (JM) at 2025-08-20 09:58:25.
<br>
<Br>
2025-08-20 09:58:25 is the answer to question 7
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 7.png">
<h2>Question 8</h2>
<h3>What was the RMM Account name used by the attacker? (string)</h3>
<br>
<Br>
I opened Program Files\TeamViewer\Connections_incoming.txt (the same file used to find the incoming time) and inspected the listed incoming connections.
<bR>
<Br>
The entry shows the RMM account name James Moriarty, which matches the attacker’s "JM" signature.
<br>
<Br>
James Moriarty is the answer to question 8
<br>
<Br>
<img src="The_Watchman's_Residue_Images/Question 8.png">
<h2>Question 9</h2>
<h3>What was the machine's internal IP address from which the attacker connected? (IPv4 address)</h3>
<br>
<br>
I opened TeamViewer15_Logfile.log and searched for 192.168.* entries
<br>
<br>
Several addresses appeared, but 192.168.69.213 is logged as a UDP punch-in around the attacker’s connection time
<br>
<Br>
192.168.69.213 is the asnwer to question 9
<br>
<Br>
<img src="The_Watchman's_Residue_Images/Question 9.png">
<h2>Question 10</h2>
<h3>The attacker brought some tools to the compromised workstation to achieve its objectives. Under which path were these tools staged? (C:\FOLDER\PATH)</h3>
<br>
<br>
I searched TeamViewer15_Logfile.log for "James Moriarty" and inspected his session actions
<br>
<Br>
The log shows multiple Write file operations creating executables (e.g., JM.exe) in C:\Windows\Temp\safe
<br>
<br>
C:\Windows\Temp\safe\ is the answer to question 10
<bR>
<br>
<img src="The_Watchman's_Residue_Images/Question 10.png">
<h2>Question 11</h2>
<h3>Among the tools that the attacker staged was a browser credential harvesting tool. Find out how long it ran before it was closed? (Answer in milliseconds) (number)</h3>
<br>
<br>
I loaded the Cogwork_Admin NTUSER.DAT into Registry Explorer, rebuilt the user hive
<br>
<Br>
Checked the Focus Time entry for WebBrowserPassView.exe
<br>
<br>
The focus time is 08 s, which converts to 8000 ms
<bR>
<BR>
8000 is the answer to question 11
<br>
<Br>
<img src="The_Watchman's_Residue_Images/Question 11.png">
<h2>Question 12</h2>
<h3>The attacker executed a OS Credential dumping tool on the system. When was the tool executed? (YYYY-MM-DD HH:MM:SS)</h3>
<bR>
<br>
I parsed the $J (USN Journal) with Eric Zimmerman's mftcmd into CSV and opened it in VSCode
<bR>
<br>
Searching for MIMIKATZ showed creation and execution entries
<br>
<br>
The execution timestamp is 2025-08-20 10:07:08.
<br>
<br>
2025-08-20 10:07:08 is the answer to question 12
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 12.png">
<h2>Question 13</h2>
<h3>The attacker exfiltrated multiple sensitive files. When did the exfiltration start? (YYYY-MM-DD HH:MM:SS)</h3>
<br>
<br>
I searched TeamViewer15_Logfile.log for C:\Windows\Temp\ and found a Send file entry marking the start of exfiltration with timestamp 2025/08/20 11:12:07
<bR>
<br>
Subtracting one hour to convert to the workstation’s local time gives 2025-08-20 10:12:07
<br>
<Br>
2025-08-20 10:12:07 is the answer to question 13
<h2>Question 14</h2>
<h3>Before exfiltration, several files were moved to the staged folder. When was the Heisen-9 facility backup database moved to the staged folder for exfiltration? (YYYY-MM-DD HH:MM:SS)</h3>
<bR>
<Br>
I searched the parsed $J CSV for Heisen-9 and found entries showing the backup database was moved at 2025-08-20 10:11:09.
<br>
<Br>
2025-08-20 10:11:09 is the answer to question 14
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 14.png">
<h2>Question 15</h2>
<h3>When did the attacker access and read a txt file, which was probably the output of one of the tools they brought, due to the naming convention of the file? (YYYY-MM-DD HH:MM:SS)</h3>
<br>
<Br>
I searched the parsed $J CSV for dump.txt and found an access/read entry for that file at 2025-08-20 10:08:06
<br>
<br>
2025-08-20 10:08:06 is the answer to question 15
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 15.png">
<h2>Question 16</h2>
<h3>The attacker created a persistence mechanism on the workstation. When was the persistence setup? (YYYY-MM-DD HH:MM:SS)</h3>
<bR>
<br>
I checked the SOFTWARE hive (C:\Windows\System32\config\SOFTWARE) and found the Winlogon Userinit value modified to include an extra path to JM.exe
<br>
<br>
This registers JM.exe to run at user login
<br>
<Br>
The modification timestamp matches the attack timeframe.
<br>
<Br>
2025-08-20 10:13:57 is the answer to question 16
<br>
<Br>
<img src="The_Watchman's_Residue_Images/Question 16.png">
<h2>Question 17</h2>
<h3>What is the MITRE ID of the persistence subtechnique? (Txxxx.xxx)</h3>
<bR>
<br>
To find this persistence technique's MITRE ATT&CK ID, I just looked it up on Google
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 17.png">
<bR>
<br>
<h2>Question 18</h2>
<h3>When did the malicious RMM session end? (YYYY-MM-DD HH:MM:SS)</h3>
<br>
<br>
I checked Connections_incoming.txt in C:\Program Files\TeamViewer and found an entry showing the Heisen-9 database was moved at 2025-08-20 10:14:27
<bR>
<br>
2025-08-20 10:14:27 is the answer to question 18
<bR>
<br>
<img src="The_Watchman's_Residue_Images/Question 8.png">
<h2>Question 19</h2>
<h3>The attacker found a password from exfiltrated files, allowing him to move laterally further into CogWork-1 infrastructure. What are the credentials for Heisen-9-WS-6? (user:password)</h3>
<br>
<br>
I converted the acquired file (critical).kdbx to a hash
<br>
<Br>
Cracked it with Hashcat
<br>
<br>
Then recovered the master password cutiepie14
<bR>
<br>
Opening the KeePass database revealed the Werni entry with password Quantum1!
<br>
<br>
Werni:Quantum1! is teh answer to question 19
<br>
<br>
<img src="The_Watchman's_Residue_Images/Question 19.png">
