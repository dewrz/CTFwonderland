# CTFwonderland

This CTF focused on the compromising of a Linux-based system. It was a bit more involved than the previous CTF I had posted here. While completing this task, I did reference <a href="https://book.hacktricks.xyz/linux-hardening/privilege-escalation">HackTricks.xyz</a> (which is a treasure trove of information) and <a href="https://gtfobins.github.io/">GTFOBins</a>. I use both of those sites quite often to help guide me in my process. I often download Linpeas to the machine I'm attacking for efficiency when searching for angles that I can exploit to gain privilege escalation, but recently I've been trying to approach this by manually searching. It takes much longer, but because of my novice pentesting skills, I believe by doing this manually it will help bolster those skills through repetition and solidify my understanding of those approaches. 

We begin by running an NMAP scan of the network to identify if there are any vulnerable assets with the scan returning open ports on port 22 and 80 with a web server.
<br>
<br>
<a href="https://imgur.com/JDLsA1G"><img src="https://i.imgur.com/JDLsA1G.jpg" title="source: imgur.com" /></a>
<br>
<br>
I then ran a directory search with Gobuster to further enumerate the webserver. Gobuster revealed a few directories, /poem revealed nothing in particular, but the directory /r, led to a string of other directories spelling out “rabbit”. 
<br>
<br>
<a href="https://imgur.com/JDGap4O"><img src="https://i.imgur.com/JDGap4O.jpg" title="source: imgur.com" /></a>
<br>
<br>
<a href="https://imgur.com/TM8QZIw"><img src="https://i.imgur.com/TM8QZIw.jpg" title="source: imgur.com" /></a>
<br>
<br>
Checking the page source of the /t directory revealed what appears to be a username and password.
<br>
<br>
<a href="https://imgur.com/agycvig"><img src="https://i.imgur.com/agycvig.jpg" title="source: imgur.com" /></a>
<br>
<br>
Since I didn’t find an admin portal on the web server, I tried to login via SSH with the username and password and was authenticated.
<br>
<br>
<a href="https://imgur.com/7YrETWY"><img src="https://i.imgur.com/7YrETWY.jpg" title="source: imgur.com" /></a>
<br>
<br>
I immediately checked the username’s sudo privileges and it will let me run walrus_and_the_carpenter.py as the user rabbit with sudo from Alice's home directory.
<br>
<br>
<a href="https://imgur.com/YzBsyVO"><img src="https://i.imgur.com/YzBsyVO.jpg" title="source: imgur.com" /></a>
<br>
<br>
After looking at the python script I discovered that it uses the import random module. We can take advantage of this by using the Python library hijack technique by creating a counterfeit python library in the same directory to escalate privileges to the user rabbit. We’re able to do this because Python will look in the current directory for the random library first, before searching other directories.
<br>
<br>
<a href="https://imgur.com/10BLGff"><img src="https://i.imgur.com/10BLGff.jpg" title="source: imgur.com" /></a>
<br>
<br>
I created the hoax python library and took advantage of the privilege by running the script as rabbit.
<br>
<br>
<a href="https://imgur.com/FW0wrz6"><img src="https://i.imgur.com/FW0wrz6.jpg" title="source: imgur.com" /></a>
<br>
<br>
<a href="https://imgur.com/O9RWID2"><img src="https://i.imgur.com/O9RWID2.jpg" title="source: imgur.com" /></a>
<br>
<br>
I ran cat on the teaParty file and received back some code/obfuscated, so I then opened the file in nano and it is an executable ELF that is using a date function.
<br>
<br>
<a href="https://imgur.com/rOrMC2s"><img src="https://i.imgur.com/rOrMC2s.jpg" title="source: imgur.com" /></a>
<br>
<br>
We will now exploit the PATH of the date function and then execute the teaParty script to escalate our privilege.
<br>
<br>
<a href="https://imgur.com/s3rf7Bf"><img src="https://i.imgur.com/s3rf7Bf.jpg" title="source: imgur.com" /></a>
<br>
<br>
I changed the directory to hatter and listed the files and there was a file there called password.txt. This is hatter’s password, so I ran sudo –l, checked for shared object hijacking or any cronjobs that could be exploited, but I had no luck. There was a perl capability that can be exploited though.
<br>
<br>
<a href="https://imgur.com/aCEhSwz"><img src="https://i.imgur.com/aCEhSwz.jpg" title="source: imgur.com" /></a>
<br>
<br>
<a href="https://imgur.com/bS0ZswO"><img src="https://i.imgur.com/bS0ZswO.jpg" title="source: imgur.com" /></a>
<br>
<br>
<a href="https://imgur.com/K8pld9n"><img src="https://i.imgur.com/K8pld9n.jpg" title="source: imgur.com" /></a>
<br>
<br>
I referenced GTFOBins and found a command that I can run to exploit the Perl capability. With this, I was able to elevate my privilege to root and retrieve both the user and root flags.
<br>
<br>
<a href="https://imgur.com/WCzEOT1"><img src="https://i.imgur.com/WCzEOT1.jpg" title="source: imgur.com" /></a>
<br>
<br>
<b>Lessons Learned:</b><br>
<br>
I made a bunch of rookie mistakes along the way which increased the amount of time spent on this CTF. One of the mistakes made was not switching users and running on fragmented privilege. When trying to execute the path exploit, it took a few moments of "User does not have permission." for me to figure it out. I also didn't record a password found, so I had to backtrack to retrieve it, costing me time. 


































































