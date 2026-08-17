Add New Users to Active Directory using Powershell
--------------------------------------------------------------

To add users to Active Directory with PowerShell, use the ActiveDirectory module and the New-ADUser cmdlet. Be sure to run PowerShell as Administrator.

### 1. Make sure the ActiveDirectory PowerShell module is available

On a domain controller, it is usually already installed. You can test it with:

```
Get-Module -ListAvailable ActiveDirectory
```

If it's not available then install it.

```
Install-WindowsFeature -Name RSAT-AD-PowerShell
```

Then import it.

```
Import-Module ActiveDirectory
```

### 2. Create a single user

```
#Hard Code in password
$Password = ConvertTo-SecureString "StrongPassword" -AsPlainText -Force

#Prompt for typing in password
$Password = Read-Host "Enter password" -AsSecureString

#Add User
New-ADUser `
    -SamAccountName "jdoe" `
    -UserPrincipalName "jdoe@corp.example.com" `
    -Name "John Doe" `
    -GivenName "John" `
    -Surname "Doe" `
    -DisplayName "John Doe" `
    -EmailAddress "jdoe@corp.example.com" `
    -Path "DC=corp,DC=example,DC=com" `
    -AccountPassword $Password `
    -Enabled $true `
    -ChangePasswordAtLogon $true
```

#### 2b. Create Multiple Users from a CSV File

Make sure the csv file headers match the ones used in your script.

```
Firstname,Lastname,SamAccountName,Department
```

```
Import-Module ActiveDirectory

$DomainName = (Get-ADDomain).DNSRoot
$CSVPath = "path\to\csv\file"
$DefaultPassword = ConvertTo-SecureString "StrongPassword" -AsPlainText -Force

$Users = Import-Csv -Path $CSVPath

foreach ($User in $Users) {
    $UPN = "$($User.SamAccountName)@$DomainName"
    
    # Check if account already exists
    if (-not (Get-ADUser -Filter "SamAccountName -eq '$($User.SamAccountName)'")) {
        New-ADUser `
            -Name "$($User.FirstName) $($User.LastName)" `
            -GivenName $User.FirstName `
            -Surname $User.LastName `
            -DisplayName "$($User.LastName), $($User.FirstName)" `
            -SamAccountName $User.SamAccountName `
            -UserPrincipalName $UPN `
            -Department $User.Department `
            -AccountPassword $DefaultPassword `
            -Enabled $true `
            -ChangePasswordAtLogon $true

        Write-Host "Successfully created user: $($User.SamAccountName)" -ForegroundColor Green
    } else {
        Write-Host "User '$($User.SamAccountName)' already exists. Skipping." -ForegroundColor Yellow
    }
}
```
