---
title: "Encrypt-String and Decrypt-String"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

**AES256 encryption**

_Encrypt-String_ and _Decrypt-String_ functions are in the last code block. Here are some examples first.

 

**Encrypt a text file, line-by-line:** 

Get-Content "c:\\somefile.txt" | Encrypt-String "myPassword"

 

**Encrypt a text file as a single string:** 

Get-Content "c:\\somefile.txt" | Out-String | Encrypt-String "myPassword"

 

**Encrypt and decrypt a string:**

$plainText = "This is unencrypted text"
$encrypted = Encrypt-String $plainText "myPassword"  #$encrypted = "W+mpS97SGoiGxKj5l555wj/FBMDlTXorUUKVFNyz9vM="
$decrypted = Decrypt-String $encrypted "myPassword"  #$decrypted = "This is unencrypted text"

 

**The actual functions:**

\### Import the System.Security .NET assembly/namespace
\[void\] \[Reflection.Assembly\]::LoadWithPartialName("System.Security")

Function Encrypt-String() {
	\[CmdletBinding()\]
	Param(
		\[Parameter(Mandatory=$True,ValueFromPipeline=$True,ValueFromPipelinebyPropertyName=$True)\]
		$String,
		$Passphrase = "aEs256PaSsWoRd",
		$Salt = "SaLtPaSsWoRd",
		$IvInit = "iNtReSeCtInGvEcToR"
	)

	Process {
		try {
			### New COM object for Rijndael cryptography
			$r = New-Object System.Security.Cryptography.RijndaelManaged
			
			### Convert $Passphrase and $Salt to byte arrays
			$pass = \[Text.Encoding\]::UTF8.GetBytes($Passphrase)
			$Salt = \[Text.Encoding\]::UTF8.GetBytes($Salt)
		
			### Create an encryption key using the passphrase, salt, and SHA1 at 256 bits (32 \* 8)
			$r.Key = (New-Object Security.Cryptography.PasswordDeriveBytes $pass, $Salt, "SHA1", 5).GetBytes(32)
			
			### Create an Intersecting Vector hash with the init password
			$r.IV = (New-Object Security.Cryptography.SHA1Managed).ComputeHash(\[Text.Encoding\]::UTF8.GetBytes($IvInit))\[0..15\]
			
			### Create new encryptor
			$c = $r.CreateEncryptor()
			
			### Create new MemoryStream
			$ms = New-Object IO.MemoryStream
			
			### Create new CryptoStream
			$cs = New-Object Security.Cryptography.CryptoStream $ms, $c, "Write"
			
			### Create new StreamWriter
			$sw = New-Object IO.StreamWriter $cs
			
			### Write the string to the CryptoStream
			$sw.Write($String)
			
			### Close the StreamWriter, CryptoStream, and MemoryStream
			$sw.Close()
			$cs.Close()
			$ms.Close()
			
			### Clear the IV and Hash from memory
			$r.Clear()
			
			### Write the MemoryStream to an array
			\[byte\[\]\]$result = $ms.ToArray()
			
			### Return the encrypted string encoded in Base64
			\[Convert\]::ToBase64String($result)		
		} catch {
			"encryptError"
		}
	}
}

Function Decrypt-String() {
	\[CmdletBinding()\]
	Param(
		\[Parameter(Mandatory=$True,ValueFromPipeline=$True,ValueFromPipelinebyPropertyName=$True)\]
		$String,
		$Passphrase = "aEs256PaSsWoRd",
		$Salt = "SaLtPaSsWoRd",
		$IvInit = "iNtReSeCtInGvEcToR"
	)

	Process {
		Try {
			### Convert from Base64 to byte array
			$Encrypted = $String
			If ( $Encrypted -is \[string\] ) {
				$Encrypted = \[Convert\]::FromBase64String($Encrypted)
			}

			### New COM object for Rijndael cryptography
			$r = New-Object System.Security.Cryptography.RijndaelManaged
			
			### Convert $Passphrase and $Salt to byte arrays
			$pass = \[Text.Encoding\]::UTF8.GetBytes($Passphrase)
			$Salt = \[Text.Encoding\]::UTF8.GetBytes($Salt)
		
			### Create an encryption key using the passphrase, salt, and SHA1 at 256 bits (32 \* 8)
			$r.Key = (New-Object Security.Cryptography.PasswordDeriveBytes $pass, $Salt, "SHA1", 5).GetBytes(32)
			
			### Create an Intersecting Vector hash with the init password
			$r.IV = (New-Object Security.Cryptography.SHA1Managed).ComputeHash(\[Text.Encoding\]::UTF8.GetBytes($IvInit))\[0..15\]
			
			### Create new decryptor
			$c = $r.CreateDecryptor()
			
			### Create new MemoryStream
			$ms = New-Object IO.MemoryStream @(,$Encrypted)
			
			### Create new CryptoStream
			$cs = New-Object Security.Cryptography.CryptoStream $ms, $c, "Read"
			
			### Create new StreamReader
			$sr = New-Object IO.StreamReader $cs
			
			### Read the decrypted string
			$decrypted = $sr.ReadToEnd()
			
			### Close the StreamReader, CryptoStream, and MemoryStream
			$sr.Close()
			$cs.Close()
			$ms.Close()

			### Clear the IV and Hash from memory
			$r.Clear()
			
			### Return the decrypted string
			$decrypted
		} Catch {
			"decryptError"
		}
	}
}
