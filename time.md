<script src="https://www.hackthebox.eu/badge/36120"></script>

<h2>Time - HackTheBox - WriteUp</h2>


<h1>Steps</h1>

1° step: Nmap scan\n
2° step: Dirsearch (nothing interesting found)\n
3° step: Enumeration\n
3° step: Test a vector in the web application\n
4° step: Getting a shell\n
5° step: Privilege escalation\n


<h1>Nmap</h1>
![image](https://i.imgur.com/nNgEXkK.png)
We found two ports: 22 and 80.

While I was enumerating the webserver, I left running dirsearch.
![image](https://imgur.com/iFMN0Fv)
Nothing interesting found.


In the webserver, there's a "JSON Beautifier and Validator"
Testing "Beautify" option, nothing curious was found.
![image](https://imgur.com/oXxi1e0)

But, if we change to "Validate(Beta!)" option, we see something interesting.
![image](https://imgur.com/Ya1EBHq)
An error, that's awesome! Let's take a look for it.

That's the complete error:
![image](https://imgur.com/nGsqPqX)
I marked an interesting factor, let's research it.


After some research, I found and read some articles talking about "Jackson Deserialization" and a "RCE" in this library.
I tried to send '{"FDivision"}' and the error changed:
![image](https://imgur.com/TMCdfOe)

[Github exploit](https://github.com/jas502n/CVE-2019-12384)
