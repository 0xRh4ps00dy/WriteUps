# Installation in 3 steps

## 1. Install Java




## 2. Install Neo4j


## 3. Install BloodHound

| Command                                                                     | Description                            |
| --------------------------------------------------------------------------- | -------------------------------------- |
| `.\SharpHound.exe -c all --zipfilename inlanefreight_bloodhound`            | Run the SharpHound C# ingestor         |
| `Invoke-BloodHound -CollectionMethod all -ZipFileName ilfreight_bloodhound` | Run the SharpHound PowerShell ingestor |
| `bloodhound-python -dc <DC> -gc <GC> -d <DOMAIN -c All -u <user>`           | Run bloodhound-python                  |
