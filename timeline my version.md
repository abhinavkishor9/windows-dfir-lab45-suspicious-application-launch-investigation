# Investigation Timeline

| Step | Activity | Evidence / Observation |
| ---- | -------- | ---------------------- |
| 01 | Investigation started | Suspicious application launch scenario established |
| 02 | UserAssist verified | UserAssist Registry key returned `True` |
| 03 | UserAssist located | `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist` identified |
| 04 | UserAssist enumerated | GUID-based subkeys identified |
| 05 | `Count` key  | Application-related UserAssist values identified |
| 06 | Investigation folder  | `C:\UserAssistLab` created |
| 07 | Test executable created | `InvoiceViewer.exe` created from a copy of Notepad |
| 08 | Test executable verified | `InvoiceViewer.exe` confirmed on disk |
| 09 | Application launched | `InvoiceViewer.exe` successfully executed |
| 10 | Executable verified after launch | File remained present in investigation directory |
| 11 | UserAssist re-examined | Existing UserAssist entries reviewed |
| 12 | Filename search performed | `InvoiceViewer` search returned no direct match |
