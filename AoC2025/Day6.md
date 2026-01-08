# Malware Analysis - Egg-xecutable 

**Q1)** **Static analysis: What is the `SHA256Sum` of the `HopHelper.exe`?**

- The `SHA256 sum` was located using `PEstudio`
- `F29C270068F865EF4A747E2683BFA07667BF64E768B38FBB9A2750A3D879CA33`


**Q2) Static analysis: Within the strings of `HopHelper.exe`, a flag with the format `THM{XXXXX}` exists. What is that flag value? Note, this can be found towards the bottom of the strings output.**

- In the strings section, I found this
- `THM{STRINGS_FOUND}`



**Q3) Dynamic analysis: What registry value has the `HopHelper.exe` modified for persistence? Note: Provide the full path of the key that has been modified**

- Used `Regshot` to first take snapshot of registry values before running the malware, and one snapshot are executing it.
- Compared both the shots and looked at values added.
- `HKU\S-1-5-21-1966530601-3185510712-10604624-1008\Software\Microsoft\Windows\CurrentVersion\Run\HopHelper`
![](day6_1.png)


**Q4) Dynamic analysis: Filter the output of `ProcMon` for `TCP` operations. What network protocol is `HopHelper.exe` using to communicate?**

- Filtered process name to `HopHelper.exe`
- Filtered Operation to `TCP Connect`
- `http`
