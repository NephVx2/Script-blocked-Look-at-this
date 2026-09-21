## Script blocked by PowerShell

Two different mechanisms can block a downloaded script. Follow the steps in order.

**1. Unblock the downloaded file**

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

**2. Allow script execution**

If the message says "running scripts is disabled on this system", the execution policy is the cause. Check it first:
```powershell
Get-ExecutionPolicy -List
```
Then allow scripts for your account only, without administrator rights:
```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```
Confirm with `Y`. With `RemoteSigned`, local scripts run normally, and downloaded ones must have been unblocked (step 1).

**3. Or run the script once, without changing anything**

```powershell
powershell -ExecutionPolicy Bypass -File .\ScriptName.ps1
```
The bypass only applies to this one run and doesn't change any system setting.

**4. Run as administrator if the script requires it**

Right-click PowerShell (or Windows Terminal) → **Run as administrator**, then `cd` to the script's folder before launching it. A script that touches security, drivers or networking will refuse to run without these rights, even once unblocked.

**Still blocked?**

- In `Get-ExecutionPolicy -List`, if `MachinePolicy` or `UserPolicy` is not `Undefined`, the policy is enforced by a Group Policy (work or school PC): you can't change it yourself, you need to ask the machine's administrator.
- If SmartScreen or Microsoft Defender shows a warning, click **More info** then **Run anyway**. If Defender quarantines the file, first make sure you downloaded it from the right repository.
- Double-clicking a `.ps1` opens it in Notepad instead of running it: launch it from a PowerShell console, or right-click → **Run with PowerShell**.

> Always read a script before running it, especially with `-ExecutionPolicy Bypass`.
