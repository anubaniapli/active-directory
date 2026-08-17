Dynamic Secure Password Generation For Powershell
====================================================================================================

This PowerShell script generates a highly secure, cryptographically random 20-character password and stores it safely in memory. It ensures the password contains a mix of uppercase letters, lowercase letters, numbers, and symbols. Here is the step-by-step breakdown of how the code works:

1. Define Character Sets

The script creates an array containing four separate character groups:

Uppercase letters (\(A-Z\))

Lowercase letters (\(a-z\))

Numbers (\(0-9\))

Special characters/symbols

Each string is converted into a character array (ToCharArray()) so individual letters can be selected.

2. Guarantee Complexity Requirements

To ensure the password satisfies strong complexity policies, the script loops through each of the four groups. It uses a cryptographically secure random number generator (RandomNumberGenerator::GetInt32) to pick exactly one random character from each group and adds it to a running list ($passwordChars). This guarantees the final password has at least one uppercase letter, one lowercase letter, one digit, and one symbol.

3. Fill the Remaining Length

The script flattens all four character groups into a single massive array ($allChars). It then runs a loop to fill the rest of the password until it reaches a total length of 20 characters. It continues to use the secure random number generator to pull random characters from the combined pool.

4. Shuffle the Characters

Because the first four characters were added in a specific order (Uppercase, Lowercase, Digit, Symbol), the script uses the Fisher-Yates shuffle algorithm to completely randomize the order. It loops backward through the list, swapping characters randomly. This destroys any predictable patterns and prevents an attacker from guessing that the first four characters represent the four distinct categories.

5. Secure Storage in Memory

Instead of converting the final character list into a standard plain-text string (which can linger vulnerably in computer memory), the script creates a [System.Security.SecureString] object. It appends each character directly to this secure object one by one. Finally, .MakeReadOnly() locks the string so it cannot be modified, protecting it from being leaked or scraped from the system's memory by malicious software.

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

