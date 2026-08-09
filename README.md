# windows-dfir-lab45-suspicious-application-launch-investigation

## Overview

Windows UserAssist is a Registry artifact that can provide supporting evidence about applications launched through the Windows graphical interface. During DFIR investigations, UserAssist can help investigators understand application activity associated with a Windows user profile.

In this hands-on DFIR lab, a simulated `InvoiceViewer.exe` executable was created and executed. The Windows UserAssist Registry location was then examined using Registry Editor, PowerShell, and `reg.exe` to determine whether evidence of the application activity was recorded.

---

# Executive Summary

This investigation focused on analyzing the Windows UserAssist Registry artifact as a source of application activity evidence. A simulated `InvoiceViewer.exe` executable was created and executed as part of a controlled investigation scenario. The UserAssist Registry structure and `Count` key were then examined using native Windows tools.

UserAssist was successfully located and application-related entries were identified. However, a direct `InvoiceViewer.exe` entry was not confirmed. The investigation demonstrated both the usefulness and limitations of UserAssist as a standalone forensic artifact.

---

# Investigation Objectives

- Understand the Windows UserAssist artifact.
- Locate the UserAssist Registry key.
- Identify UserAssist GUID-based subkeys.
- Examine the `Count` Registry key.
- Create a controlled suspicious executable.
- Execute the test executable.
- Search UserAssist for the test application.
- Understand why a direct filename search may fail.
- Identify supporting execution artifacts.
- Document forensic findings and limitations.

---

# Skills Demonstrated

- Windows Registry Forensics
- UserAssist Analysis
- Suspicious Application Investigation
- Application Execution Analysis
- PowerShell Registry Enumeration
- Registry Editor Investigation
- `reg.exe` Usage
- Host-Based DFIR
- Evidence Correlation
- Forensic Documentation

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

Suppose a suspicious application is suspected to have been launched on a Windows workstation.

Investigators discover:

- `InvoiceViewer.exe` is no longer present.
- The application may have been launched by a user.
- Direct process evidence is unavailable.
- Investigators need historical evidence of application activity.

### Investigation Questions

- Was `InvoiceViewer.exe` launched?
- Did Windows retain evidence of the application activity?
- Can UserAssist provide supporting evidence?
- Why might a direct filename search fail?
- What additional artifacts should be examined?

---

# Investigation Workflow

1. Create the investigation workspace.
2. Verify the UserAssist Registry artifact.
3. Locate the UserAssist Registry key.
4. Identify UserAssist GUID-based subkeys.
5. Locate the `Count` key.
6. Examine existing UserAssist entries.
7. Create the simulated `InvoiceViewer.exe`.
8. Verify the executable.
9. Execute the application.
10. Re-examine UserAssist.
11. Search for `InvoiceViewer.exe`.
12. Assess the UserAssist evidence.
13. Identify supporting forensic artifacts.
14. Correlate the evidence.
15. Document findings.
16. Clean up the investigation workspace.

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

UserAssist itself is a forensic artifact rather than a MITRE ATT&CK technique. The mappings represent activities relevant to the investigation scenario.

---

# Evidence Collected

- UserAssist Registry key
- UserAssist GUID-based subkeys
- UserAssist `Count` key
- UserAssist Registry values
- PowerShell Registry output
- `reg.exe` output
- Registry Editor observations
- `InvoiceViewer.exe`
- Investigation workspace
- Application execution evidence
- Supporting Windows execution artifacts

---

# Evidence Correlation

The investigation correlated the simulated executable with Windows Registry and endpoint activity evidence.

The controlled `InvoiceViewer.exe` file was created inside `C:\UserAssistLab` and executed using PowerShell. The UserAssist Registry structure was then examined using PowerShell and `reg.exe`.

Existing application-related UserAssist entries were identified, but a direct `InvoiceViewer.exe` entry was not confirmed.

The result was therefore treated as supporting evidence rather than definitive proof of execution.

---

# Supporting Artifacts

| Artifact | Investigation Value |
| -------- | ------------------- |
| Prefetch | Execution history and executable-related timestamps |
| Amcache | Application presence and execution-related metadata |
| ShimCache | Application compatibility/cache information |
| BAM/DAM | User-associated execution evidence |
| MUICache | Application-related Registry evidence |
| LNK Files | Evidence of files or applications accessed through shortcuts |
| Recent Items | Evidence of recently accessed files |
| Windows Event ID 4688 | Process creation evidence |
| Sysmon Event ID 1 | Detailed process creation evidence |
| File System Timestamps | File creation and modification context |

---

# Investigation Findings

The investigation confirmed that the Windows UserAssist Registry structure was present and contained application-related entries.

The simulated `InvoiceViewer.exe` executable was successfully created and executed. However, a direct search for `InvoiceViewer.exe` did not identify a corresponding UserAssist entry.

This demonstrates an important forensic limitation: the absence of an executable from UserAssist does not prove that the executable was never executed.

UserAssist should therefore be treated as supporting evidence and correlated with other Windows execution artifacts.

---

# Investigation Limitations

- UserAssist is not a complete execution log.
- A missing UserAssist entry does not prove non-execution.
- A single artifact should not be used to make a definitive execution determination.
- Application activity should be correlated with additional endpoint evidence.
- Artifact behavior may vary depending on the application and execution method.

---

# Key Takeaways

- UserAssist is a useful Windows DFIR artifact.
- UserAssist is stored under the user's Registry hive.
- UserAssist contains GUID-based subkeys.
- The `Count` key is important during UserAssist analysis.
- A simple filename search should not be the only investigation method.
- PowerShell and `reg.exe` can both be useful for Registry investigation.
- The absence of a UserAssist entry does not prove that an application was never executed.
- Multiple independent artifacts should be correlated.
- Controlled test applications can safely reproduce execution scenarios during DFIR labs.

---

# Conclusion

This lab demonstrated a scenario-based investigation of a suspicious application launch using the Windows UserAssist Registry artifact. A controlled `InvoiceViewer.exe` executable was created and successfully executed, followed by examination of the UserAssist Registry structure using PowerShell, Registry Editor, and `reg.exe`.

Although a direct `InvoiceViewer.exe` entry was not confirmed in UserAssist, the investigation demonstrated an important DFIR principle: **a single artifact should not be treated as definitive evidence of execution or non-execution**.

UserAssist should be correlated with Prefetch, Amcache, ShimCache, BAM/DAM, MUICache, LNK files, Recent Items, Sysmon, Windows Event Logs, and filesystem evidence to build a more reliable execution timeline.
