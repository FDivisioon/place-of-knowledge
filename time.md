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
An error, that's awesome! Let's take a look for it.

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

First, we need to create a "inject.sql", you just need to change your IP and PORT.
||teste||
