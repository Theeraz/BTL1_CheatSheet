## SOC Fundamentals

List of common ports.

|Port|Service|Description|
|:---:|:---:|:---:|
|20,21|FTP|File Transfer Protocol used to transfer files b/w systems.|
|22|SSH|Secure Shell Protocol allows users to securely connect to a remote host.|
|23|Telnet|Used before SSH, allows users to connect to a remote host, doesn't offer encryption.|
|25|SMTP|Simple Mail Transfer Protocol used to send emails between servers within the network, or over the internet.|
|53|DNS|Domain Name System converts human-readable domain names to machine-readable IP address.|
|67,68|DHCP|Dynamic Host Configuration Protocol assign IP address-related information to any hosts on the network automatically.|
|80|HTTP|Hypertext Transfer Protocol allows browsers (Chrome, Firefox, etc) to connect to web servers and request contents.|
|443|HTTPS|Hypertext Transfer Protocol Secure is a secure version of HTTP Protocol which allows browsers to securely connect to web servers and request contents.|
|514|Syslog|Syslog server listens for incoming Syslog notifications, transported by UDP packets.|

## Phishing Analysis

### Gathering IOCs

1. **Email Artifacts** :

- [ ] Sending Email Address
- [ ] Subject Line
- [ ] Recipient Email Addresses
- [ ] Sending Server IP & Reverse DNS
- [ ] Reply-To Address
- [ ] Date & Time

2. **Web Artifacts** :

- [ ] Full URLs
- [ ] Domain Names

3. **File Artifacts** :

- [ ] Attachment Name
- [ ] MD5, SHA1, SHA256 Hash Value

### Analyzing Artifacts

1. **Visualization Tools** - [URL2PNG](https://www.url2png.com/), [URLScan](https://urlscan.io/), [AbuseIPDB](https://www.abuseipdb.com/)
2. **URL Reputation Tools** - [VirusTotal](https://www.virustotal.com/gui/), [URLScan](https://urlscan.io/), [URLhaus](https://urlhaus.abuse.ch/), [WannaBrowser](https://www.wannabrowser.net/)
3. **File Reputation Tools** - [VirusTotal](https://www.virustotal.com/gui/), [Talos File Reputation](https://www.talosintelligence.com/talos_file_reputation)
4. **Malware Sandboxing** - [Hybrid Analysis](https://www.hybrid-analysis.com/), [Any.run](https://any.run/), [VirusTotal](https://www.virustotal.com), [Joe Sandbox](https://www.joesandbox.com/)

## Digital Forensics

1. Data Representation can be done in following ways,

- Base64
- Hexadecimal
- Octal
- ASCII
- Binary

2. File Carving :

```bash
scalpel -b -o <output> <disk image file>
```

3. Hashes :

- **Windows** -

By default, `get-filehash` command will generate SHA256 sum of a file,

```powershell
get-filehash <file>
```

To generate MD5 hash of a file,

```powershell
get-filehash -algorithm MD5 <file>
```

To generate SHA1 hash of a file,

```powershell
get-filehash -algorithm SHA1 <file>
```

- **Linux** - 

```bash
md5sum <file>
sha1sum <file>
sha256sum <file>
```

4. Find digital evidence with 
	- **FTK Imager** - Import .img file in FTK imager
	- **KAPE** - Can be used for fast acquisition of data.

5. **Windows Investigations** :

- **LNK Files** - These files can be found at 

```md
C:\Users\$USER$\AppData\Roaming\Microsoft\Windows\Recent
```

- **Prefetch Files** - 
	- **PECmd** - This tool can be used to view the prefetch files. `PECmd.exe -f <path/to/file.pf>`

These files can be found at 
```md
C:\Windows\Prefetch
```

- **Jumplist Files** - These files can be found at

```md
C:\Users\% USERNAME%\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations
C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Windows\Recent\CustomDestinations
```

- **Logon Events**
	- **ID 4624** - successful logons to the system.
	- **ID 4672** - Special Logon events where administrators logs in.
	- **ID 4625** - Failed Logon events.
	- **ID 4634** - Logoffs from the current session.

These event logs can be found at
```md
C:\Windows\System32\winevt\Logs
```

- Capture and view the browser history with 
	- **Browser History Viewer** 
	- **Browser History Capturer**

6. **Linux Investigations** :
	- **/etc/passwd** - contains all information about users in the system. 
	- **/etc/shadow** - contains encrypted passwords
	- **Unshadow** - used to combine the passwd and shadow files.
	- **/var/lib** - In `/var/lib/dpkg/status` location, this file includes a list of all installed software packages.
	- **.bash_history** - contains all the issued commands by the users.
	- **Hidden Files** - isuch files whose name begins with **.**
	- **Clear Files** - files that are accessible through standard means.
	- **Steganography** - practice of concealing messages or information within other non-secret text or data.

7. **Volatility** - 

Find the imageinfo of the file, 

```bash
volatility -f /path/to/file.mem imageinfo
```

List the processes of a system,

```bash
volatility -f /path/to/file.mem --profile=PROFILE pslist
```

View the process listing in tree form,

```bash
volatility -f /path/to/file.mem --profile=PROFILE pstree
```

View command line of the specific process with PID XXXX,

```bash
volatility -f /path/to/file.mem --profile=PROFILE dlllist -p XXXX
```

View Network Connections,

```bash
volatility -f /path/to/file.mem --profile=PROFILE netscan
```

Dumping the process with a specific PID XXXX,

```bash
volatility -f /path/to/file.mem --profile=PROFILE procdump -p XXXX -D /home/ubuntu/Desktop
```

Print all available processes,

```bash
volatility -f memdump.mem --profile=PROFILE psscan
```

Print expected and hidden processes,

```bash
volatility -f memdump.mem --profile=PROFILE psxview
```

Create a timeline of events from the memory image,

```bash
volatility -f memdump.mem --profile=PROFILE timeliner
```

Pull internet browsing history,

```bash
volatility -f memdump.mem --profile=PROFILE iehistory
```

Identify any files on the system from the memory image,

```bash
volatility -f memdump.mem --profile=PROFILE filescan
```

8. **Metadata** - Data about data
	
- **Exiftool** 

```bash
exiftool <file>
```

## Security Information and Event Management

### SPLUNK

Queries must start by referencing the dataset,

```md
index="botsv1"
```

To search for a source IP (src) address with a value of 127.0.0.1,

```md
index="botsv1" src="127.0.0.1"
```

To search for a destination IP (dst) address that this source IP address made a connection with a value of X.X.X.X,

```md
index="botsv1" src="127.0.0.1" dst="X.X.X.X"
```

Query to get the count of whatever we need,

```md
index="botsv1" | stats count by signature
```

Query to show all the events containing the term Powershell,

```md
index=windowslogs | search Powershell
```

Query to display the top 7 Image ( representing Processes) captured,

```md
index=windowslogs | top limit=7 Image
```

Query that does the opposite of top command as it returns the least frequent values or bottom 10 results,

```md
index=windowslogs | rare limit=7 Image
```

Query to highlight the three mentioned fields in the RAW logs,

```md
index=windowslogs | highlight User, host, EventID, Image
```

Query to  transform the data into tables or visualizations,

```md
index=windowslogs | chart count by User
```

Query to add or remove mentioned fields from the search results,

```md
index=windowslogs | fields + host + User + SourceIp
```

Query to create a table with three columns selected and ignore all the remaining columns from the display,

```md
index=windowslogs | table EventID Hostname SourceName
```

Query to get the HTTP status code,

```md
index="botsv1" status=200
```

Query to get the type of event,

```md
index="botsv1" event_type=alert
```

Query to get the sysmon source,

```md
index="botsv1" source=="XmlWinEventLog"
```

Query for the Fortigate Alerts counter,

```md
index=* sourcetype=fortigate_utm level=alert | stats count by attack 
```

Query to find the version of the scanning tool,

```md
index=* sourcetype=fortigate_utm level=alert   attack="Acunetix.Web.Vulnerability.Scanner" ref="http://www.fortinet.com/ids/VID39769"

(Then we check the link to fortinet or whatever page is. If we don't find the version there, we should check in other logs, like Suricata and in the search field we write some reference, in this case Acunetix)
```


Query that gets the count of events based on each severity rating in SURICATA and FORTIGATE
```md
index=* sourcetype=suricata event_type=alert | stats count by severity

index=* sourcetype=fortigate_utm level=alert | stats count by severity
```

IMPORTANT NOTE

- You must escape / with \ if required in the given context.
- Do not escape \ itself, unless necessary for another syntax.

```md
\/var\/log\/system\.log
```

Here, the / is escaped (\/), and the . is also escaped (\.) to be interpreted literally.


## Incident Response

1. **Network Analysis** - use Wireshark to import .pcap, .pcapng files.

Most common Wireshark filters:

```md
Filter by source IP: ip.src == 10.251.96.4
Filter traffic by destination IP: ip.dst == 192.168.1.100
Filter by specific port: tcp.port == 80
Filter to find an specific info inside of a packet: frame contains <specific info>
Filter by protocol: dns
Filter by protocol + string (api in this case): dns contains api
Search for HTTP GET requests: http.request.method == "GET"
Filter by files uploaded or downloaded to a page: http.request.method==POST
Filter by tools: ip.dst == 10.251.96.5 && http.user_agent
Filter by suspicious payloads or strings: http contains "cmd"
Filter by specific file extensions or types in HTTP traffic: http.request.uri contains ".exe"
Filter by failed login attempts via HTTP: http.request.method == "POST" && http.host contains "login"
Displays TCP packets with a window size of 8000 bytes or over: tcp.window_size_value >= 8000
Exclude the type of packets you decide (tcp in the next example): not ftp / !udp
```

2. **CMD** : Command prompt can be used to view the valuable information,

To view the network configuration of the system,

```cmd
ipconfig /all
```

To check running processes and programs,

```cmd
tasklist
```

Display running processes and the associated binary file that was executed to create the process,

```cmd
wmic process get description, executablepath
```

To view all number of users in the command prompt

```cmd
net users
```

List all users that are in the administrators user group,

```cmd
net localgroup administrators (we can use just "net localgroup" to see a list of all groups)
```

List all users in RDP group,

```cmd
net localgroup "Remote Desktop Users" (quotes are for groups that include spaces)
```

List all services and detailed information about each one,

```cmd
sc query | more
```

List open ports on a system, which could show the presence of a backdoor,

```cmd
netstat -ab
```

Export a tasklist into a txt,

```cmd
tasklist > tasklist.txt
```

3. **Powershell** - Can also be used often retrieve much more information.

These commands will get network-related information from the system,

```powershell
Get-NetIPConfiguration
Get-NetIPAddress
```

List all local users on the system,

```powershell
Get-LocalUser
```

Provide a specific user to the command to only get information about them,

```powershell
Get-LocalUser -Name BTLO | select *
```

Quickly identify running services on the system in a nice separate window,

```powershell
Get-Service | Where Status -eq "Running" | Out-GridView
```

Group running processes by their priority value,

```powershell
Get-Process | Format-Table -View priority
```

Collect specific information from a service by including the name in the command (-Name ‘namehere’) or the Id, as shown above and below,

```powershell
Get-Process -Id 'idhere' | Select *
```

Scheduled Tasks are often abused and utilized a common persistence technique,

```powershell
Get-ScheduledTask
```

Specify the task, and retrieving all properties for it,

```powershell
Get-ScheduledTask -TaskName 'PutANameHere' | Select *
```

Changing the Execution Policy applied to our user,

```powershell
Set-ExecutionPolicy Bypass -Scope CurrentUser
```

4. **DeepBlueCLI** - PowerShell script that was created by SANS to aid with the investigation and triage of Windows Event logs.

To process log.evtx,

```powershell
./DeepBlue.ps1 log.evtx
```

DeepBlue will point at the local system's Security or System event logs directly,

```powershell
# Start the Powershell as Administrator and navigate into the DeepBlueCli tool directory, and run the script

./DeepBlue.ps1 -log security
./DeepBlue.ps1 -log system

IMPORTANT: This would be the command in case the logs are in the same directory as DeepBlueCLi (exactly the same directory).
For example, if the logs are in a previous directory the command would be:

./DeepBlue.ps1 ..\Logname1

# if the script is not running, then we need to bypass the execution policy
Set-ExecutionPolicy Bypass -Scope CurrentUser
```

To look at all of the event logs at once and export it to a text file,

```powershell
./DeepBlue.ps1 .\evtx\* > output.txt
```
