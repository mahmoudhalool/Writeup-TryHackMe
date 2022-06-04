# Frank & Herby make an app|Writeup-TryHackMe|medium
**this is a solution for Frank and Herby try again.... room on <font color="&#x1F4D8;">https://tryhackme.com</font>   
**URl: <font color="&#x1F4D8;"> https://tryhackme.com/room/frankandherby </font>

# Task 1 "Frank & Herb Make an App!"
 # 1st question
 The question begins asking us what port with a web application listening is open on the machine.
 to know that we have to  run **rustscan** on the **machine_ip**  then we get a list of open ports 
 write the next line in your terminal to create the scan "rustscan -a machine_ip --ulimit 3000"
 **after we create the scan we found that:** 
````

PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
10250/tcp open  ssl/http Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
10255/tcp open  http     Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
25000/tcp open  ssl/http Gunicorn 19.7.1
31337/tcp open  http     nginx 1.21.3
32000/tcp open  unknown 

````
If we look carefully there is only one port that fits what we need. Alternatively you can visit each of them in the browser until you find the one that connects.
**ans :31337**

 # 2nd question
now we  will use gobuster to discover what other directories there  using the port number from 1st question
```` gobuster -e -u  $machine_ip:31337 -w -w /usr/share/wordlists/dirb/common.txt ````
**the output for that scan is:** 
````
http://machine_ip/.git-credentials
http://machine_ip/assets
http://machine_ip/css
http://machine_ip/index.html
http://machine_ip/vendor

````
after that we will curl on the first ip address:````curl $IP:31337/.git-credentials````
