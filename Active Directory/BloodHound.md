# Installation in 3 steps
## Windows

### 1. Install Java

https://www.oracle.com/java/technologies/javase-jdk11-downloads.html

```powershell-session
.\jdk-11.0.17_windows-x64_bin.exe /s
```


### 2. Install Neo4j

https://neo4j.com/download-center/#community

```powershell-session
Expand-Archive .\neo4j-community-4.4.16-windows.zip .
```

```powershell-session
.\neo4j-community-4.4.16\bin\neo4j.bat install-service
```

```powershell-session
PS C:\htb> net start neo4j
The Neo4j Graph Database - neo4j service is starting..
The Neo4j Graph Database - neo4j service was started successfully.
```
### 3. Install BloodHound

[https://github.com/BloodHoundAD/BloodHound/releases](https://github.com/BloodHoundAD/BloodHound/releases)

## Linux

### 1. Install Java

Updating APT sources to install Java

```shell-session
0xRh4ps00dy@htb[/htb]# echo "deb http://httpredir.debian.org/debian stretch-backports main" | sudo tee -a /etc/apt/sources.list.d/stretch-backports.list
0xRh4ps00dy@htb[/htb]# sudo apt-get update
```
### 2. Install Neo4j

Updating APT sources to install Neo4j

```shell-session
0xRh4ps00dy@htb[/htb]$ wget -O - https://debian.neo4j.com/neotechnology.gpg.key | sudo apt-key add -
0xRh4ps00dy@htb[/htb]$ echo 'deb https://debian.neo4j.com stable 4.4' | sudo tee -a /etc/apt/sources.list.d/neo4j.list
0xRh4ps00dy@htb[/htb]$ sudo apt-get update
```
### 3. Install BloodHound



| Command                                                                     | Description                            |
| --------------------------------------------------------------------------- | -------------------------------------- |
| `.\SharpHound.exe -c all --zipfilename inlanefreight_bloodhound`            | Run the SharpHound C# ingestor         |
| `Invoke-BloodHound -CollectionMethod all -ZipFileName ilfreight_bloodhound` | Run the SharpHound PowerShell ingestor |
| `bloodhound-python -dc <DC> -gc <GC> -d <DOMAIN -c All -u <user>`           | Run bloodhound-python                  |
