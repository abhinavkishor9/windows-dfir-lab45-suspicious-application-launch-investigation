# Troubleshooting Notes

## Lab: Suspicious Application Launch Investigation

---

# Issue 1 

### Problem

UserAssist was not immediately visible in Registry Editor.

### Investigation

Check The UserAssist artifact using PowerShell.

Command:

`Test-Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

The result returned `True`.

This confirmed that the UserAssist Registry key existed even though it was not initially located through manual navigation.

### Resolution

The UserAssist Registry location was accessed directly:

`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

PowerShell was also used to enumerate the key:

`Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

---

# Issue 2 

### Problem

The UserAssist Registry structure does not directly present application names at the first level.

### Investigation


`UserAssist → {GUID} → Count`

### Resolution

The GUID-based subkeys were enumerated using:

`Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

The `Count` subkey was then examined for application-related values.

---

# Issue 3 

### Problem

After executing `InvoiceViewer.exe`, searching UserAssist for the filename did not return a direct match.

### Command Used

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s | Select-String "InvoiceViewer"`

### Result

No direct `InvoiceViewer` match was identified.


### Resolution

The result was documented as an artifact limitation.

Additional artifacts were identified for correlation:

- Prefetch
- Amcache
- ShimCache
- BAM/DAM
- MUICache
- LNK files
- Recent Items
- Sysmon Event ID 1
- Windows Event ID 4688
- File system timestamps


---



# Issue 4 

### Problem

PowerShell output alone may not always provide the clearest view of Registry contents.

### Resolution

Command:

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s`

This allowed the UserAssist Registry contents to be examined independently of the PowerShell Registry provider.


---



