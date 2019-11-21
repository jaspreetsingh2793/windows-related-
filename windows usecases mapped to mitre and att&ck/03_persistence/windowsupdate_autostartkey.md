# Windows Update Service AutoStart Key
## Technique ID
T0000\_windowsupdate\_autostart


## Description
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Setup\ServiceStartup has two interesting properties:
* 'CacheFile': It can be used as a persistence mechanism
* 'TargetFile': It can also mysteriously make files re-appear on the system as the mechanism is used to update files and as such, the entries are being used to copy files during the system start (by the ‘svchost.exe -k netsvcs’ process) 

Older versions of Windows (XP) allow you to use the '20MUFixUp' entry to copy the file from the CacheFile location to the TargetFile one and execute it. All it takes is a restart of Windows Update Services for this to work. In newer versions of Windows (Tested successfully on Win 7), you need to use the 'RegistrationFlags' entry and set its value to either '1' or '2'.
* 'DllInstall' = dword:00000001
* 'DllRegisterServer' = dword:00000002

### Examples:
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Setup\ServiceStartup\malware.dll
"RegistrationFlags"=dword:00000001
"CacheFile"="C:\\test\\malware.dll"
"TargetFile"="C:\\test\\malware.dll"

HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Setup\ServiceStartup\malware.dll
"RegistrationFlags"=dword:00000002
"CacheFile"="C:\\test\\malware.dll"
"TargetFile"="C:\\test\\malware.dll"


## Hypothesis
Adversaries might be using the Windows Update Service Autostart key to maintain persistence in my environment.

## Attack Simulation

| Script  | Short Description | Author | 
|---------|---------|---------|
| \[TBD\](TBD)| TBD | \[TBD\](TBD) |



## Recommended Data Sources

| ATT&CK Data Source | Event Log |
|---------|---------|
|Registry Monitoring| Sysmon|



## Specific Events

| Source | EventID | EventField | Details | Reference | 
|--------|---------|-------|---------|-----------| 
| Sysmon | 13 | TargetObject | "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Setup\ServiceStartup" AND ("RegistrationFlags" OR "20MUFixUp") | [@hexacorn](http://www.hexacorn.com/blog/2017/03/18/beyond-good-ol-run-key-part-60/) |
| Sysmon | 13 | Details | DWORD AND (0x00000001 OR 0x00000002) | [@hexacorn](http://www.hexacorn.com/blog/2017/03/18/beyond-good-ol-run-key-part-60/) |
| Sysmon | 13 | TargetObject | "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Setup\ServiceStartup" AND CacheFile AND TargetFile | [@hexacorn](http://www.hexacorn.com/blog/2017/03/18/beyond-good-ol-run-key-part-60/) |
| Sysmon | 13 | Details | *.dll OR *.exe (Basic examples) | [@hexacorn](http://www.hexacorn.com/blog/2017/03/18/beyond-good-ol-run-key-part-60/) |

## Recommended Configuration(s)
| Title | Description | Reference|
|---------|---------|---------|
| windowsupdate\_autostart | Sysmon configuration | [T0000\_windowsupdate\_autostart.xml](https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/blob/master/attack_matrix/windows/sysmon_configs/T0000_windowsupdate_autostart.xml)



## Data Analytics 

| Analytic Type  | Analytic Logic | Analytic Data Object |
|--------|---------|---------|
| Anomaly/Outlier |  TBD  | [registry]\(TBD\) |


## Hunter Notes
* For Windows 7 systems, look for RegistrationFlags entries. This persistence technique does not work on Windows 10.
* You can group those events to hunt for this persistence technique or stack the values of the CacheFile AND TargetFile properties to find anomalies/outliers (That way we do not depend on specific file names).


## Hunting Techniques Recommended

- [x] Grouping
- [ ] Searching
- [ ] Clustering
- [x] Stack Counting
- [ ] Scatter Plots
- [ ] Box Plots
- [ ] Isolation Forests
