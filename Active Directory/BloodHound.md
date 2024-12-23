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
net start neo4j
```
### 3. Install BloodHound

[https://github.com/BloodHoundAD/BloodHound/releases](https://github.com/BloodHoundAD/BloodHound/releases)

## Linux

### 1. Install Java

Updating APT sources to install Java

```shell-session
echo "deb http://httpredir.debian.org/debian stretch-backports main" | sudo tee -a /etc/apt/sources.list.d/stretch-backports.list

sudo apt-get update
```

Change Java version to 11

```shell-session
sudo update-alternatives --config java
```
### 2. Install Neo4j

Updating APT sources to install Neo4j

```shell-session
wget -O - https://debian.neo4j.com/neotechnology.gpg.key | sudo apt-key add -

echo 'deb https://debian.neo4j.com stable 4.4' | sudo tee -a /etc/apt/sources.list.d/neo4j.list

sudo apt-get update
```

Installing required packages

```shell-session
sudo apt-get install apt-transport-https
```

Installing Neo4j

```shell-session
sudo apt list -a neo4j 

sudo apt install neo4j=1:4.4.16 -y
```

To start and stop the service, we can use the following commands
#### Start Neo4j

```shell-session
sudo systemctl start neo4j
```
#### Stop Neo4j

```shell-session
sudo systemctl stop neo4j
```

### 3. Install BloodHound

https://github.com/BloodHoundAD/BloodHound/releases

Unzip BloodHound

```shell-session
unzip BloodHound-linux-x64.zip 
```

Execute BloodHound

```shell-session
0xRh4ps00dy@htb[/htb]$ cd BloodHound-linux-x64/
0xRh4ps00dy@htb[/htb]$ ./BloodHound --no-sandbox
```

# Recovering Neo4j Credentials

1. Stop neo4j if it is running

```shell-session
sudo systemctl stop neo4j
```

2. edit `/etc/neo4j/neo4j.conf`, and uncomment `dbms.security.auth_enabled=false`.

3. Start neo4j console:

```shell-session
sudo neo4j console
Directories in use:
home:         /var/lib/neo4j
config:       /etc/neo4j
logs:         /var/log/neo4j
plugins:      /var/lib/neo4j/plugins
import:       /var/lib/neo4j/import
data:         /var/lib/neo4j/data
certificates: /var/lib/neo4j/certificates
licenses:     /var/lib/neo4j/licenses
run:          /var/lib/neo4j/run
Starting Neo4j.
2023-01-05 20:49:46.214+0000 INFO  Starting
...SNIP...
```

4. Navigate to [http://localhost:7474/](http://localhost:7474/) and click `Connect` to log in without credentials.

5. Set a new password for the `neo4j` account with the following query: `ALTER USER neo4j SET PASSWORD 'Password123';`

![text](https://academy.hackthebox.com/storage/modules/69/neo4j_password_recovery1.jpg)

6. Stop neo4j service.
   
7. Edit `/etc/neo4j/neo4j.conf`, and comment out the `dbms.security.auth_enabled=false`.

8. Start Neo4j and use the new password.

| Command                                                                     | Description                            |
| --------------------------------------------------------------------------- | -------------------------------------- |
| `.\SharpHound.exe -c all --zipfilename inlanefreight_bloodhound`            | Run the SharpHound C# ingestor         |
| `Invoke-BloodHound -CollectionMethod all -ZipFileName ilfreight_bloodhound` | Run the SharpHound PowerShell ingestor |
| `bloodhound-python -dc <DC> -gc <GC> -d <DOMAIN -c All -u <user>`           | Run bloodhound-python                  |
