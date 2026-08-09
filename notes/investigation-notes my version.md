# Investigation Notes

## Lab: Suspicious Application Launch Investigation

---

# Investigation Scenario

An employee claims:

“I never opened that application.”

The SOC discovers:

InvoiceViewer.exe

was previously present on the workstation.

The executable has now been deleted.

The investigator wants to determine:

Was the application launched?
Was it launched interactively?
Is there evidence associated with the user's profile?
Can UserAssist identify the application?
Can the finding be correlated with other artifacts?

---

# Investigation Objective

The objective of this investigation was to examine UserAssist Registry artifact  and determine whether it could provide supporting evidence of application activity.

We understood the limitations of UserAssist and the importance of correlating multiple Windows forensic artifacts.

---

# Step 1 - Verify UserAssist

Check UserAssist Registry location  using PowerShell.

Command:

`Test-Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

The UserAssist Registry structure was confirmed to be present on the system.

---

# Step 2 - Locate UserAssist

The primary Registry location investigated was:

`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

The equivalent PowerShell path is:

`HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`

---

# Step 3 - Enumerate UserAssist


Command:

`Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist"`

The command returned the available UserAssist GUID-based subkeys.


---

# Step 4 - Query UserAssist Using reg.exe

`reg.exe` was also used to validate the Registry information.

Command:

`reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist" /s`

The command returned UserAssist Registry information and application-related entries.

Using both PowerShell and `reg.exe` provided an additional validation method.

---

# Step 5 - Examine the Count Key

`UserAssist → {GUID} → Count`

The `Count` key contained multiple application-related entries.

---

# Step 6 - Create Investigation Workspace

Command:

`New-Item C:\UserAssistLab -ItemType Directory`

The directory was used to store the simulated executable.

---

# Step 7 - Create Controlled Test Executable

Command:

`Copy-Item C:\Windows\System32\notepad.exe C:\UserAssistLab\InvoiceViewer.exe`

The executable was renamed to:

`InvoiceViewer.exe`


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

tion Findings

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


# Investigation Conclusion

The simulated application was launched through the Windows GUI, but a corresponding UserAssist entry could not be confirmed. Therefore, UserAssist alone does not establish execution, and additional artifacts should be examined.
