# AclInspector

> **Welcome to this project.** This is my first project since joining GitHub. It may have some issues, so please bear with me.

A lightweight Windows utility that scans directory ACLs (Access Control Lists) and highlights common permission issues. Written in C#, it runs on Windows 10 and 11 without any additional dependencies.

---

## Overview

AclInspector walks through a folder and every subfolder beneath it, reads the access control entries (ACEs) of each one, and flags the patterns that usually indicate a misconfigured system:

- **Orphaned SIDs** — rules pointing to accounts that no longer exist
- **Explicit Deny rules** — which take priority over Allow and often cause confusing access errors
- **Everyone: Full Control** — a well-known security risk on non-personal folders

On top of scanning, it can **snapshot** the current permission table and **restore** it later. Snapshots are stored in a custom `.neon` format that only AclInspector can read.

---

## Features

| Feature | Description |
|---|---|
| **Directory scanner** | Recursively reads DACL entries from every subdirectory |
| **Anomaly detection** | Flags Orphan SIDs, Everyone: Full Control, and Explicit Deny |
| **Snapshot & restore** | Save the current permission table to a `.neon` file, restore it anytime |
| **Display themes** | Default (light), Acrylic, and Aero Glass |
| **Bilingual UI** | Simplified Chinese and American English, switchable at runtime |
| **Status bar** | Live run status, privilege level, and execution state |
| **Native look** | Dark interface with optional acrylic or glass blur |
| **No installation** | Single `.exe`, no registry writes, no background services |
---

## System Requirements

- **Windows 10, version 22H2 (build 19045) or later**
  - Both x86 (32-bit) and x64 (64-bit) are supported
- **Windows 11, version 21H2 (build 22000) or later**
  - x64 (64-bit) only, as Windows 11 has no 32-bit edition
- **.NET Framework 4.x** (bundled with Windows, no download needed)
- **Administrator rights** recommended, though not strictly required

> Earlier Windows 10 versions (21H2 and older) are **not officially supported**. They may work, but are untested.

---

### Run

1. Right-click `AclInspector.exe` and choose **Run as administrator**.
2. If Windows SmartScreen shows a warning, click **More info → Run anyway**. The warning appears because the binary is not code-signed.
3. Click **Browse and Select** to pick a folder.
4. Click **Scan**.

> **Tip:** Do not scan `C:\` on your first attempt. Start with something small, such as `C:\Users\<YourName>\Documents`. Scanning an entire drive can take several minutes.

---

## Understanding the Results

Each row in the result list represents one access rule on one directory.

| Column | Meaning |
|---|---|
| **Path** | The folder the rule applies to |
| **Account** | The user or group the rule targets |
| **Type** | `Allow` or `Deny` |
| **Rights** | Permission bits, e.g. `Read`, `Write`, `FullControl` |
| **Inherited** | `Yes` if inherited from a parent folder, `No` if set directly |
| **Anomaly** | Problem flag — see below |

Rows flagged as anomalies are highlighted in red.

### Anomaly Types

- **Orphan SID** — The rule refers to an account that no longer exists on this system. Often left behind after a user was deleted, or carried over from an old drive.
- **Everyone: Full Control** — The built-in `Everyone` group has full control over this folder. A security risk on anything outside your personal folders.
- **Explicit Deny** — A deny rule is present. Deny rules take precedence over allow rules and often cause hard-to-diagnose access errors.

---

## Snapshots

A snapshot records the current permission table of every scanned directory so you can restore it later.

### Taking a snapshot

1. Scan a directory first. Snapshots only include directories scanned in the current session.
2. Click **Snapshot** at the bottom of the window.
3. Choose a save location and click **Save Snapshot to This Location**.

The snapshot is saved as a `.neon` file.

### Restoring a snapshot

1. Click **Snapshot** → **Have a Snapshot? Restore?**
2. Select a `.neon` file.
3. A new window shows:
   - The snapshot timestamp and version
   - A per-directory comparison between the snapshot and the current system
   - Which entries will actually change
4. Click **Overwrite System with This File** to apply.

> **Warning:** This operation modifies system permissions and cannot be undone. Take a fresh snapshot first if you are not certain.

### About `.neon`

`.neon` is a custom file format used exclusively by AclInspector. The file starts with a plain-text notice explaining how to obtain the correct interpreter; the actual snapshot data is encoded and is not readable by ordinary text editors or office software.

Opening a `.neon` file with AclInspector automatically enters the restore workflow.

---

## Display Themes

Click **Display** at the bottom of the window to switch visual themes:

| Theme | Description |
|---|---|
| **Default (Light)** | Plain light-colored interface. Recommended if visual effects cause issues on your system. |
| **Acrylic** | Dark translucent interface using the Windows Acrylic blur effect. Requires Windows 10 or 11. |
| **Aero Glass** | Dark translucent interface using the legacy Aero Glass API. Works on a wider range of systems than Acrylic. |

---

## Language

The interface supports **Simplified Chinese** and **American English**. Use the button in the top-right corner to switch at runtime. All labels, buttons, and tooltips update immediately. The default language is Simplified Chinese.

---

## Building from Source

You do **not** need Visual Studio, .NET SDK, or any additional tooling. Windows ships with a C# compiler inside the .NET Framework.

1. Install Python 3.x.
2. Run `ACL-installer.py`. This generates `AclInspector.cs` and `build.bat` in the current directory.
3. On Windows, double-click `build.bat`. It uses the built-in `csc.exe`.
4. The compiled `AclInspector.exe` appears in the same folder.

---

## FAQ

**Q: My antivirus flagged `AclInspector.exe`. Is it a virus?**
A: No. The binary is not code-signed, and antivirus tools frequently flag unsigned utilities that read file permissions. The full source code is included in the repository for review.

**Q: The window looks black and not transparent.**
A: The Acrylic blur effect is only available on certain Windows builds. Try switching the theme to **Aero Glass** or **Default** from the **Display** menu.

**Q: The result list is empty.**
A: Either the folder you selected has no unusual permissions, or the program could not read any permissions from it. Try a different folder, or run as administrator.

**Q: The scan is very slow.**
A: You likely selected a large folder. Try a smaller one. Closing the window will abort the scan.

**Q: Can I restore a snapshot on a different computer?**
A: Yes, but the account SIDs in the snapshot may not match those on the target machine. The restore window shows exactly which directories will be modified.

**Q: How do I uninstall it?**
A: Delete the folder. The program does not write to the registry and does not install anything on your system.

---

## License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for the full text.

---

## Credits

Created by **Neon2077-adb**. Contributions and issue reports are welcome.
