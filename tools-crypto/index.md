---
title: "Tools.Crypto"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.IO
Imports System.Text
Imports System.Security.Cryptography
 
Namespace Tools
    Namespace Crypto
        Namespace OneWay
            Public Class MD5
                Private md5Provider As New MD5CryptoServiceProvider
                Private UTF8 As New UTF8Encoding
 
                Public Function GetHash(ByVal PlainText As String) As String
                    Dim plainBytes As Byte() = UTF8.GetBytes(PlainText)  'Convert to a byte array
                    Dim encryptedBytes As Byte() = md5Provider.ComputeHash(plainBytes)  'Encrypt the byte array
                    Dim encryptedText As String = Convert.ToBase64String(encryptedBytes)  'Convert byte array to base64 string
                    Return encryptedText
                End Function
            End Class
 
            Public Class SHA1
                Private shaProvider As New SHA1CryptoServiceProvider
                Private UTF8 As New UTF8Encoding
 
                Public Function GetHash(ByVal PlainText As String) As String
                    Dim plainBytes As Byte() = UTF8.GetBytes(PlainText)  'Convert to a byte array
                    Dim encryptedBytes As Byte() = shaProvider.ComputeHash(plainBytes)  'Encrypt the byte array
                    Dim encryptedText As String = Convert.ToBase64String(encryptedBytes)  'Convert byte array to base64 string
                    Return encryptedText
                End Function
            End Class
 
            Public Class SHA256
                Private shaProvider As New SHA256CryptoServiceProvider
                Private UTF8 As New UTF8Encoding
 
                Public Function GetHash(ByVal PlainText As String) As String
                    Dim plainBytes As Byte() = UTF8.GetBytes(PlainText)  'Convert to a byte array
                    Dim encryptedBytes As Byte() = shaProvider.ComputeHash(plainBytes)  'Encrypt the byte array
                    Dim encryptedText As String = Convert.ToBase64String(encryptedBytes)  'Convert byte array to base64 string
                    Return encryptedText
                End Function
            End Class
 
            Public Class SHA384
                Private shaProvider As New SHA384CryptoServiceProvider
                Private UTF8 As New UTF8Encoding
 
                Public Function GetHash(ByVal PlainText As String) As String
                    Dim plainBytes As Byte() = UTF8.GetBytes(PlainText)  'Convert to a byte array
                    Dim encryptedBytes As Byte() = shaProvider.ComputeHash(plainBytes)  'Encrypt the byte array
                    Dim encryptedText As String = Convert.ToBase64String(encryptedBytes)  'Convert byte array to base64 string
                    Return encryptedText
                End Function
            End Class
 
            Public Class SHA512
                Private shaProvider As New SHA512CryptoServiceProvider
                Private UTF8 As New UTF8Encoding
 
                Public Function GetHash(ByVal PlainText As String) As String
                    Dim plainBytes As Byte() = UTF8.GetBytes(PlainText)  'Convert to a byte array
                    Dim encryptedBytes As Byte() = shaProvider.ComputeHash(plainBytes)  'Encrypt the byte array
                    Dim encryptedText As String = Convert.ToBase64String(encryptedBytes)  'Convert byte array to base64 string
                    Return encryptedText
                End Function
            End Class
        End Namespace
 
        Namespace TwoWay
            Public Class AES
                Private aesProvider As New AesCryptoServiceProvider
                Private UTF8 As New UTF8Encoding
                Private MD5Hash As New MD5CryptoServiceProvider
                Private m\_Key As String
 
                Public Sub New()
                    Me.New("AES!Password")
                End Sub
 
                Public Sub New(ByVal Key As String)
                    Me.m\_Key = Key
                End Sub
 
                Public WriteOnly Property Key As String
                    Set(ByVal NewKey As String)
                        m\_Key = NewKey
                    End Set
                End Property
 
                Public Function Encrypt(ByVal PlainText As String) As String
                    aesProvider.Key = MD5Hash.ComputeHash(UTF8.GetBytes(m\_Key)) ' Scramble the password
                    aesProvider.Mode = CipherMode.ECB ' Set the mode to ECB (encrypt each block individually)
                    Dim aesEncryptor As ICryptoTransform = aesProvider.CreateEncryptor ' Init the encryptor
                    Dim plainBytes As Byte() = UTF8.GetBytes(PlainText) ' Convert our text to a byte array
                    Dim encryptedBytes As Byte() = aesEncryptor.TransformFinalBlock(plainBytes, 0, plainBytes.Length) ' Encrypt the byte array
                    Dim encryptedText As String = Convert.ToBase64String(encryptedBytes) ' Convert the byte array to a base64 string
                    Return encryptedText
                End Function
 
                Function Decrypt(ByVal EncryptedText As String) As String
                    aesProvider.Key = MD5Hash.ComputeHash(UTF8.GetBytes(m\_Key)) ' Scramble the password
                    aesProvider.Mode = CipherMode.ECB ' Set the mode to ECB (encrypt each block individually)
                    Dim aesDecryptor As ICryptoTransform = aesProvider.CreateDecryptor ' Init the decryptor
                    Dim encryptedBytes As Byte() = Convert.FromBase64String(EncryptedText) ' Convert our base64 text to a byte array
                    Dim plainBytes As Byte() = aesDecryptor.TransformFinalBlock(encryptedBytes, 0, encryptedBytes.Length) ' Decrypt the byte array
                    Dim plainText As String = UTF8.GetString(plainBytes) ' Convert the byte array to a string
                    Return plainText
                End Function
            End Class
 
            Public Class TripleDES
                Private desProvider As New TripleDESCryptoServiceProvider
                Private UTF8 As New UTF8Encoding
                Private MD5Hash As New MD5CryptoServiceProvider
                Private m\_Key As String
 
                Public Sub New()
                    Me.New("TripleDES!Password")
                End Sub
 
                Public Sub New(ByVal Key As String)
                    Me.Key = Key
                End Sub
 
                Public WriteOnly Property Key As String
                    Set(ByVal NewKey As String)
                        m\_Key = NewKey
                    End Set
                End Property
 
                Public Function Encrypt(ByVal PlainText As String) As String
                    desProvider.Key = MD5Hash.ComputeHash(UTF8.GetBytes(m\_Key)) ' Scramble the password
                    desProvider.Mode = CipherMode.ECB ' Set the mode to ECB (encrypt each block individually)
                    Dim desEncryptor As ICryptoTransform = desProvider.CreateEncryptor ' Init the encryptor
                    Dim plainBytes As Byte() = UTF8.GetBytes(PlainText) ' Convert our text to a byte array
                    Dim encryptedBytes As Byte() = desEncryptor.TransformFinalBlock(plainBytes, 0, plainBytes.Length) ' Encrypt the byte array
                    Dim encryptedText As String = Convert.ToBase64String(encryptedBytes) ' Convert the byte array to a base64 string
                    Return encryptedText
                End Function
 
                Public Function Decrypt(ByVal EncryptedText As String) As String
                    desProvider.Key = MD5Hash.ComputeHash(UTF8.GetBytes(m\_Key)) ' Scramble the password
                    desProvider.Mode = CipherMode.ECB ' Set the mode to ECB (encrypt each block individually)
                    Dim desDecryptor As ICryptoTransform = desProvider.CreateDecryptor ' Init the decryptor
                    Dim encryptedBytes As Byte() = Convert.FromBase64String(EncryptedText) ' Convert our base64 text to a byte array
                    Dim plainBytes As Byte() = desDecryptor.TransformFinalBlock(encryptedBytes, 0, encryptedBytes.Length) ' Decrypt the byte array
                    Dim plainText As String = UTF8.GetString(plainBytes) ' Convert the byte array to a string
                    Return plainText
                End Function
            End Class
        End Namespace
    End Namespace
End Namespace
