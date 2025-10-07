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
<img src="">
