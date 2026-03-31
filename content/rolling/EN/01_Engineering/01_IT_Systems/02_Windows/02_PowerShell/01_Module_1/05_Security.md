@title: Execution Policy and Signing
@icon: 🛡️
@description: Running scripts safely in real environments.
@order: 5

# Execution policy and signing

**ExecutionPolicy** is not a perfect security boundary but helps reduce casual script execution. Combine with **AppLocker**, **Code Signing**, and **Constrained Language Mode** in mature orgs.

@section: View scopes

```powershell
Get-ExecutionPolicy -List
```

@section: Common values

**RemoteSigned** is a common dev workstation balance; **AllSigned** is stricter.

@section: Signing

Use a **code signing certificate** and `Set-AuthenticodeSignature` for trusted scripts.

@section: Unblock-File

Removes the **Zone.Identifier** mark from downloaded files—only when you trust the source.

@quiz: Which cmdlet shows execution policy settings for all scopes?
@option: Get-Policy
@correct: Get-ExecutionPolicy -List
@option: Show-ExecutionPolicy

@quiz: What does `Set-AuthenticodeSignature` do?
@option: Encrypt the disk
@correct: Digitally sign a script for integrity and origin
@option: Compress scripts
