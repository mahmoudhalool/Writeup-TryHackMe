# Frank & Herby make an app|Writeup-TryHackMe|medium
**this is a solution for Frank and Herby try again.... room on <font color="&#x1F4D8;">https://tryhackme.com</font>   
**URl: <font color="&#x1F4D8;"> https://tryhackme.com/room/frankandherby </font>

# Task 1 "Frank & Herb Make an App!"
 The question begins with asking us what port with a web application listening is open on the machine.
 to know that we have to  run **rustscan** on the machine ip  then we get a list of open ports 
````
```
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
10250/tcp open  ssl/http Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
10255/tcp open  http     Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
25000/tcp open  ssl/http Gunicorn 19.7.1
31337/tcp open  http     nginx 1.21.3
32000/tcp open  unknown 
```
````
