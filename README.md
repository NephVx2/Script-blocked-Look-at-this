# Script blocked by PowerShell

🇫🇷 [Version française](README_POWERSHELL_FRENCH.md)

Two different mechanisms can block a downloaded script. Follow the steps in order.

## Before you start: go to the script's folder

Every command below uses `.\` (the current folder), so PowerShell must be pointed at the folder that contains the script. Open PowerShell (as Administrator if the script requires it), then move there with `cd`:

```powershell
cd "$HOME\Downloads"
```

Replace the path with the folder where you saved the script, for example `cd "D:\Scripts"`. Keep the quotes if the path contains spaces. `cd` also switches drives in PowerShell. To check you are in the right place, run `dir`: the `.ps1` file should be listed.

> Shortcut: in File Explorer, open the folder, click the address bar, type `powershell` and press Enter. A PowerShell window opens directly in that folder (not as Administrator: if the script requires it, use the method above instead).

## 1. Unblock the downloaded file

Windows flags every file that comes from the Internet (the "Mark of the Web"). PowerShell then refuses to run it with a message like "is not digitally signed".

- Right-click the `.ps1` → **Properties** → tick **Unblock** at the bottom of the General tab → **OK**.
- Or in PowerShell, from the script's folder:
  ```powershell
  Unblock-File .\ScriptName.ps1
  ```
- If the script came in a `.zip` archive, unblock the `.zip` **before** extracting it, otherwise every extracted file inherits the block. To unblock a folder that's already extracted:
  ```powershell
  Get-ChildItem -Recurse | Unblock-File
  ```

## 2. Allow script execution

If the message says "running scripts is disabled on this system", the execution policy is the cause. Check it first:

```powershell
Get-ExecutionPolicy -List
```

Then allow scripts for your account only, without administrator rights:

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

Confirm with `Y`. With `RemoteSigned`, local scripts run normally, and downloaded ones must have been unblocked (step 1).

## 3. Or run the script once, without changing anything

```powershell
powershell -ExecutionPolicy Bypass -File .\ScriptName.ps1
```

The bypass only applies to this one run and doesn't change any system setting.

## 4. Run as administrator if the script requires it

Right-click PowerShell (or Windows Terminal) → **Run as administrator**, then `cd` to the script's folder before launching it. A script that touches security, drivers or networking will refuse to run without these rights, even once unblocked.

## Still blocked?

- In `Get-ExecutionPolicy -List`, if `MachinePolicy` or `UserPolicy` is not `Undefined`, the policy is enforced by a Group Policy (work or school PC): you can't change it yourself, you need to ask the machine's administrator.
- If SmartScreen or Microsoft Defender shows a warning, click **More info** then **Run anyway**. If Defender quarantines the file, first make sure you downloaded it from the right repository.
- Double-clicking a `.ps1` opens it in Notepad instead of running it: launch it from a PowerShell console, or right-click → **Run with PowerShell**.
- `The term '.\ScriptName.ps1' is not recognized as the name of a cmdlet, function, script file, or operable program`: you are not in the script's folder (see [Before you start](#before-you-start-go-to-the-scripts-folder)), or the file name is misspelled.

> Always read a script before running it, especially with `-ExecutionPolicy Bypass`.
