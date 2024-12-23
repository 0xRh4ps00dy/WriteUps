# Installation in 3 steps

## 1. Install Java

https://www.oracle.com/java/technologies/javase-jdk11-downloads.html

```powershell-session
.\jdk-11.0.17_windows-x64_bin.exe /s
```


## 2. Install Neo4j

https://neo4j.com/download-center/#community

```powershell-session
Expand-Archive .\neo4j-community-4.4.16-windows.zip .
```
## 3. Install BloodHound

| Command                                                                     | Description                            |
| --------------------------------------------------------------------------- | -------------------------------------- |
| `.\SharpHound.exe -c all --zipfilename inlanefreight_bloodhound`            | Run the SharpHound C# ingestor         |
| `Invoke-BloodHound -CollectionMethod all -ZipFileName ilfreight_bloodhound` | Run the SharpHound PowerShell ingestor |
| `bloodhound-python -dc <DC> -gc <GC> -d <DOMAIN -c All -u <user>`           | Run bloodhound-python                  |
