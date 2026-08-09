# Timeline

## Lab: Suspicious Application Launch Investigation

---

# Investigation Timeline

| Step | Activity | Evidence / Observation |
| ---- | -------- | ---------------------- |
| 01 | Investigation started | Suspicious application launch scenario established |
| 02 | UserAssist verified | UserAssist Registry key returned `True` |
| 03 | UserAssist located | `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist` identified |
| 04 | UserAssist enumerated | GUID-based subkeys identified |
| 05 | `Count` key examined | Application-related UserAssist values identified |
| 06 | Investigation workspace created | `C:\UserAssistLab` created |
| 07 | Test executable created | `InvoiceViewer.exe` created from a copy of Notepad |
| 08 | Test executable verified | `InvoiceViewer.exe` confirmed on disk |
| 09 | Application launched | `InvoiceViewer.exe` successfully executed |
| 10 | Executable verified after launch | File remained present in investigation directory |
| 11 | UserAssist re-examined | Existing UserAssist entries reviewed |
| 12 | Filename search performed | `InvoiceViewer` search returned no direct match |
| 13 | Result assessed | UserAssist evidence considered inconclusive for execution |
| 14 | Supporting artifacts identified | Prefetch, Amcache, ShimCache, BAM/DAM, MUICache, Sysmon and other artifacts identified for correlation |
| 15 | Evidence correlated | UserAssist evaluated alongside application and execution evidence |
| 16 | Investigation concluded | Additional artifacts required for definitive execution determination |

---

# Detailed Timeline

## 1. Investigation Initiation

The investigation began with a scenario involving a potentially suspicious application named `InvoiceViewer.exe`.

The objective was to determine whether Windows retained evidence of the application's activity after execution.

---

## 2. UserAssist Verification

The UserAssist Registry artifact was verified using PowerShell.

Command:

`Test-Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

Result:

`True`

This confirmed that UserAssist was present for the current Windows user.

---

## 3. UserAssist Location Identified

The following Registry location was identified:

`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

The corresponding PowerShell path was:

`HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

---

## 4. UserAssist Structure Examined

The UserAssist Registry structure was enumerated.

Command:

`Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

GUID-based subkeys were identified.

The `Count` subkey was identified as the important location for application-related values.

---

## 5. Existing UserAssist Entries Examined

The UserAssist `Count` entries were examined using native Windows tools.

Existing application-related values were observed.

This established that UserAssist contained historical application activity information.

---

## 6. Investigation Workspace Created

A dedicated investigation directory was created:

`C:\UserAssistLab`

Command:

`New-Item C:\UserAssistLab -ItemType Directory`

The directory was used to store the controlled test executable.

---

## 7. Controlled Executable Created

A copy of Windows Notepad was created and renamed:

`InvoiceViewer.exe`

Command:

`Copy-Item C:\Windows\System32\notepad.exe C:\UserAssistLab\InvoiceViewer.exe`

This provided a safe executable for reproducing the suspicious application launch scenario.

---

## 8. Test Executable Verified

The investigation directory was checked.

Command:

`Get-ChildItem C:\UserAssistLab`

`InvoiceViewer.exe` was confirmed to be present.

---

## 9. Suspicious Application Launch Simulated

The controlled executable was launched.

Command:

`Start-Process C:\UserAssistLab\InvoiceViewer.exe`

The application launched successfully.

This established a known execution event for comparison with Windows forensic artifacts.

---

## 10. Executable Verified After Execution

The executable was checked again.

Command:

`Get-Item C:\UserAssistLab\InvoiceViewer.exe`

The file remained present on disk.

---

## 11. UserAssist Re-Examined

After executing the controlled application, the UserAssist Registry structure was examined again.

The existing UserAssist entries were reviewed to determine whether the simulated executable appeared.

---

## 12. InvoiceViewer Filename Search

A direct search for the test executable was performed.

Command:

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s | Select-String "InvoiceViewer"`

Result:

No direct `InvoiceViewer` match was identified.

---

## 13. Negative Result Assessed

The missing `InvoiceViewer.exe` entry was documented as an investigation finding.

The result was **not** interpreted as proof that the application was never executed.

UserAssist is not a complete execution log.

---

## 14. Supporting Artifacts Identified

Because UserAssist did not provide a direct match, additional forensic artifacts were identified for correlation.

The artifacts included:

- Prefetch
- Amcache
- ShimCache
- BAM/DAM
- MUICache
- LNK files
- Recent Items
- Windows Event ID 4688
- Sysmon Event ID 1
- File system timestamps

---

## 15. Evidence Correlation

The controlled execution event was compared with the available UserAssist evidence.

The executable was confirmed on disk and successfully launched, while a direct UserAssist entry was not confirmed.

The UserAssist result was therefore considered **inconclusive for proving execution**.

---

## 16. Investigation Conclusion

The investigation demonstrated that UserAssist can provide useful supporting evidence about application activity, but it should not be treated as standalone proof of execution.

The absence of `InvoiceViewer.exe` from the UserAssist search did not establish that the application was never executed.

A definitive conclusion would require correlation with additional endpoint artifacts.

---

# Timeline Summary

The investigation followed this sequence:

**UserAssist Verification → Registry Enumeration → Controlled Executable Creation → Application Execution → UserAssist Re-Examination → Filename Search → Negative Result Assessment → Artifact Correlation → Forensic Conclusion**

---

# Final DFIR Assessment

The timeline demonstrates an important forensic principle:

> A missing artifact entry does not automatically mean that the associated activity never occurred.

UserAssist provided useful contextual evidence, but additional artifacts are required to reconstruct a reliable application execution timeline.
