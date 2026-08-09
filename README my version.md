# windows-dfir-lab45-suspicious-application-launch-investigation

## Overview

UserAssist is a Windows Registry artifact associated with applications and shortcuts launched through the Windows graphical interface.

It is stored in the user's Registry hive:

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist

Inside UserAssist are GUID-named keys, and under those keys we commonly find:

Count

The Count data contains information related to programs and shortcuts launched through the Windows GUI.

---

# Executive Summary

This investigation focused on analyzing the Windows UserAssist Registry artifact as a source of application activity evidence. A simulated `InvoiceViewer.exe` executable was created and executed as part of a controlled investigation scenario. The UserAssist Registry structure and `Count` key were then examined using native Windows tools.

demonstrated both the usefulness and limitations of UserAssist as a standalone forensic artifact.

---

# Investigation Objectives

- Did the user actually launch the application?
- Was it launched through the Windows GUI?
- How many times was it launched?
- When was it last launched?
- Can UserAssist provide supporting evidence?
- Can the UserAssist finding be correlated with other Windows artifacts?

---


# Tools Used

- Windows 10
- PowerShell
- Registry Editor
- `reg.exe`
- File Explorer

---

# Lab Environment

| Component | Details |
| --------- | ------- |
| Operating System | Windows 10 |
| Investigation Type | Host-Based DFIR |
| Primary Artifact | Windows UserAssist |
| Registry Hive | HKEY_CURRENT_USER |
| Analysis Method | Native Windows Tools |
| Test Application | InvoiceViewer.exe |
| Investigation Directory | C:\UserAssistLab |

---

# Investigation Scenario

A Windows user reports that they did not launch a suspicious application on their workstation.

During the investigation, the analyst discovers that a suspicious executable named:

InvoiceViewer.exe

was present on the system earlier, but the file is no longer available.

The investigation needs to determine:

Did the user actually launch the application?
Was it launched through the Windows GUI?
How many times was it launched?
When was it last launched?
Can UserAssist provide supporting evidence?
Can the UserAssist finding be correlated with other Windows artifacts?

---

# Investigation Workflow

1. Create the lab folder.
2. Verify the UserAssist Registry artifact.
3. Locate the UserAssist Registry key.
4. Identify UserAssist GUID-based subkeys.
5. Locate the `Count` key.
6. Examine existing UserAssist entries.
7. Create `InvoiceViewer.exe`.
8. Verify the executable.
9. Execute the application.
10. Re-examine UserAssist.
11. Search for `InvoiceViewer.exe`.
12. Assess the UserAssist evidence.

---

# UserAssist Registry Location

The primary Registry location examined during the investigation was:

`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

The same location can be accessed using PowerShell:

`HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

The UserAssist structure contains GUID-based subkeys with a `Count` key containing application-related values.

---

# Key Commands

### Verify UserAssist

`Test-Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

### Query UserAssist

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s`

### Enumerate UserAssist

`Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

### Create Investigation Directory

`New-Item C:\UserAssistLab -ItemType Directory`

### Create Controlled Test Executable

`Copy-Item C:\Windows\System32\notepad.exe C:\UserAssistLab\InvoiceViewer.exe`

### Execute Test Application

`Start-Process C:\UserAssistLab\InvoiceViewer.exe`

### Search for Test Application

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s | Select-String "InvoiceViewer"`

---

# MITRE ATT&CK Mapping

| Technique | Description |
| --------- | ----------- |
| T1059.001 | PowerShell |
| T1083 | File and Directory Discovery |
| T1204.002 | User Execution: Malicious File |

# Conclusion

The simulated application was launched through the Windows GUI, but a corresponding UserAssist entry could not be confirmed. Therefore, UserAssist alone does not establish execution, and additional artifacts should be examined.
# Conclusion

