# Archetype Walkthrough

## Recommended OS - ParrotOS, Kali Linux or Pwnbox

## Tools Used
- `openvpn`
- `nano`
- `nmap`
- `smbclient`
- `impacket`
   - `mssqlclient.py`


## Start: Setting up the host VM or using Pwnbox for the currnet box

If you're in an open Pwnbox window - connect to the machine and copy the target machine IP address. 

Regardless of whether we use Pwnbox or, it is easy to create (or traverse into) a `starting_point` directory, and it also makes sense to move the `starting_point_<username>.ovpn` file from the downloads folder to keep directories clean: 

If the `starting_point` directory doesn't exist:

```
mkdir starting_point
```
Then move the `.ovpn` file using:

```
cd Downloads
mv starting_point_<username>.ovpn /home/<home_user>/HTB/starting_point/starting_point_<username>.ovpn
```
- NB: `home_user` is the name of the user on your VM. If running Kali from root the prefix of the filepath will just be `ROOT` unless otherwise specified. 

The next step is to traverse into the `starting_point` directory and start the vpn:

```
cd 
cd HTB/starting_point
sudo openvpn starting_point_<username>.ovpn
```

Rather than having to reopen the browser window or traversing through old terminal commands, it is ideal to create a separate working directory for the specific box or task to avoid any confusion for example if you have created a `starting_point` directory and this is the first box of **Tier 2**, make a new directory for boxes at this level:

```
mkdir tier2
cd tier2
   
mkdir archetype
cd archetype
pwd
```

So the present working directory obtained using `pwd` above should give: 

``` 
/home/<home_user>/HTB/starting_point/tier2/archetype/
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

Since we're doing a bit more than just checking to see how many ports are open, it is worth using two flags (`-sC` and `-sV`) on the NMAP scan in an open terminal window:

```
nmap -sC -sV <target_IP_address>
```

The flags of nmap are used for the following reasons:
 
-`-sV`: Used to check the version of each service offered by the open ports.
-`sC`: Enable scripts from NSE, the Nmap Scripting Engine. 


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

Notice that TCP Port 1433 contains an SQL server on it, specifically Microsoft SQL Server 2017 14.00.1000.00; RTM.

- [X] **Submit the port number on the browser window**

We can now submit the port number **1433** to the HackTheBox interface.

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

- [X] **List the non-administrator sharename(s)**

From above we can see that the non-admin sharename is `backups`. 

- [X] **Submit the non-administrator share to HackTheBox**

The answer for task two is **backups** since there is only one non-admin share present after enumeration.

### Task 3:  What is the password identified in the file on the SMB share? 

- [X] **Check the backups share for files to use for privelege escalation**

Now that we know the non-admin share is `backups`, we can add this to the end of the smbclient terminal command using only the `-N` flag:

```
smbclient -N \\\\<target_ip>\\backups
```

Which enables access to the SMB server and should give the following output:

```
Try "help" to get a list of possible commands.
smb: \> 
```

So we now have access to the smb server - time to escalate priveleges by exploiting any files with sensitive information in them. To do so we just list the files in the current directory using `dir` as it is a windows based server:

```
smb: \> dir
  .                                   D        0  Mon Jan 20 23:20:57 2020
  ..                                  D        0  Mon Jan 20 23:20:57 2020
  prod.dtsConfig                     AR      609  Mon Jan 20 23:23:02 2020

		5056511 blocks of size 4096. 2623696 blocks available
```


- [X] **Download and look at the contents of the file(s) necessary for privelege escalation**

**Hmm!** So we can see can see a file that contains some version of "Config" in its name and no other files in the current working directory. The easiest way to do more analysis of this is to download the `.dtsConfig` file using a get request: 

```
smb: \> get prod.dtsConfig
getting file \prod.dtsConfig of size 609 as prod.dtsConfig (0.3 KiloBytes/sec) (average 0.3 KiloBytes/sec)
```

This downloads the `.dtsConfig` file to the user directory on the local machine (however when doing many boxes this can get untidy). We really want that file in the `archetype` folder. If we check the directory using `pwd` we should be in the `/home/<home_user>/` directory, if not just change the working directory and move the file using:

```
cd
mv prod.dtsConfig /home/<home_user>/HTB/starting_point/tier2/archetype/prod.dtsConfig
```

We can then check the file contents by first ensuring that we are in the correct working directory using `pwd` and then printing to terminal using `cat`:

```
pwd
cat prod.dtsConfig
```

Which should print the following to the terminal window:

```
<DTSConfiguration>
    <DTSConfigurationHeading>
        <DTSConfigurationFileInfo GeneratedBy="..." GeneratedFromPackageName="..." GeneratedFromPackageID="..." GeneratedDate="20.1.2019 10:01:34"/>
    </DTSConfigurationHeading>
    <Configuration ConfiguredType="Property" Path="\Package.Connections[Destination].Properties[ConnectionString]" ValueType="String">
        <ConfiguredValue>Data Source=.;Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc;Initial Catalog=Catalog;Provider=SQLNCLI10.1;Persist Security Info=True;Auto Translate=False;</ConfiguredValue>
    </Configuration>
</DTSConfiguration>
```

We can see both `User ID=ARCHETYPE\sql_svc` and `Password=M3g4c0rp123`

- [X] **Submit the password from the file**

Let's submit the password M3g4c0rp123 to HackTheBox!

### Task 4: What script from Impacket collection can be used in order to establish an authenticated connection to a Microsoft SQL Server? 

- [X] **Check Impacket documentation to look for references to connecting to a Microsoft SQL Server**

If we open the impacket github repo available [here](https://github.com/SecureAuthCorp/impacket), we see that there is an mssqlclient.py file in the examples folder.

- [ ] **Clone and install impacket from the github repo**



- [ ] Submit the name of the collection used  

### Task 5

- [ ]

### Task 6

- [ ]

### Task 7

- [ ]
