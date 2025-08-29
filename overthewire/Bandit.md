Level 0:
`ssh bandit0@bandit.labs.overthewire.org -p 2220`
password: bandit0


Level 1:
Password: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If

> Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.

[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html) , [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html) , [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html) , [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html) , [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html) , [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)

Level 2 Password: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx

> if you want to open this type of file you have to specify the full location of the file such as **./-** .For eg. , if you want to see what is in that file use **cat ./-**

Level 2:
to deal with spaces in file names you got to use ""
`cat "File With Spaces`
`cat ./"--file with spaces--"`

Level 3:
File is hidden
I used `ls -all` to find it
then used `cat ...Hiding-From-You`
i also used `cd inhere`

Level 4:

use `reset` if my terminal messes up
i just tried to read every file there is by using `cd ./-file00`

Level 5:

searching file by size `find -size -1033c`
searching `-readable

`find ~/inhere -type f -size 1033c`

it was specified to me that the file is
- readable
- 1033 bytes in size
- not executable

i used the first 2 info into my advantage and utilized `find -readable -size 1033c` to locate the file that matches the first 2 info

i found 1 match and it contained a hash code,

Level 6:

```bash
bandit6@bandit:/$ find . -user bandit7 -group bandit6 -size 33c  2>/dev/null
./var/lib/dpkg/info/bandit7.password
bandit6@bandit:/$ cat ./var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
bandit6@bandit:/$ pwd
/
```
very interesting, i had to go `cd ..` a couple of times to reach the 'root' directory that covers the server, i then used find with specific arguments, used the information provided to my advantage i also had to use `2>/dev/null` to bypass permission stuff

Level 7:
a file i had to search for the word 'millionth'

i used grep command `grep -a "millionth" data.txt
`
and it gave me the password instantly

Level 8:
~~a lot of hash codes, i used a command that's called `uniq` to find a code that's occurred once~~

~~`uniq -1c data.txt`~~ 


```
Save output to a file.

>>

Append output to a file.

<

Read input from a file.

2>

Redirect error messages.

|

Send the output from one program as input to another program.
```

`cat data.txt | sort | uniq -u

> Piping
 So far we've dealt with sending data to and from files. Now we'll take a look at a mechanism for sending data from one program to another. It's called piping and the operator we use is ( | ) (found above the backslash ( \ ) key on most keyboards). What this operator does is feed the output from the program on the left as input to the program on the right.

`bandit8@bandit:~$ sort data.txt | uniq -c1`

Level 9:
The way I found the password was stupid, I used `sort -h data.txt` which is wrong, but somehow managed to find the password.

instead i should have used `string data.txt` to view all human-readable text.
furthermore, i could have combined both grep and strings to get more clearer output
`strings data.txt | grep '=='`

Level 10:

Very easy, didn't take long.
All I used is `base64 data.txt` to decode binary into readable text.

Level 11:
`cat data.txt | tr 'a-z' 'n-za-m'` rotate by 13 however this is wrong, i got a letter wrong
i changed the command to do `cat data.txt | tr 'a-z-A-Z` 'n-za-m-N-ZA-M'

Level 12:
First
`mkdir schizonostradamus | cd /tmp`
`cp data.txt /tmp/schizonostradamu`

Now...
i kept using `file` to check what kind of file im dealing with, every compression method required a different uncompress command, ill write down the commands i used

also since this was a hex dump i had to first reverse the hex using `xxd`

`file data.txt`
`xxd -r data.txt > compressed.bin` 
`file compressed.bin`
it said: gzip
i said:
`mv compressed.bin layer1.gz`
why? because it wasn't a tar file yet, i had to copy it into a `.gz` format so i can use `gunzip`
> one way of checking that the file is ready to be `gunzip`'ed is to `ls` and see if it shows in red

now we can;
`gunzip layer1.gz` 
it give us layer1 in ls after being unzipped using gunzip

i redo `file layer1` to see what kind of file it is now...
surprise: it's a bzip2 now. what do i do?
`mv layer1 layer2.bz2`
`bunzip2 layer2.bz2`

again..
`file layer2`
keep unzipping and unzipping... **WAIT A SECOND, IT'S NOW A POSIX TAR ARCHIVE, WHAT THE HELL!??!??!?!**

easy, your next solution is to `tar -xf filename`
and now it's been unextracted. 

keep going until we reach a readable `cat file` that give us the password.

Level 13:
`cat` the ssh key file

`chmod 600 ssh.key` to set perm that ssh accepts the key to be protected perm wise, this includes the SSH nonsense key
`ssh -i ssh.key hostname@labs.overthewire.org -p 2220`

Level 14:
`nc localhost 30000` to submitting the password to localhost on port 30000.

Level 15:
`openssl s_client -connect localhost:30001`
and then i entered the password

Level 16:

First I did this
`nmap -p 31000-32000 bandit.labs.overthewire.org`

Noted the open ports, tested each one by connecting via SSH
and it gave me keyupdate? now what?

i think it's expecting a key or something

`openssl s_client -connect localhost:31790`

this took me a while to understand but i discovered that if i used openssl it will keep telling me `KEYUPDATE` and put me in a loop

i tried a different approach which was `ncat --ssl localhost 31790` entered the password and it gave me an RSA key.

Sequence for connecting via RSA key:
`echo "RSA_KEY_LONG" >> bandit17_key.pem`
`chmod 600 bandit17_key.pem`
`ssh -i bandit17_key.pem  bandit17@bandit.labs.overthewire.org -p 2220`

> Note: Make sure contents of bandit17_key.pem
   Double-check the key file content

`-----BEGIN RSA PRIVATE KEY-----`
`KEY`
`-----END RSA PRIVATE KEY-----`

Level 17:

i used `diff passwords.old passwords.new`
and it gave me the first key which seems to be the password because it said if it gives you `byebye!` it's related to the next level

also you can confirm by doing `cat passwords.new | grep "password_text"` and it should show it

Level 18:

we gotta bypass the login without triggering `.bashrc` 
so i used a command that when we log in it immediately reads the `readme` file

`ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme`

funny fact: i tried to pass it that way `ssh bandit18@bandit.labs.overthewire.org -p 2220 cat | readme` but it would say that it's on directory because the working directory is still my home kali one not overthewire labs.
Level 19:

`ssh bandit19@bandit.labs.overthewire.org -p 2220`
`./bandit20-do cat /etc/bandit_pass/bandit20`

`./bandit20-do`
This is an **executable file** in your current directory (`./` means "current directory").
It's  a **setuid binary**, which means it runs with the **permissions of its owner**, not the user running it.
In this case, the file is **owned by `bandit20`**, so when you (as `bandit19`) run it, the command it executes runs **as `bandit20`**.

`cat`
This is the **command being passed** to the `bandit20-do` binary.
Display contents.

`/etc/bandit_pass/bandit20`
This is the file that stores the **password for the next level**, `bandit20`.
As `bandit19`, you **cannot read** this file directly—it’s readable only by `bandit20`.

 So What Happens?
`bandit20-do` receives the command `cat /etc/bandit_pass/bandit20`.
It executes this command **as the user `bandit20`**, thanks to its setuid bit.
As a result, you're able to read a file you normally couldn’t access.

Why It’s Secure
This setup lets you do exactly **one privileged action** (run a command as `bandit20`), without giving you full control of that account. It’s a controlled privilege escalation used for the challenge.


Level 20:

first by doing `ls -l` we find that we have `suconnect` in our directory, it's a stuid file, and it's not actually a normal file, it's an executable one. it's a program.

i discover that `suconnect` is used for and i quote
> This program will connect to the given port on localhost using TCP. If it receives the correct password from the other side, the next password is transmitted back.

it's also owned by `bandit21`

so, how do we send a password?

first i gotta run this command in the background before executing suconnect
`echo -n '0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO' | nc -l -p 12345`
`-n` avoid new line

`-l` listen mode, wait for incoming connections
`-p 12345` specify the port number to listen on

then you can start the program `./suconnect 12345` or the port you chose for that task.

Why It Works:
- `suconnect` is a **setuid** program that runs with the permissions of `bandit21`.
- It opens a **TCP connection** to your locally running server on the specified port.
- It **reads** your provided password, matches it to the current level, and if correct, **sends back** the password for `bandit21`.

Level 21:
*im croning it*

cronjobs are located at **/etc/cron.d/**
all i did was `ls -l` to see the crons, then 
`cat /etc/cron.d/cronjob_bandit22`
output: `* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null`

i accessed the given output file path to read cronjob_bandit22.sh
`cat /usr/bin/cronjob_bandit22.sh`
and i got my password `cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv`

don't forget to `cat /tmp/blablabla`

Level 22:
`whoami` bandit22

`ls -l /etc/cron.d` to see what cronjobs are there
`cat /etc/cron.d/cronjob_bandit23` we find the job we're looking for
`cat /usr/bin/cronjob_bandit23.sh` let's see the contents of the job

```shell
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

what the script does?
- **Gets current username** with `whoami` (which is `bandit22` when cron runs).
- Creates a **hash** from the string: `"I am user bandit23"` using `md5sum`.
- **Copies** the password file:
Your job is to **calculate the same md5 hash** the script does, so you know **which `/tmp` file to read**.

`echo "I am user bandit23" | md5sum | cut -d ' ' -f 1`
`cat /tmp/8ca319486bfbbc3663ea0fbe81326349`

Level 23:
`bandit23@bandit:~$ ls -l /etc/cron.d`
`cat /etc/cron.d/cronjob_bandit24`

```shell
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

`cd /var/spool/bandit24/foo`

`echo '#!/bin/bash
`cat /etc/bandit_pass/bandit24 > /tmp/bandit24_pass_for_bandit23' > myscript.sh`

`chmod +x myscript.sh`

wait for it to execute...

then `cat /tmp/bandit24_pass_for_bandit23`

Level 24:
i would need to create a bash script
`mktemp -d`

`nano script.sh`
```shell
#!/bin/bash

# echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8";

for pin in $(seq -w 0000 9999); do
   echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 "$pin;
done; 
```

then we execute this command and use `netcat`
`./script.sh | nc localhost 30002 > results.txt`
make sure to give the shell script permissions `chmod +x script.sh`

Level 25:
