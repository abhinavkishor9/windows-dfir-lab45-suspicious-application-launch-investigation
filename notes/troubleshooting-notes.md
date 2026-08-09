# Troubleshooting Notes

## Lab: Suspicious Application Launch Investigation

---

# Issue 1 - UserAssist Not Immediately Visible

### Problem

UserAssist was not immediately visible when navigating through Registry Editor.

### Investigation

The UserAssist artifact was checked using PowerShell.

Command:

`Test-Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

The result returned `True`.

This confirmed that the UserAssist Registry key existed even though it was not initially located through manual navigation.

### Resolution

The UserAssist Registry location was accessed directly:

`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

PowerShell was also used to enumerate the key:

`Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

### Lesson

When a Registry artifact is difficult to locate manually, verify its existence and location using PowerShell before assuming that the artifact is missing.

---

# Issue 2 - UserAssist Contains GUID-Based Subkeys

### Problem

The UserAssist Registry structure does not directly present application names at the first level.

### Investigation

The UserAssist key contains GUID-based subkeys.

The structure is:

`UserAssist → {GUID} → Count`

### Resolution

The GUID-based subkeys were enumerated using:

`Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

The `Count` subkey was then examined for application-related values.

### Lesson

UserAssist analysis requires understanding its Registry structure rather than expecting executable names to appear immediately under the main UserAssist key.

---

# Issue 3 - Direct Filename Search Returned No InvoiceViewer Entry

### Problem

After executing `InvoiceViewer.exe`, searching UserAssist for the filename did not return a direct match.

### Command Used

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s | Select-String "InvoiceViewer"`

### Result

No direct `InvoiceViewer` match was identified.

### Analysis

This result was not treated as proof that the executable was never executed.

UserAssist is not a complete execution log, and the absence of a filename does not necessarily indicate non-execution.

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

### Lesson

A negative result from one forensic artifact should be treated as **inconclusive** unless supported by additional evidence.

---

# Issue 4 - PowerShell Registry Access

### Problem

Registry investigation through PowerShell requires the correct Registry provider path.

### Incorrect Approach

Using a normal filesystem path such as:

`C:\Users\<username>\...`

does not directly access Registry keys.

### Correct Approach

Use the `HKCU:` Registry provider:

`HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

### Example

`Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

### Lesson

PowerShell uses Registry provider paths such as `HKCU:` and `HKLM:` when accessing Windows Registry keys.

---

# Issue 5 - Validating Registry Results with reg.exe

### Problem

PowerShell output alone may not always provide the clearest view of Registry contents.

### Resolution

The native Windows `reg.exe` utility was used as a second method of validation.

Command:

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s`

This allowed the UserAssist Registry contents to be examined independently of the PowerShell Registry provider.

### Lesson

Using more than one native tool can help validate forensic observations and reduce the chance of misinterpreting a tool-specific result.

---

# Issue 6 - Test Executable Not Present

### Problem

The controlled `InvoiceViewer.exe` executable must exist before it can be launched.

### Verification Command

`Get-Item C:\UserAssistLab\InvoiceViewer.exe`

### If the File Does Not Exist

Recreate the controlled executable:

`Copy-Item C:\Windows\System32\notepad.exe C:\UserAssistLab\InvoiceViewer.exe`

Then verify:

`Get-ChildItem C:\UserAssistLab`

### Lesson

Always verify the test file before attempting to execute it.

---

# Issue 7 - Investigation Directory Does Not Exist

### Problem

The investigation workspace was not present.

### Resolution

Create the directory:

`New-Item C:\UserAssistLab -ItemType Directory`

Then verify:

`Get-ChildItem C:\UserAssistLab`

### Lesson

Using a dedicated investigation directory keeps controlled artifacts organized and makes the investigation easier to reproduce.

---

# Issue 8 - Test Application Does Not Launch

### Problem

`InvoiceViewer.exe` may fail to launch if the file was not created correctly or the path is incorrect.

### Verification

Check the file:

`Get-Item C:\UserAssistLab\InvoiceViewer.exe`

Check the file type:

`Get-Command C:\UserAssistLab\InvoiceViewer.exe`

### Execution

`Start-Process C:\UserAssistLab\InvoiceViewer.exe`

### Lesson

Verify the executable path and file existence before troubleshooting the execution itself.

---

# Issue 9 - Assuming UserAssist Is Proof of Execution

### Problem

There is a risk of interpreting UserAssist entries as definitive proof that an executable was executed.

### Analysis

UserAssist is a supporting forensic artifact.

Its presence or absence should be interpreted together with other evidence.

### Resolution

Correlate UserAssist with:

- Prefetch
- Amcache
- ShimCache
- BAM/DAM
- MUICache
- Sysmon
- Windows Security Logs
- LNK files
- Recent Items
- File system timestamps

### Lesson

**Artifact presence does not automatically equal execution, and artifact absence does not automatically equal non-execution.**

---

# Troubleshooting Summary

| Issue | Resolution |
| ----- | ---------- |
| UserAssist not visible in Registry Editor | Verify using PowerShell |
| GUID-based structure confusing | Navigate to the `Count` subkey |
| `InvoiceViewer.exe` not found | Treat as inconclusive and correlate other artifacts |
| PowerShell Registry path issue | Use the `HKCU:` Registry provider |
| Need independent validation | Use `reg.exe` |
| Test executable missing | Recreate it from Notepad |
| Investigation directory missing | Create `C:\UserAssistLab` |
| Application does not launch | Verify file path and executable |
| UserAssist interpreted as definitive | Correlate multiple forensic artifacts |

---

# Key Troubleshooting Lessons

- Verify Registry artifacts with PowerShell when manual Registry navigation is difficult.
- Understand the internal structure of Windows forensic artifacts before searching them.
- Use `reg.exe` as an independent validation method.
- Do not treat a failed filename search as proof of non-execution.
- Always verify controlled test files before execution.
- Maintain a dedicated investigation workspace.
- Correlate multiple endpoint artifacts before reaching a forensic conclusion.
