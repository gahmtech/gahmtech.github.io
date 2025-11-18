# The Missing Sysadmin

## Writeup from noob to root

So this is a new CTF, called "The Missing Sysadmin" by Björn Ettelman

<img width="640" height="480" alt="image" src="https://github.com/user-attachments/assets/f41fc666-4139-4704-9742-06e5fe3aaac3" />

So how do i start gathering information?

Let's begin with an basic nmap scan to check for open ports.

`nmap <IP>`

The results shows only three open ports:

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

Visiting the page and inspecting the code shows no clues on how to breach, and there are no /robots.txt available.
So whats on the port 5000? It shows a scoreboard and a endpoint of /submit to add your flags..

Let's crawl the web for hidden files with gobuster

`gobuster dir -x html -u http://10.3.10.xxx/ -w /wordlist-big.txt`

After a while it shows up some results:
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
index.html           (Status: 200) [Size: 5569]
status.html          (Status: 200) [Size: 637]

The index.html is irrelevant, but *status.html* is interresting, lets check it out:
<img width="828" height="153" alt="image" src="https://github.com/user-attachments/assets/7b31cfe2-d8e7-4cd8-a064-8a7bf4e40e43" />

Now it looks like we got an entry-point on ssh? Lets try:

`ssh trainee@10.3.10.xxx` and entering the password 'training123'

There we go, first entrance!

ssh trainee@10.3.10.xxx
Welcome to The Missing Sysadmin!
trainee@10.3.10.xxx's password: 
Linux TheMissingSysadmin 6.12.41+deb13-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.12.41-1 (2025-08-12) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Nov 17 09:06:43 2025 from 10.3.10.xxx
trainee@TheMissingSysadmin:~$ 

`cat flag.txt` reveals the first flag!

but there are also a directory named *notes* with an subfolder *old_logs* containing a lot of old log-files.
Aaah, the webpage mentioned "check old logs"..

Viewing the first one shows a lot of login logs... lets se if we can extract some credentials...

`cat *.log | grep "pass="` to list them all and only show lines containing *pass=*

Looks like a lot of login attempts, a brute force attack?
However, one line sticks out: `login user=webbackup pass=coffe2025`

Let's try to ssh to it: `ssh webbackup@10.3.10.xxx` and entering the password 'coffee2025'

Allright, second entrance!
sudo -l and other common privescs shows nothing interresting.
so there must be something in the tar-archive:

webbackup@TheMissingSysadmin:~$ ls
webserver_backup.tar.gz

Downloading it and extracting reveals a flag.txt and a README.txt
YES! One more flag collected!!

Let's check the README:
cat README.txt
This is an archived snapshot of the old webserver.
You might find credentials in the site database: site.db

An easy way to exctract data from database is to do a strings:

strings site.db
SQLite format 3
tableusersusers
CREATE TABLE users(id INTEGER PRIMARY KEY, username TEXT, password TEXT)
%othernot_this_one
-cryptoletsgetsomemoney

That reveals an user crypto with pass letsgetsomemoney

Let's try to ssh to it: `ssh crypto@10.3.10.xxx` and entering the password 'letsgetsomemoney'





cat secret_message.txt
The sysadmin never trusted password managers...
Username: sysadmin
Password: --removed--

YES! One more collected, now we got 
