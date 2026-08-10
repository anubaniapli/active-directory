Dynamic Secure Password Generation For Powershell
====================================================================================================

This PowerShell script generates a highly secure, cryptographically random 20-character password and stores it safely in memory. It ensures the password contains a mix of uppercase letters, lowercase letters, numbers, and symbols.

```

# 1. Define character sets to ensure complexity requirements are met
$charGroups = @(
    'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.ToCharArray(),
    'abcdefghijklmnopqrstuvwxyz'.ToCharArray(),
    '0123456789'.ToCharArray(),
    '!@#$%^&*()-_=+[]{}'.ToCharArray()
)

# 2. Select at least one character from each required category
$passwordChars = [System.Collections.Generic.List[char]]::new()
foreach ($group in $charGroups) {
    $randomIndex = [System.Security.Cryptography.RandomNumberGenerator]::GetInt32(0, $group.Length)
    $passwordChars.Add($group[$randomIndex])
}

# 3. Fill the rest of the password length (e.g., 20 characters total)
$allChars = $charGroups | Select-Object -ExpandProperty SyncRoot
for ($i = $passwordChars.Count; $i -lt 20; $i++) {
    $randomIndex = [System.Security.Cryptography.RandomNumberGenerator]::GetInt32(0, $allChars.Length)
    $passwordChars.Add($allChars[$randomIndex])
}

# 4. Shuffle the characters using Fisher-Yates to avoid predictable patterns
for ($i = $passwordChars.Count - 1; $i -gt 0; $i--) {
    $j = [System.Security.Cryptography.RandomNumberGenerator]::GetInt32(0, $i + 1)
    $temp = $passwordChars[$i]
    $passwordChars[$i] = $passwordChars[$j]
    $passwordChars[$j] = $temp
}

# 5. Convert array directly into a SecureString without creating an exposed plain-text string
$SafeModePassword = [System.Security.SecureString]::new()
foreach ($char in $passwordChars) {
    $SafeModePassword.AppendChar($char)
}
$SafeModePassword.MakeReadOnly()

```

