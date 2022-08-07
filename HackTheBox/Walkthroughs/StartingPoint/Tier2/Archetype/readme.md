# Archetype Walkthrough

## Recommended OS - ParrotOS or Pwnbox

## Tools Used
- nmap
- smbclient
- impacket
   - mssqlclient.py


## Start
Making sure that you are connected to the HackTheBox vpn using:

   ```sudo openvpn starting_point_<username>.ovpn```

or you're in an open pwnbox window - connect to the machine and copy the target machine IP address. I like to store the target IP in a text file using:

   ```nano target_ip.txt```

So that each time we need to access the target IP I can just open a new terminal window and use
   
   ```cat target_ip.txt```
   
Rather than having to reopen the browser window or traversing through old terminal commands. I also usually create a working directory for the specific box or task. Note - if we need to close the box for any reason and reopen it later - ensure that the first time we're starting the box again we copy the IP and store it in the **target_ip.txt** file.

### Task 1: Which TCP port is hosting a database server? 

- [x] Perform an nmap scan

Since we're doing a bit more than just checking to see how many ports are open, it is worth using two flags (-sC and -sV) on the NMAP scan in an open terminal window:

   ```nmap -sC -sV <target_IP_address>```

The flags of nmap are used for the following reasons:

   ``` 
   -sV: Used to check the version of each service offered by the open ports.
   -sC: Enable scripts from NSE, the Nmap Scripting Engine. 
   
   The -sC flag is useful later on during privelege escalation.
   ```

- [ ] Check to see which ports are open

The summary from Nmap should look something like:

```
Nmap scan report for <target_ip>
Host is up (0.28s latency).
Not shown: 971 closed tcp ports (conn-refused)
PORT      STATE    SERVICE         VERSION
7/tcp     filtered echo
135/tcp   open     tcpwrapped
139/tcp   open     tcpwrapped
416/tcp   filtered silverplatter
445/tcp   open     tcpwrapped
1051/tcp  filtered optima-vnet
1088/tcp  filtered cplscrambler-al
1198/tcp  filtered cajo-discovery
1433/tcp  open     tcpwrapped
1947/tcp  filtered sentinelsrm
2001/tcp  filtered dc
2005/tcp  filtered deslogin
3914/tcp  filtered listcrt-port-2
4002/tcp  filtered mlchat-proxy
4129/tcp  filtered nuauth
4550/tcp  filtered gds-adppiw-db
5904/tcp  filtered unknown
6001/tcp  filtered X11:1
7443/tcp  filtered oracleas-https
7921/tcp  filtered unknown
8200/tcp  filtered trivnet1
8443/tcp  filtered https-alt
9040/tcp  filtered tor-trans
9207/tcp  filtered wap-vcal-s
9290/tcp  filtered unknown
16993/tcp filtered amt-soap-https
49156/tcp filtered unknown
61532/tcp filtered unknown
63331/tcp filtered unknown

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 177.12 seconds
```

- [ ] Store the port containing a name with SQL or DB in it

- [ ] Submit the port number on the browser window
### Task 2

- [ ]

### Task 3

- [ ]

### Task 4

- [ ]

### Task 5

- [ ]

### Task 6

- [ ]

### Task 7

- [ ]
