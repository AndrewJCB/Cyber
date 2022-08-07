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
   -sC: Enable scripts from NSE, the **N**map **S**cripting **E**ngine. This is useful later on during privelege escalation.
   ```

- [ ] Check to see which ports are open

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
