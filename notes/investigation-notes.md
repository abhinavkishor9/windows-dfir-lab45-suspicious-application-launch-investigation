# Investigation Notes

## Lab: Suspicious Application Launch Investigation

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

# Investigation Objective

The objective of this investigation was to examine the Windows **UserAssist Registry artifact** and determine whether it could provide supporting evidence of application activity.

The investigation also focused on understanding the limitations of UserAssist and the importance of correlating multiple Windows forensic artifacts.

---

# Step 1 - Verify UserAssist

The UserAssist Registry location was checked using PowerShell.

Command:

`Test-Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

The UserAssist Registry structure was confirmed to be present on the system.

---

# Step 2 - Locate UserAssist

The primary Registry location investigated was:

`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

The equivalent PowerShell path is:

`HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

UserAssist contains GUID-based subkeys with a `Count` key containing application-related values.

---

# Step 3 - Enumerate UserAssist

UserAssist was enumerated using PowerShell.

Command:

`Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

The command returned the available UserAssist GUID-based subkeys.

This confirmed that the expected UserAssist structure existed under the current user's Registry hive.

---

# Step 4 - Query UserAssist Using reg.exe

`reg.exe` was also used to validate the Registry information.

Command:

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s`

The command returned UserAssist Registry information and application-related entries.

Using both PowerShell and `reg.exe` provided an additional validation method.

---

# Step 5 - Examine the Count Key

The UserAssist `Count` subkey was examined to identify application-related values.

The structure follows:

`UserAssist → {GUID} → Count`

The `Count` key contained multiple application-related entries.

This demonstrated that UserAssist can retain historical information related to application activity.

---

# Step 6 - Create Investigation Workspace

A dedicated directory was created for the controlled investigation.

Command:

`New-Item C:\UserAssistLab -ItemType Directory`

The directory was used to store the simulated executable.

---

# Step 7 - Create Controlled Test Executable

A safe copy of Windows Notepad was used as the test executable.

Command:

`Copy-Item C:\Windows\System32\notepad.exe C:\UserAssistLab\InvoiceViewer.exe`

The executable was renamed to:

`InvoiceViewer.exe`

This provided a controlled way to reproduce a suspicious application-launch scenario without using actual malware.

---

# Step 8 - Verify the Test Executable

The investigation directory was examined.

Command:

`Get-ChildItem C:\UserAssistLab`

The output confirmed that `InvoiceViewer.exe` was present in the investigation directory.

---

# Step 9 - Execute the Test Application

The simulated suspicious application was launched using PowerShell.

Command:

`Start-Process C:\UserAssistLab\InvoiceViewer.exe`

The application launched successfully.

This created a controlled execution event that could then be compared against Windows forensic artifacts.

---

# Step 10 - Verify the Executable

The executable was verified again after execution.

Command:

`Get-Item C:\UserAssistLab\InvoiceViewer.exe`

The file was confirmed to exist on disk.

---

# Step 11 - Search UserAssist for InvoiceViewer

UserAssist was searched for the simulated executable name.

Command:

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s | Select-String "InvoiceViewer"`

No direct `InvoiceViewer` match was identified.

This was recorded as an investigation finding.

It was not interpreted as proof that the application was never executed.

---

# Step 12 - Analyze the Result

The controlled executable was successfully created and launched.

However, the expected executable name was not directly identified within the UserAssist search results.

This demonstrates an important limitation of UserAssist:

> The absence of a filename in UserAssist does not prove that the application was never executed.

UserAssist should therefore be considered supporting evidence rather than a complete execution log.

---

# Step 13 - Identify Supporting Artifacts

Because UserAssist alone did not provide a direct match, additional Windows artifacts were identified for correlation.

| Artifact | Investigation Value |
| -------- | ------------------- |
| Prefetch | Execution evidence and execution-related timestamps |
| Amcache | Program presence and application metadata |
| ShimCache | Application compatibility and executable-path information |
| BAM/DAM | User-associated execution information |
| MUICache | Application-related Registry evidence |
| LNK Files | Evidence of files or applications accessed through shortcuts |
| Recent Items | Evidence of recently accessed files |
| Windows Event ID 4688 | Process creation evidence |
| Sysmon Event ID 1 | Detailed process creation information |
| File System Timestamps | Additional temporal context |

---

# Evidence Correlation

The investigation correlated:

- Controlled executable creation
- Controlled executable execution
- UserAssist Registry evidence
- PowerShell output
- `reg.exe` output
- Supporting Windows forensic artifacts

The `InvoiceViewer.exe` file was confirmed on disk and successfully executed.

UserAssist was present and contained application-related entries, but a direct `InvoiceViewer.exe` entry was not confirmed.

Therefore, the UserAssist result was considered **inconclusive for proving execution**.

---

# Investigation Findings

The investigation established the following:

1. UserAssist was present on the Windows endpoint.
2. UserAssist contained GUID-based Registry structures.
3. The `Count` key contained application-related entries.
4. A controlled `InvoiceViewer.exe` was created.
5. The executable was successfully launched.
6. The executable remained present on disk during the investigation.
7. A direct search for `InvoiceViewer` did not identify a matching UserAssist entry.
8. UserAssist alone was insufficient to confirm the application's execution history.
9. Additional Windows execution artifacts should be correlated.

---

# Forensic Assessment

The evidence does not support the conclusion that `InvoiceViewer.exe` was never executed.

Instead, the correct assessment is:

**UserAssist did not provide a directly identifiable entry for the simulated executable during this investigation. Additional artifacts are required to determine whether the application was executed and to establish an accurate execution timeline.**

---

# Key DFIR Lesson

A single Windows artifact should rarely be used as definitive proof of application execution or non-execution.

UserAssist can provide useful supporting evidence, but it should be correlated with:

- Prefetch
- Amcache
- ShimCache
- BAM/DAM
- MUICache
- LNK files
- Recent Items
- Windows Event Logs
- Sysmon
- File system timestamps

The strongest DFIR conclusions come from **correlating multiple independent artifacts** rather than relying on one Registry artifact.

---

# Investigation Conclusion

The investigation successfully demonstrated how UserAssist can be located and analyzed using native Windows tools. A controlled suspicious application launch was reproduced using `InvoiceViewer.exe`, but the executable was not directly identified through a simple UserAssist filename search.

The result highlighted an important forensic limitation: **absence of a UserAssist entry is not proof of non-execution**. Additional endpoint artifacts must be examined and correlated before reaching a final conclusion about suspicious application execution.
