Level 0: To pass this level you read the readme file with cat.

Level 1: To read a file that’s a dash (-) you simply specify its location in cat, such as cat ./-

Level 2: This level is meant to teach you how to deal with files that have spaces in their names, same as before except you could add double quote to read it (cat ./”file with name spaces”) 

Level 3: In order to see the hidden file, you would need to add a parameter to your list (ls) command and that would be (ls -l) and then you can read it with cat.

Level 4: For this level, I tried reading each file until I got a readable one. Though there may have been other options since this could mess up your terminal, I did it because there aren’t that many files to go through anyway.

Level 5: Here you are introduced to the “find” command, you are provided hints such as file size, it’s readable human text so you type in (find -readable -size -1033c) and it would list a bunch of files that match, the first one is the best match.

Level 6: Here you get more in-depth with the “find” command, in my case I used it to discover all files that are readable by group Bandit6, so I typed into my terminal (find . -user bandit7 -group bandit6 -size 33c 2>/dev/null) and I added the 2>/dev/null to discard any standard error outputs (this way I can only see the file I need and for ever file I cannot read should be discarded from the output) and you would receive a file path, use cat and you have your next password.

Level 7: We are introduced to the command “grep” here, to search for a specific string (like a keyword) in a text file. We got the hint that the password is on the same line as the word “millionth” so you would type in (grep “millionth” data.txt) 

Level 8: In this level, we learn about our first piping methods, we are also introduced to a couple of new commands “sort” and “uniq” . These commands are useful to sort text files, especially big ones to retrieve flags or lines of text that don't repeat twice. (cat data.txt | sort | uniq -u)

Level 9: For this level, we will be using a new command “strings” the purpose behind it is to retrieve readable or printable text that can be read by humans, combined with “grep” you should be able to grab the password. (strings data.txt | grep “==”) 

Level 10: This level teaches us about encoding and decoding text. You can decode and encode text using the “base64” command, I used it to decode the given text and got the password (base64 -d data.txt)

Level 11: Here we shift towards cryptography, where a text is encrypted by a rotation of 13 letters. By using the command “tr” we can rotate and reverse the encrypted text (decryption) and retrieve a readable text for the password. (cat data.txt | tr ‘a-z-A-Z’ ‘n-za-m-N-ZA-M’)

Level 12: This level is simple but takes some time to decompress files. You are dealing with hexdump, zipped files (gzip, bzip, tar) and would need to constantly check the file type and try to unzip it. The process is as follows: (1) Check file type (file) command. (2) De-compress the file or reverse the hexdump (gunzip, bunzip2, xxd -r, tar -xf). (3) Repeat the process until you have a readable text file with a password.

Level 13: We are provided an RSA key. Keys can be used to establish SSH connections and are by far a safer alternative to passwords as long as they are kept secure and private. You can take the key and place it on your Linux desktop, furthermore, you would need to change its permissions (chmod 600 ssh.key) and to connect to the next level you would type (ssh -i ssh.key bandit14@labs.overthewire.org -p 2220)

Level 14: Reaching this level, we enter the world of networking. We use the “nc” command short for Netcat to read and write data across network connections using TCP and UDP protocols. In our case here, we are tasked to submit the password over the port 3000 and the server would transmit back the next password if the current one is correct. (nc localhost 3000) and type in the current password.

Level 15: More on key exchange and networking, we are introduced to “OpenSSL” a powerful utility used for security operations such as private key exchange, generating public and private keys, certificate operations and more… You establish a secure connection over the port 30001 using the command and submit the password.

Level 16: In this level, we are tasked to discover open ports ranging from 3100-3200, for this we’re going to be using the Nmap utility. You’d discover a few open ports. Note them down, then try to establish a secure connection using Netcat (nc), then submit the current password of the level and it should transmit an RSA key that can be used to access the next level. (nmap localhost -p3100-3200) (ncat –ssl localhost 31790)

Level 17: Much simpler level, we rotate back to dealing with text files. In this case, we use the command “diff” to retrieve and output the difference between two text files. As hinted in the level, the new password exists in the newpassword text file. To confirm this, we can use cat and grep just to be sure. (diff passwordold.txt passwordnew.txt) and (cat passwordnew.txt | grep “PASSWORD HERE”)

Level 18: If you try to log into this level, it prints “Byebye!” and close the connection, the reason for this is that we are dealing with a modified .basharc and we would have to start thinking of a way to bypass it. I realized after conducting research that you could send commands over the SSH connection that would be executed on the spot before the connection shuts down. If you type in (ssh bandit18@labs.overthewire.org -p 2220 ls) notice the “ls”, it should output a readme file. If you try reading it the same way, you would have the next level password. We basically bypassed a restriction by telling our SSH connection to execute a command as soon as it connects before the connection closes if that makes sense.

Level 19: Upon logging in, we notice a binary executable file called bandit20-do, if you try to run it, it’ll say “Run a command as another user”, so what that means we can run commands as Bandit20 using this program. It’s important to note that Bandit20 has access to his own password in etc/bandit_pass. In summary it’s a restricted privilege escalation, we can use the program to read the next level password (./bandit20-do cat /etc/bandit_pass/bandit20)

Level 20: Back in networking, we have a program called “suconnect”, for this level you would have to start a client and a server, the program would act as a client, run and connect to a specific port. Program would check for the current level password, if it matches, it transmits the next level password. To do this run (echo -n ‘CURRENT PASSWORD’ | nc -l -p 7777) in the background (or 2nd terminal) and (./suconnect 7777) on another.

Level 21: We are introduced to cronjobs and how they can be used for bypassing permissions, cronjobs are tasks that are executed regularly. Could be a shell script that runs every five minutes for example. In our case, we are asked to examine a cronjob for bandit22. If you try to see the shell script it executes, you would discover that it sends the password from /etc/bandit_pass/bandit22 to /tmp/FILENAME directory. If you try to read the file path, you’d get the password for the next level.

Level 22: To understand this level, you would have to research MD5 hashes and how they are generated. It’s a cronjob that converts the text “I am user bandit23” into an MD5 hash, it uses that hash as a name for the file containing the password. We want that filename to access the password, to do that, we replicate the command to create an MD5 hash that would be the exact same one used for the file name containing the password. You can type in the same command used by the cronjob shell script to generate MD5 hash to retrieve it (echo “I am user bandit23” | md5sum | cut -d ‘ ‘ -f 1) outputs an MD5 hash. This is the file name that contains the next level password, type in (cat /tmp/GENERATED_HASH) to read the password.

Level 23: In this level, you create your first shell script. The way it works is that we have a cronjob that runs every shell script that exists inside /var/spool/bandit24/foo and deletes the script afterwards. Cronjobs have elevated permissions and it can read for instance /etc/bandit_pass/bandit24. 

To solve this, you change directory into /var/spool/bandit24/foo.
Create a shell script (nano script.sh)
```shell
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/waseempass
```

Wait for the cronjob to execute the script, then try (cat /tmp/waseempass)


Level 24: To solve this level, you can in theory open a Netcat connection to the daemon listening on port 30002 and constantly try every pincode and submission until you get the next level password, but that would take an insane amount of time. Instead, this is where you need to perform a brute-force attack, creating a shell script that goes through every possibility until you get the correct combination. For this I coded the following script.

```shell
#!/bin/bash

#echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8";

for pin in $(seq -w 0000 9999); do
   echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 "$pin;
done;
```

Then run the command (./script.sh | nc localhost 30002 > results.txt)
Piping the results into a text file is a lot cleaner since it will fill your terminal with wrong combinations until it gets one right.
