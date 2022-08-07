# Archetype Walkthrough

## Recommended OS - ParrotOS or Pwnbox

## Tools Used
- `openvpn`
- `nano`
- `nmap`
- `smbclient`
- `impacket`
   - `mssqlclient.py`


## Start
Making sure that you are connected to the HackTheBox vpn using:

   ```sudo openvpn starting_point_<username>.ovpn```

or you're in an open pwnbox window - connect to the machine and copy the target machine IP address. 

Rather than having to reopen the browser window or traversing through old terminal commands. It is ideal to create a separate working directory for the specific box or task to avoid any confusion for example if you have created a starting_point directory and this is the first box of **Tier 2**, make a new directory for boxes at this level:

```
mkdir tier2
cd tier2
   
mkdir archetype
cd archetype
pwd
```

So the present working directory obtained using `pwd` above should give: 

``` 
/home/<your_username>/starting_point/tier2/archetype/
```

It is good practice to store the target IP in a text file using:

```
nano target_ip.txt
```

So that each time we need to access the target IP we can just open a new terminal window from the same working directory and use:
   
```
cat target_ip.txt
```

Note - if we need to close the box for any reason and reopen it later - ensure that the first time we're starting the box again we copy the IP and store it in the `target_ip.txt` file in the appropriate file directory.

### Task 1: Which TCP port is hosting a database server? 

- [x] **Perform an nmap scan**

Since we're doing a bit more than just checking to see how many ports are open, it is worth using two flags (-sC and -sV) on the NMAP scan in an open terminal window:

```
nmap -sC -sV <target_IP_address>
```

The flags of nmap are used for the following reasons:

``` 
-sV: Used to check the version of each service offered by the open ports.
-sC: Enable scripts from NSE, the Nmap Scripting Engine. 
```

NB: The `-sC` flag is useful later on during privelege escalation.


- [X] **Check to see which ports are open**

The summary from Nmap should look something like:

```
   Nmap scan report for <target_IP>
   Host is up (0.26s latency).
   Not shown: 996 closed tcp ports (conn-refused)
   PORT     STATE SERVICE      VERSION
   135/tcp  open  msrpc        Microsoft Windows RPC
   139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
   445/tcp  open  microsoft-ds Windows Server 2019 Standard 17763 microsoft-ds
   1433/tcp open  ms-sql-s     Microsoft SQL Server 2017 14.00.1000.00; RTM
   | ms-sql-ntlm-info: 
   |   Target_Name: ARCHETYPE
   |   NetBIOS_Domain_Name: ARCHETYPE
   |   NetBIOS_Computer_Name: ARCHETYPE
   |   DNS_Domain_Name: Archetype
   |   DNS_Computer_Name: Archetype
   |_  Product_Version: 10.0.17763
   | ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
   | Not valid before: 2022-08-07T06:04:27
   |_Not valid after:  2052-08-07T06:04:27
   |_ssl-date: 2022-08-07T06:08:58+00:00; +1s from scanner time.
   Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

   Host script results:
   | smb-security-mode: 
   |   account_used: guest
   |   authentication_level: user
   |   challenge_response: supported
   |_  message_signing: disabled (dangerous, but default)
   | smb2-time: 
   |   date: 2022-08-07T06:08:46
   |_  start_date: N/A
   | smb-os-discovery: 
   |   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
   |   Computer name: Archetype
   |   NetBIOS computer name: ARCHETYPE\x00
   |   Workgroup: WORKGROUP\x00
   |_  System time: 2022-08-06T23:08:43-07:00
   | ms-sql-info: 
   |   10.129.51.144:1433: 
   |     Version: 
   |       name: Microsoft SQL Server 2017 RTM
   |       number: 14.00.1000.00
   |       Product: Microsoft SQL Server 2017
   |       Service pack level: RTM
   |       Post-SP patches applied: false
   |_    TCP port: 1433
   | smb2-security-mode: 
   |   3.1.1: 
   |_    Message signing enabled but not required
   |_clock-skew: mean: 1h24m01s, deviation: 3h07m50s, median: 0s

   Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
   Nmap done: 1 IP address (1 host up) scanned in 56.82 seconds
```


- [X] **Store the port containing a name with SQL or DB in it**

Notice that **TCP Port 1433** contains an SQL server on it, specifically Microsoft SQL Server 2017 14.00.1000.00; RTM.

- [X] **Submit the port number on the browser window**

We can now submit the port number to the HackTheBox interface.

### Task 2: What is the name of the non-Administrative share available over SMB?

- [X] **Enumerate over SMB ports**

Since we discovered in _Task 1_ that there are open SMB ports and that port 1433 contains an SQL server, we can enumerate using the `smbclient` tool with flags `-N` and `-L`:

```
smbclient -N -L \\\\<target_IP>\\
```

Where 

- `-N` is the "no password" flag (meaning no password is parsed when accessing the SMB server)
- `-L` is the flag that displays the services available on the server (even if they aren't immediately accessible)

- [X] **List all Sharenames**

The `smbclient` terminal command used above produces the following output

```
	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	backups         Disk      
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
SMB1 disabled -- no workgroup available
```

Where we can see that there are 3 priveleged Sharename addresses: `ADMIN$`, `C$` and `IPC$`, whilst there's only one non-administrator share.

- [X] **List the Non-administrator Sharename**

From above we can see that the non-admin sharename is `backups`, so the answer for task two is **backups**.

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
