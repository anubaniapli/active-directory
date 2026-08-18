PowerShell script to reset an Active Directory user's password and force them to change it at their next logon.

Requirements:

 *   Must be run from a domain-joined machine or via RSAT (Remote Server Administration Tools).

 *   Requires an account with Active Directory privileges to reset user passwords (e.g., Domain Admin or delegated permissions on the target OU).

```
# Import the Active Directory module
Import-Module ActiveDirectory

# Define the target user's SamAccountName
$UserName = "jdoe"

# Prompt securely for the new password
$NewPassword = Read-Host -AsSecureString -Prompt "Enter the new password"

# Reset the password and enforce password change at next logon
Set-ADAccountPassword -Identity $UserName -NewPassword $NewPassword -Reset
Set-ADUser -Identity $UserName -ChangePasswordAtLogon $true
```

One-Liner (For Quick Execution)

```
Set-ADAccountPassword -Identity "jdoe" -NewPassword (Read-Host -AsSecureString -Prompt "New Password") -Reset; Set-ADUser -Identity "jdoe" -ChangePasswordAtLogon $true
```
