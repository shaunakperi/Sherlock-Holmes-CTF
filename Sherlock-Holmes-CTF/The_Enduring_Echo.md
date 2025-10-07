# Sherlock Holmes CTF: The Enduring Echo

<h2>Prompt:</h2>
LeStrade passes a disk image artifacts to Watson. It's one of the identified breach points, now showing abnormal CPU activity and anomalies in process logs.
<br>
<br>
<h2>Difficulty</h2>
EASY
<br>
<br>
<h2>Question 1:</h2>
<h3>What was the first (non cd) command executed by the attacker on the host? (string)</h3>
<br>
<br>
To find this flag, we have to look through the winevt logs and check Security.evtx for attacker commands.
<br>
<br>
We filtered Event Viewer by Event ID 4688 (Process Creation) to find command executions.
<br>
<br>
We limited results to logs from the attacker’s computer, Heisen-9-WS-6.
<br>
<br>
Found a relevant log on 8/24/2025 at 6:51:09 PM showing the first command of the session.
<br>
<br>
The Process Command Line value showed the executed command: systeminfo.
<br>
<br>
systeminfo is the answer to question 1.
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 1.png">


<h2>Question 2</h2>
<h3>Which parent process (full path) spawned the attacker’s commands? (C:\FOLDER\PATH\FILE.ext)</h3>
<br>
<br>
We used Event Viewer Find with obvious parent-process keywords to spot attacker activity.
<br>
<br>
The search term wmi succeeded because WMIPrvSE.exe can run remote code without dropping files.
<br>
<br>
Found a log entry on 8/20/2025 12:48:05 PM showing the process command line.
<br>
<br>
The Process Command Line value is C:\Windows\system32\wbem\wmiprvse.exe.
<br>
<br>
C:\Windows\system32\wbem\wmiprvse.exe is the answer to question 2.
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 2.png">



<h2>Question 3</h2>
<h3>Which remote-execution tool was most likely used for the attack? (filename.ext)</h3>
<br>
<br>
We know from the previous question that the attacker was using WmiPrvSE.exe as their parent process of suspicious commands.
<br>
<br>
WmiPrvSE.exe is found within the wmiexec module, which is run or called from the wmiexec.py script.
<br>
<br>
wmiexec.py is the answer to question 3.
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 3.png">

<h2>Question 4</h2>
<h3>What was the attacker’s IP address? (IPv4 address)</h3>
<br>
<br>
Search Event Viewer for Event ID 4624 (an account was successfully logged on).
<br>
<br>
Use the Flag 1 timestamp 8/24/2025 6:51:09 PM as the starting point.
<br>
<br>
Scan roughly 30 minutes after that time for relevant logon events.
<br>
<br>
Open the matching 4624 entry and check Network Information → Source Network Address.
<br>
<br>
The listed IPv4 address that aligns with the attack timeline is the attacker’s IP.
<br>
<br>
10.129.242.110 is the answer to question 4
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 4.png">



<h2>Question 5</h2>
<h3>What is the first element in the attacker's sequence of persistence mechanisms? (string)</h3>
<br>
<br>
The folder path is The_Enduring_Echo\C\Windows\System32\Tasks.
<br>
<br>
This folder contains scheduled task definitions, often used for persistence.
<br>
<br>
Identified SysHelper Update as the only non-Microsoft-related task.
<br>
<br>
Opened Security.evtx and filtered by Event ID 4688 (Process Creation).
<br>
<br>
Used Find to search for "SysHelper Update" within the filtered logs
<br>
<br>
Found a log showing creation of a task named SysHelper Update that runs as SYSTEM every 4 minutes and redirects output to an admin share.
<br>
<br>
SysHelper Update is the answer to question 5
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 5.png">

<h2>Question 6</h2>
<h3> Identify the script executed by the persistence mechanism. (C:\FOLDER\PATH\FILE.ext)</h3>
<br>
<br>
The answer lies in the log from the previous question.
<br>
<br>
Check the Process Command Line value in the expanded event — it lists the script filename and full path.
<br>
<br>
C:\Users\Werni\Appdata\Local\JM.ps1 is the answer to question 6
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 6.png">



<h2>Question 7</h2>
<h3>What local account did the attacker create? (string)</h3>
<br>
<br>
Filter Security.evtx by Event ID 4720.
<br>
<br>
Event ID 4720 indicates that “A user account was created.”
<br>
<br>
Only one log entry appears with this event ID.
<br>
<br>
svc_netupd is the answer to question 8
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 7.png">



<h2>Question 8</h2>
<h3>What domain name did the attacker use for credential exfiltration? (domain)</h3>
<br>
<br>
Searched extracted files for anything related to credential exfiltration.
<br>
<br>
Found JM.ps1 in The_Enduring_Echo\C\Users\Werni\AppData\Local.
<br>
<br>
Opened JM.ps1 in Notepad to inspect its contents.
<br>
<br>
The domain appears in the parameters of an Invoke-WebRequest call.
<br>
<br>
NapoleonsBlackPearl.htb is the answer to question 8
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 8.png">


<h2>Question 9</h2>
<h3>What password did the attacker's script generate for the newly created user? (string)</h3>
<br>
<br>
JM.ps1 generates a username and password; the password is Watson_ + the script run timestamp in yyyyMMddHHmmss format.
<br>
<br>
Find when the script ran by locating the user creation event for svc_netupd in Security.evtx.
<br>
<br>
The svc_netupd account was created at 8/24/2025 7:05:09 PM (EST).
<br>
<br>
Converted to 24-hour timestamp: 20250824160509.
<br>
<br>
Watson_20250824160509 is the password to question 9




<h2>Question 10</h2>
<h3>What was the IP address of the internal system the attacker pivoted to? (IPv4 address)</h3>
<br>
<br>
Checked the Administrator user’s .ssh folder at The_Enduring_Echo\C\Users\Administrator\.ssh.
<br>
<br>
Found a known_hosts file containing a hostname, key type, and a base64 public key.
<br>
<br>
Opened known_hosts in Notepad to confirm the IP.
<br>
<br>
192.168.1.101 is the answer to question 10
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 10.png">


<h2>Question 11</h2>
<h3>Which TCP port on the victim was forwarded to enable the pivot? (port 0-65565)</h3>
<br>
<br>
Inspect the command that forwarded incoming connections to the pivot IP to find the forwarded TCP port.
<br>
<br>
The attacker ran: netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=9999 connectaddress=192.168.1.101 connectport=22.
<br>
<br>
The listenport parameter shows which local TCP port was forwarded.
<br>
<br>
The forwarded TCP port is 9999.
<br>
<br>
9999 is the answer to question 11
<br>
<br>



<h2>Question 12</h2>
<h3>What is the full registry path that stores persistent IPv4→IPv4 TCP listener-to-target mappings? (HKLM......)</h3>
<br>
<br>
The SYSTEM hive shows HKLM\SYSTEM\ControlSet001\Services\PortProxy\v4tov4\tcp.
<br>
<br>
That ControlSet path is a snapshot and not the live configuration.
<br>
<br>
The HKLM\SYSTEM\Select key holds the Current value that points to the live control set.
<br>
<br>
Resolve the live path using CurrentControlSet instead of ControlSet001.
<br>
<br>
Live registry path: HKLM\SYSTEM\CurrentControlSet\Services\PortProxy\v4tov4\tcp.
<br>
<br>
HKLM\SYSTEM\CurrentControlSet\Services\PortProxy\v4tov4\tcp is the answer to question 12



<h2>Question 13</h2>
<h3>What is the MITRE ATT&CK ID associated with the previous technique used by the attacker to pivot to the internal system? (Txxxx.xxx)</h3>
<br>
<br>
Searched Google for the PortProxy v4tov4\tcp technique to find its MITRE ATT&CK mapping.
<br>
<br>
The MITRE ATT&CK site appeared in the search results.
<br>
<br>
The site identifies the technique as the Internal Proxy sub-technique of Proxy.
<br>
<br>
T1090.001 is the answer to question 13




<h2>Question 14</h2>
<h3>Before the attack, the administrator configured Windows to capture command line details in the event logs. What command did they run to achieve this? (command)</h3>
<br>
<br>
Search Security.evtx for Event ID 4719 ("System audit policy was changed").
<br>
<br>
Only one 4719 entry exists and it occurred before the attack — this is where the admin enabled command-line auditing.
<br>
<br>
The 4719 event itself doesn't show the raw command used to change the policy.
<br>
<br>
Check ConsoleHost_history.txt at The_Enduring_Echo\C\Users\Administrator\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline for historical admin commands.
<br>
<br>
Line 37 contains a policy-related command (reg add ... LocalAccountTokenFilterPolicy) showing admin activity.
<br>
<br>
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit" /v ProcessCreationIncludeCmdLine_Enabled /t REG_DWORD /d 1 is the answer to question 14
<br>
<br>
<img src="The_Enduring_Echo_Images/Question 14.png">

