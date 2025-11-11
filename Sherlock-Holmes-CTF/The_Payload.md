# Sherlock Holmes CTF: The Card

<h2>Prompt:</h2>
With the malware extracted, Holmes inspects its logic. The strain spreads silently across the entire network. Its goal? Not destruction-but something more persistent…friends. NOTE: The downloaded file is active malware. Take the necessary precautions when attempting this challenge.
<br>
<br>
<h2>Difficulty</h2>
HARD
<br>
<br>
<h2>Question 1</h2>
<h3>During execution, the malware initializes the COM library on its main thread. Based on the imported functions, which DLL is responsible for providing this functionality? (filename.ext)</h3>
<bR>
<br>
After extracting The_Payload.zip, I found three files inside: AetherDesk-v74-77.exe, AetherDesk-v74-77.pdb, and DANGER.txt.
<br>
<Br>
Opening DANGER.txt reveals a note addressed to the player, warning about the risks associated with the artifacts in this challenge.
<br>
<Br>
For the first question, we need to identify which DLL is being used, so I opened AetherDesk-v74-77.exe in CFF Explorer and checked the Import Directory.
<br>
<Br>
From there, we can see that ole32.dll is the DLL responsible for initializing the COM library on the main thread.
<br>
<br>
ole32.dll is teh answer to question 1
<h2>Question 2</h2>
<h3>Which GUID is used by the binary to instantiate the object containing the data and code for execution? (----)</h3>
<bR>
<br>
We’ll be using a tool called Ghidra, a reverse engineering framework that’s great for inspecting binary files
<br>
<BR>
After opening AetherDesk-v74-77.exe in Ghidra and navigating to the decompiled view of the main function, we can locate the GUID that’s being instantiated.
<br>
<br>
Identified GUID: dabcd999-1234-4567-89ab-1234567890ff
<bR>
<br>
dabcd999-1234-4567-89ab-1234567890ff is teh answer to question 2
<h2>Question 3</h2>
<h3>Which .NET framework feature is the attacker using to bridge calls between a managed .NET class and an unmanaged native binary? (string)</h3>
<br>
<br>
In the code, we can see calls to CoCreateInstance, OleRun, and QueryLibrary.
<bR>
<Br>
Looking into these functions shows that they all belong to the COM library.
<br>
<br>
A quick search about how the COM library enables calls between .NET and unmanaged code points us to the concept of COM Interop.
<br>
<br>
Interop is the answer to question 3
<h2>Question 4</h2>
<h3>Which Opcode in the disassembly is responsible for calling the first function from the managed code? (** ** **)</h3>
<br>
<bR>
While reviewing the decompiled AetherDesk-v74-77.exe file, we can spot the .NET call in the line
(**(code **) (*local_208 + 0x68))(local_208, &local_210);.
<br>
<br>
This line represents the first function call into the managed code.
<br>
<Br>
When we select this line, Ghidra shows that it corresponds to the opcode sequence: ff 50 68.
<br>
<Br>
ff 50 68 is the answer to question 4
<h2>Question 5</h2>
<h3> Identify the multiplication and addition constants used by the binary's key generation algorithm for decryption. (*, **h)</h3>
<br>
<br>
This flag requires us to extract hexadecimal values, as indicated by the (*, **h) format, where "h" specifies hex.
<br>
<br>
While examining the main function, we find a line of decryption logic:
local_1f8._Buf[(longlong)pIVar12] = (char)pIVar12 * '\a' + 'B';
<br>
<Br>
From this, we can see that \a and B are the key components used in the binary’s key generation algorithm.
By writing a short Python snippet to convert these ASCII characters to hexadecimal, we obtain:

\a → 0x7

B → 0x42
<bR>
<br>
7, 42h is the answer to question 5
<h2>Question 6</h2>
<h3>Which Opcode in the disassembly is responsible for calling the decryption logic from the managed code? (** ** **)</h3>
<br>
<Br>
This question is pretty straightforward, and the decryption logic is found in the main function.
<bR>
<br>
ff 50 58 is teh answer to question 6
<h2>Question 7</h2>
<h3>Which Win32 API is being utilized by the binary to resolve the killswitch domain name? (string)</h3>
<bR>
<br>
Just a few lines above the previous flag’s answer, there’s a block of code that references an interesting string.
<bR>
<br>
This section of the binary appears to overwrite a structure resembling an array of COM IUnknown entries, and then places a BSTR pointer where a vtable pointer would normally go.
<bR>
<Br>
Looking at the imported functions, we can see three relevant ones: WSAStartup, WSACleanup, and getaddrinfo.
<br>
<br>
getaddrinfo is teh answer to question 7
<h2>Question 8</h2>
<h3>Which network-related API does the binary use to gather details about each shared resource on a server? (string)</h3>
<bR>
<br>
Toward the end of the main function, there’s a call to a function named ScanAndSpread().
<bR>
<br>
When we double-click into ScanAndSpread() to view its decompiled code, we see it uses the Windows API NetShareEnum, which retrieves information about each shared resource on a server.
<br>
<Br>
Further inspection shows that this function is part of the Netapi32.dll library.
<Br>
<bR>
NetShareEnum is the answer to question 8.
<h2>Question 9</h2>
<h3>Which Opcode is responsible for running the encrypted payload? (** ** **)</h3>
<bR>
<br>
In the same ScanAndSpread() function as before, we can see an encrypted blob in the code.
Tracing where it’s used, a few lines below there’s a function call tied to it.
<bR>
<br>
When we hover over that call, the disassembly shows the corresponding opcode, which gives us the value we need:
<bR>
<br>
ff 50 60 is the answer to question 9
<h2>Question 10</h2>
<h3>Find → Block → Flag: Identify the killswitch domain, spawn the Docker to block it, and claim the flag. (HTB{_****************})</h3>
<bR>
<Br>
We’ve already obtained the encrypted kill switch string from Flag 7:
KXgmYHMADxsV8uHiuPPB3w==
<bR>
<br>
The obvious next step is to decrypt it. Since it’s Base64-encoded, we first need to determine the XOR key used. Earlier in the decompiled code, we saw how the keystream is generated in this line:

local_1f8._Buf[(longlong)pIVar12] = (char)pIVar12 * '\a' + 'B';
<bR>
<br>
Using that formula, we can generate a 32-byte XOR keystream with:

(i * 7 + ord('B')) & 0xff

and print it out as a single hex string.
<br>
<Br>
This produces the key:

424950575e656c737a81888f969da4abb2b9c0c7ced5dce3eaf1f8ff060d141b
<bR>
<br>
Using CyberChef, we can build a recipe of From Base64 → XOR, plug in our encrypted string and this hex key, and get the decrypted output:

k1v7-echosim.net
<Br>
<br>
That’s not the final flag yet. After starting the Docker instance for “The Payload,” we browse to the given IP and port, which loads a DNS Management Dashboard. Entering k1v7-echosim.net into the interface triggers a confirmation and a short narrative about Holmes and the SYSTEM, ending on a page that reveals the final flag:
<Br>
<Br>
HTB{Eternal_Companions_Reunited_Again} is the answer to question 10
