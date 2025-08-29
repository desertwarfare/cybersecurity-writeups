[MrRobot Room Link](https://tryhackme.com/room/mrrobot)

Steps:
**Reconnaissance (Passive and Active)
1. Got an ip address? What is it? `nmap -sV <machine ip>` 
2. It has HTTP/S and SSH ports open? Let's see what's this website about.
3. Maybe there are hidden directories? Follow [[Web Enumeration]] tactics to find out.
4. `robots` seems interesting. 
> What's this? This page is actually used to prevent search engines such as google from indexing sensitive pages such as `wp-admin` and more. 

**Scanning & Enumeration**
First key obtained:
1. Site owner doesn't want us to access `fsocity.dic` and `key`, obviously here's your first key.
2. `fsocity.dic` seems to be a wordlist, maybe I should use this instead of `rockyou.txt` and others...
3. When we conducted web enumeration using tools such as `GoBuster` we find out this page has a `wp-login`
4. But what account should we use? Let's use the wordlist to enumerate user accounts.
5. Before using `hydra` we need to understand how can we craft HTTP POST requests that would abuse the lack of security and therefore we open `burp` and turn on intercept, we send a test login post request and study how the request is sent.

**Login Page Exploitation & Enumeration**
1. Once done, we now can use `hydra -L fsocity.dic -p test <MACHINE IP> http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:Invalid username" -t 30` maybe not exactly but you get the point.
2. We discover the user account `Elliot`, let's use hydra to log into his account. `hydra -l Elliot -P fsocity.dic <MACHINE IP> http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:Incorrect` or whatever that's right. You can also try using `wpscan` to brute force the login, because I had trouble with `hydra`. However neither worked because the machine seemed to be overloaded and I already know using my own kali wouldn't help (still slow).
3. By now we should have the username and the password, log in.

**Second stage Exploitation**
1. It's time to start a reverse shell with the server, there are many ways to do that with WordPress. However, the way I personally did it is by grabbing the pentest monkey's PHP reverse shell code and replaced the `archive.php` page content with it. **(During my cyber training in a firm, I also managed to connect a reverse shell by uploading a malicious plugin that uses php's `exec` function, and in order to not crash the server I added a `?revshell=1` trigger on the url)**
2. Make sure to set the client IP address and the port. For listener, I recommend using `rlwrap nc -lvnp 4444`
3. Access the `archive.php` by going into `<MACHINE IP>/wp-content/themes/twentyfifteen/archive.php` and ensure to have your listener listening, it should then establish a reverse shell connection.

**Privilege Escalation**
1. So, with me being `daemon` or whatever. I started conducting some recon and info gathering while on this user and upon doing some research I found out about the existence of user `robot`, there's also a `robot` directory which the permission are set to read only by robot, I also was able to see it had a key inside and a MD5 hash password. `ls -lsa`
> You can discover that info by using `cat /etc/passwd` and more...
2. Time to crack the hashed password, I grabbed it and tried with `john` using `rockyou` wordlist and it did in fact work.
3. Okay, before changing the user, we need to stabilize our shell. We use this command that I came across before during my training, `python -c 'import pty;pty.spawn("/bin/bash")'`
4. `su robot` to set our user to robot. We use the password we just cracked and boom we're in robot.
5. We can now grab our second key here. However, we're not done yet.
6. We should now search for a user binary that would give us root access. Keyword `nmap`, we  can also use commands such as `find / -perm +6000 2>/dev/null | grep '/bin/'` to search for all binaries that have high privileged access.
7. We discover `/usr/local/bin/nmap`
8. I went over to https://gtfobins.github.io/gtfobins/ and after some research I find out that we can break out of our restricted environment shell by spawning an interactive system shell through nmap. So you type in `nmap --interactive`.
9. You are now **root**.
