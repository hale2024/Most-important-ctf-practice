---
title: "Notes of HTB Machine Labs"
date: "2021-12-28"
author: "RealFakeAccount"
description: "short notes for HTB Machine Lab"
---

# Intro

I usually watch [IppSec's video](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) after finishing or failed on one box.
I would often learn some new things in his excellent video. Therefore, I need a place to store the things I learnt.

This article may contains short write-ups, but they won't be detailed. If a detailed write-up is needed, I would open a new article for it.

## Machine Labs

### Blue

- short write-ups

Box for script kiddie like me. Learn [nmap](https://nmap.org/) and [metasploit](https://www.metasploit.com/) and you should be able to solve it in a few minutes.

- what I learnt

```bash
nmap --script=vuln
```

This nmap command could detect vulnerabilities in server. It may take some time.

### Legacy

- short write-ups

nmap could scan the vulnerabilities. Use metasploit to get a reverse shell.

### Lame

- short write-ups

nmap didn't give vulnerabilities this time. However, both server running on this box (vsftpd 2.3.4 and Samba 3.0.20-Debian) is vulnerable. Either of them is in metasploit.

### Jerry

- short write-ups

Credential guessing box. the status panel's credential is `admin:admin` and the manage panel's credential is the default `tomcat:s3cret`. In the manage panel you can upload a JSP reverse shell.

- what I learnt

1. You could use Hydra to crack the password if you can't guess it.

2. You could use `msfvenom` to generate payload.

3. You can use `exploit/multi/handler` to handle the payload you uploaded.

### Netmon

- short write-ups

Search google and you can find Netmon Remote Code Execution vulnerability and Netmon used to save passwords in plain text.
Also find that the FTP config support anonymous login. Therefore, use FTP to extract the old credientials, guess the new one (note that the config was created in 2019), then use metasploit to get a shell.

- notice

The `programdata` is actually a hided folder. You need to use `ls -a` in order to list this folder.

```bash
ftp> ls
200 PORT command successful.
125 Data connection already open; Transfer starting.
02-03-19  12:18AM                 1024 .rnd
02-25-19  10:15PM       <DIR>          inetpub
07-16-16  09:18AM       <DIR>          PerfLogs
02-25-19  10:56PM       <DIR>          Program Files
02-03-19  12:28AM       <DIR>          Program Files (x86)
02-03-19  08:08AM       <DIR>          Users
02-25-19  11:49PM       <DIR>          Windows
226 Transfer complete.
ftp> ls -a
200 PORT command successful.
125 Data connection already open; Transfer starting.
11-20-16  10:46PM       <DIR>          $RECYCLE.BIN
02-03-19  12:18AM                 1024 .rnd
11-20-16  09:59PM               389408 bootmgr
07-16-16  09:10AM                    1 BOOTNXT
02-03-19  08:05AM       <DIR>          Documents and Settings
02-25-19  10:15PM       <DIR>          inetpub
05-12-21  06:53AM            738197504 pagefile.sys
07-16-16  09:18AM       <DIR>          PerfLogs
02-25-19  10:56PM       <DIR>          Program Files
02-03-19  12:28AM       <DIR>          Program Files (x86)
02-25-19  10:56PM       <DIR>          ProgramData
02-03-19  08:05AM       <DIR>          Recovery
02-03-19  08:04AM       <DIR>          System Volume Information
02-03-19  08:08AM       <DIR>          Users
02-25-19  11:49PM       <DIR>          Windows
226 Transfer complete.
```

- what I learnt

1. Use `grep -B5 -A5 pattern` to quick extract the context.

### Granny

- short write-ups

Experience some strange issues using metasploit 6.0.43. MS14-058 wouldn't work. Will come back later.

- what I learnt

1. local_exploit_suggester in metasploit could suggest exploit.

### Bashed

- short write-ups

Use enumeration (Wfuzz or gobuster, for example) to find the location of the php reverse shell that mentioned in the box's website, then notice that sudo allows you to run as another user, so generate a reverse shell and start bash as the new user. Next, find that the root use crontab to execute the scripts of the new user. Modify the scripts to get a shell as root.

- what I learnt

1. <https://github.com/rebootuser/LinEnum> is a good way to quickly explore interesting file in Linux
2. `-s` option of sudo could start an interaction shell. However, it requires password. Use `sudo -u user /bin/bash` instead.
3. CherryTree is a good way to organize information

### Optimum

- short write-ups

metasploit all the way down.

### ScriptKiddie

Finished. Waiting for retirement.

### Blocky

- short write-ups

gobuster could find a /wiki page and a /plugins page, which indicates the files in /plugins may be important. RE the jar file found in the /plugins gives us a password. Notice that althought this password have a username of `root`, it is actually `notch`'s. Notch got sudo privilege and we got his password, thus we can get root shell using sudo.

- what I learnt

1. Don't dig too deep into one finding. Do a throughout information first.

### Delivery

Finished. Waiting for retirement.

### Devel

- short write-ups

Use ftp anonymous to upload a aspx reverse shell.

- what I learnt

1. Metasploit reverse shell handler sometimes wrongly recognize the system. The reverse shell generated by powershell was recognized as BSD. Strange issue.

### Spectra

Finished. Waiting for retirement.

- what I learnt

1. `chmod +s` makes the file run as the user/group who created it.

### Armageddon

Finished. Waiting for retirement.

### Mirai

- short write-ups

Gobuster can find the admin panel. The login page will inform you that this is a newly installed raspberry-pi. Use the default login credentials to login through ssh and get user flag. Finally use `strings` to find the root flag.

- what I learnt

1. disks could be directly `cat`ed as binary. For example, you could `cat /dev/sda`.

### Shocker

- short write-ups

User: shellshock

Root: user could run perl as root. Use perl to spawn a reverse shell.

- what I learnt

1. `gobuster` has a very strange issue. it won't automatically add / after wordlist. Thus in this machine, you can't get the `/cgi-bin/` dir using `gobuster`. `/cgi-bin/` is 403 while `/cgi-bin` is 404.

2. ShellShock

### Sense

- short write-ups

User: there is a file `system-users.txt` tells you the username. Use the default pfsense password to login. Then use CVE to get reverse shell.

Root: reverse shell runs as root.

- what I learnt

1. if you stucked, buster the dir with common file extension (txt, php, pl, py, etc)

### Beep

- short write-ups

User: the webapp `elastix` had a LFI CVE. Use it to extract the config file. It contains the root passwd for ssh.

Root: reverse shell runs as root.

btw, other ways are possible for this machine. For example, this machine is vulnerable to shellshock.

### Nibbles

- short write-ups

User: find nibbleblog and its admin panel. `guess` the password `nibbles` and login as admin (lol). Use CVE to get shell.

Root: user could run all command as root using sudo.

- what I learnt

1. Often, if HTB wants you to guess passwords, it is usually machine name, `password`, `admin`.

### Buff

- short write-ups

User: The website uses unpatched framework. Use script from searchsploit to get shell.

Root: Find there is a vulnerable service `CloudMe`. Use script from searchsploit to get shell.

- what I learnt

1. if the target don't have ssh and you need a tunnel, `chisel` is a good choice.
    Simple usage: 
    ```bash
    server(attack box): ./chisel server -p 8000 --reverse
    client(victim box): ./chisel client 10.10.14.20:8000 R:port:localhost:port
    ```

2. Use `netstat -an` to see the open ports and use `tasklist /v | findstr <portnumber>` to check the process using the port

3. About `msfvenom` payloads: `windows/meterpreter_reverse_tcp` is a quite large reverse shell, while ` windows/meterpreter/reverse_tcp` contains just enough code to connect back to the attacker.

4. If you want to check the privilege of one process, you can use [accesschk](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk).

### Bank

- short write-ups

Users: set hosts file to `bank.htb` based on info from port 53. use Buster to find `/balance-transfer/`. One file in this folder didn't encrypt. Use the credential in this file to login the panel. the source code of the panel tell us we can upload a php use `.htb` suffix. Upload one and get a user shell.

Root: 2 ways. Either add a new credential in `/etc/passwd`, or use `/var/htb/bin/emergency`, which directly give you a root shell..

- what I learnt

1. In bash, single quote wouldn't inteprete anything while double quote would. So if you want to echo something contain special character ($ for example), use single quote.


2. You can use openssl to [generate hash](https://unix.stackexchange.com/questions/81240/manually-generate-password-for-etc-shadow) for [/etc/shadow](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/). In exploit, `-1` is usually enough (which is a MD5).


3. You can use `find -perm -mode` or `find -perm /mode` to [find files with given permission](https://askubuntu.com/questions/829716/diffrence-between-perm-mode-perm-mode-in-find-command). In this question, use
```bash
find / -type f -user root -perm -4000 2>/dev/null
```
to find the emergency file.

### Celestial

- short write-ups

User: Nodejs deserialization exploit. <https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/>

Root: Notice the `script.py` in /home/sun/Documents is crontabed by root. Modify it to generate a reverse shell from root.


### Valentine

- short write-ups

User: gobuster finds `/dev` folder. You can find a rsa key there encoded in Hex with a username `hype` (since the file name is `hype_key`). Nmap vuln script tells us the website is vulnerable to `heartbleed`. Use scripts from searchsploit and find a base64 string, which after decode is the password of the rsa key. Use `hype` with the rsa key to ssh into the machine.

Root: `.bash_history` is not empty this time. Notice `tmux` is used based on the history. Go to the socket file and connect it to get root access.

- what I learnt

1. You should run `--script vuln` for each box.

### Poison

- short write-ups

User: when selecting `listfiles.php` (suggested by the website), you can see a `pwdbackup.txt`. Select this file instead and find a base64. Decode 13 times to get the password. Then ssh into the machine.

Root: Check the runnning process so you can find `VNC` is running as root. Use ssh to proxy the port and connect to VNC to gain root access.


### Tenten

- short write-ups

 > Comment: Although it seems a bit complicated, the path is pretty straight. 

User: use `wpscan` to find a vulnerable plugin -- `job-manager`. Also find we can enumerate the submitted jobs apply page (http://10.10.10.10/index.php/jobs/apply/_enumerate_/). On the 13th page we can find it has a title called _HackerAccessGranted_. Enumerate this name with the vulnerability we found earlier and gives us a png. Use `steghide` to extract a rsa key, and cracked using `john`. Then login using ssh with this key.

Root: sudo tells us we can run `/bin/fuckin` without password. Check the file and find that "All it does is run the first arg, passing the second, third, and forth args in as args". Therefore, `sudo fuckin /bin/bash` could give us password.

- what I learnt

1. `steghide`

### Luke

- short write-ups

The process is too long for a short write up.

So, <https://0xdf.gitlab.io/2019/09/14/htb-luke.html>

- what I learnt:

1. HTTP OPTION for guessing the API type

2. wfuzz advance usage

### Cronos
- short write-ups

User: nmap tells us port 53 is open. Use `nslookup` to find the base domain `cronos.htb`, and use `dig` to make a `zone transfer`, which tells us `admin.cronos.htb`.

Then is the SQL injection. Strangely `sqlmap` doesn't work. But another tool called [DSSS](https://github.com/stamparm/DSSS) worked. You can also try manually.

In the admin page, you can easily guess there is a command injection. 

Root:

`Linpeas` tells us `/var/www/laravel/artisan` is being executed by root's crontab. Poison it to get root shell.

- what I learnt:

1. [DSSS](https://github.com/stamparm/DSSS)


### Horizontall
- short write-ups

User: fuzz subdomains to find the api page `api-prod.horizontall.htb`. Run a directory fuzz on this page to find the `strapi` page. Google and you should find exploit for this.

Root: Enum the box and find port 8000 is open. Proxy into your VM and find this is Laravel framework. Google and you should find PE exploit.

- what I learnt:

1. Focus on processes and ports in enum.

### Previse
- short write-ups

User: fuzz directory and find some php. Among them, the only one you could visit is `nav.php`, which list a few links that will redirect you to main page. Check the network traffic and find that your browser redirect to main page after receive all the original data. Therefore, intercept the response and modify the HTTP status code from 302 to 200 to visit the forbidden pages.

Then register a new account from a forbidden page. You can download the source of the whole website after login. Read the source code and find the author uses `exec` to generate delimiter in log. Intercept the request and modify the `delim` with your malicious code to get `www-data` shell.

Another information you can find in the source code is the database credentials. Use it to log in the database and get user name password hash. Use `john-the-ripper` and mode `md5-crypt` together with `rockyou` wordlist to crack the hash.

With username `m4lwhere` and the password you cracked, you can ssh as user.

Root: enum and find you can run a certain script with sudo. The key is in the script, the `gzip` and `date` is determined by local PATH rather than using absolute path. Use `path injection` to get root shell.

- what I learnt:

1. path injection as PE vector

2. sometimes server send you the forbidden page, but your browser will redirect to the main page because of the status code. Always check the response size to make sure you don't miss anything.

### Driver

It's surely a bit hard if you don't know the basic of SMB relay attacks.
Before get into the walkthrough, watch this if you don't know anything about SMB relay attacks. 

[Jonathan Brossard &  Hormazd Billimoria, blackhat 2015](https://www.youtube.com/watch?v=a1dgOO9bALA)

- short write-ups

User: First look at port 80. It requires authentication, but it has a weak credential (`admin:admin`). A few guess could get it. 
Also notice that port 5985 is open. Google tells us this could be used by us if we got credentials ([evil-winrm](https://github.com/Hackplayers/evil-winrm)). 
We can phish the credential using SMB. Follow this: [SMB Share – SCF File Attacks](https://pentestlab.blog/2017/12/13/smb-share-scf-file-attacks/)

crack the hash to get credentials, use `evil-winrm` to login.

Root: The hint is very obvious: printer. Use `printernightmare` to get root shell. You can check this blog: [Playing with PrintNightmare](https://0xdf.gitlab.io/2021/07/08/playing-with-printnightmare.html)

- what I learnt:

1. printer nightmare exploit.

2. Although it has been said many times, take notes about what you need and what you can get from one vector.

### Secret

- short write-ups

User: We have a document and source codes of the website. Examine the source code and we can find a RCE in `private.js`. In order to access it, we need to access `secret.htb:3000/logs`, and we need a jwt token to access `/logs`. Normal user's jwt can be generated using register and login function of api, as documented. We can find the admin's name and email in the source code. Now the only thing left is to find the jwt's secret or find a way to bypass it. Sadly the common `None` encryption method does not work here.

We find in `.env` of the source code that the secret is `secret`. However, this does not work. Examine the history version of this file to find the true secret. 
Login using the tamper version of the jwt as `theadmin`, and use RCE to get shell as user.

Root: I'll come back after I have more pwn experience.

- what I learnt:

1. Read `interesting file` section of PEASS line by line.


### Nineveh

- short write-ups

User: fuzz leads us to two interesting pages: `/department` and `/db`. After much tampering with no result, I decided to use hydra to bruteforce, and it actually worked on both page.

In `/department`, a url is particular special: `?notes=files/ninevehNotes.txt`. This seems vulnerable to LFI. First few attempts are not successful. But I notice that you have to include `ninevehNotes`. Therefore use payload `files/ninevehNotes/../../../../../../../etc/passwd` to ensure we can use LFI.

Next part is poisoning. A common way is to poison inside `/db` page. Detailed method show up easily by googling. You can also poisoning by file upload, as state here: <https://insomniasec.com/downloads/publications/LFI%20With%20PHPInfo%20Assistance.pdf>

After you get `www-data`, you can PE to root directly. There's also a way to get user tho. If you string `/var/www/ssl/secure_notes/nineveh.png`, you can find the ssh private key. Notice that the port 22 is not open to public so you need to reverse proxy to your VM.

Root: on `/`, there's a special folder called `/reports`. Google and find this is generated by `chkrootkit`. `chkrootkit` is a PE vector (cve-2014-0476). Use it to get root shell.

- what I learnt:

1. Brute force is your last resort, but remember to use it.

2. Sometimes on LFI point of attack, the victim code will check certain keywords. If you didn't make it work at the first try, maybe try a few more times and try to include original keywords.

### Silo

Weirdly, with instantclient 21.4 and odat 5.1, the file upload is not working.

### Bounty

- short write-ups

User: fuzz leads us to `/transfer.aspx` and `uoloadedFiles`. Based on [Hacktricks](https://book.hacktricks.xyz/pentesting/pentesting-web/iis-internet-information-services#execute-.config-files), we can try to upload .config files and including our asp shell code. 

Upload it with your code and get shell. One thing to note is that the user `Desktop` folder shows nothing when using `ls`. You need to use `ls -Force` to show hidden files.

Root: Notice that there are no hotpatch applied using `systeminfo`. So basically anything would work. I use [juicy potato](https://github.com/ohpe/juicy-potato) here.

- what I learnt:

1. Always check hacktricks is a good habit after enumeration.

2. make better use of `nishang` and `hacktricks`

### SolidState

> Unlike many underrated easy box, this one is an overrated medium box.
> But this box is pretty fun. The emails inside reminds me of this game: <https://store.steampowered.com/app/365450/Hacknet/>

- short write-ups

User: fuzz didn't give us anything. Focus on nmap, we see port 4555 is open, together with some mail server. A bit google allows us to find the [exploit of port 4555 (james)](https://www.exploit-db.com/exploits/50347).

We see the exploit needs ssh login. Sadly, this box doesn't have automatic ssh login.

Continue enum. Find that telnet to Apache James Server with default credentials we can do some change to credentials. Change user's credentials one by one and login to POP3 and see if they got any emails. You can find mindy's credential in her emails.

Login as ssh user, and you find you are limited by rbash. You can either 1. use the exploit above 2. bypass it following [this guide](https://www.exploit-db.com/docs/english/44592-linux-restricted-shell-bypass-guide.pdf).

Root: Run Pspy and find /opt/tmp.py is being executed constantly by root. Modify it to get reverse shell.

- what I learnt:

1. Pspy <https://github.com/DominicBreuker/pspy>

2. The theory of this exploit is amazing! check [this blog from 0xdf](https://0xdf.gitlab.io/2020/04/30/htb-solidstate.html#intended-path). Basically, because there's no path bounds check in James, we can write an email into abritrary folder. The author write malicious email into `/etc/bash_completion.d`, and since files in this folder will be executed whenever user login, we can get our shell after user login.