On Windows Server, installing Active Directory from the command line is done with PowerShell. The process has two main parts:

   1. Install the AD DS role.

   2. Promote the server to a domain controller.

Run all commands in an elevated PowerShell window.

# 1.Set Static IP and Hostname

A Domain Controller requires a static IP address and a clear computer name. Open PowerShell as Administrator and run:

```
# Rename the computer (requires restart if changed)
Rename-Computer -NewName "DC01" -Restart

# Configure static IP address (adjust parameters for your network)
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress "192.168.1.10" -PrefixLength 24 -DefaultGateway "192.168.1.1"
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "127.0.0.1"
```

# 2.Install AD DS Role and Admin Tools

Install the Active Directory Domain Services role along with the Remote Server Administration Tools (RSAT):

```
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

# 3. Promote the Server to a New Forest

Promote the server to a Domain Controller in a new forest. Replace corp.example.com and CORP with your actual domain and NetBIOS names:

```
$SecurePassword = ConvertTo-SecureString "StrongPassword" -AsPlainText -Force

Install-ADDSForest `
  -DomainName "corp.example.com" `
  -DomainNetbiosName "CORP" `
  -SafeModeAdministratorPassword $SecurePassword `
  -InstallDns:$true `
  -Force:$true
```
Note: The server will automatically restart upon completion to finish the promotion.
