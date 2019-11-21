# Authentication Package Persistence
## Technique ID
T1131


## Description
Windows Authentication Package DLLs are loaded by the Local Security Authority (LSA) process at system start. They provide support for multiple logon processes and multiple security protocols to the operating system. Adversaries can use the autostart mechanism provided by LSA Authentication Packages for persistence by placing a reference to a binary in the Windows Registry location HKLM\SYSTEM\CurrentControlSet\Control\Lsa\ with the key value of "Authentication Packages"=<target binary>. The binary will then be executed by the system when the authentication packages are loaded.


## Hypothesis
Adversaries are using LSA Authentication Packages to maintain persistence in my environment.

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
| Sysmon | 13 | TargetObject | HKLM\SYSTEM\CurrentControlSet\Control\Lsa\ | [MITRE](https://attack.mitre.org/wiki/Technique/T1131), [Crysys](http://www.crysys.hu/skywiper/skywiper.pdf) |
| Sysmon | 13 | Details | "Authentication Packages" * | [MITRE](https://attack.mitre.org/wiki/Technique/T1131), [Crysys](http://www.crysys.hu/skywiper/skywiper.pdf) |

## Recommended Configuration(s)
| Title | Description | Reference|
|---------|---------|---------|
| authentication_package | Sysmon configuration | [T1131_authentication_package.xml](https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/blob/master/attack_matrix/windows/sysmon_configs/T1131_authentication_package.xml)


## Data Analytics 

| Analytic Type  | Analytic Logic | Analytic Data Object |
|--------|---------|---------|
|  Anomaly/Outlier |  event WHERE event_id == "13" AND details ==  ("Authentication Packages" *)  | [registry]\(TBD\) | 


## Hunter Notes
* msv1_0 value is the default authentication package.
* Look for values being set to that Key (if it is normal) and stack your results to see your outliers.


## Hunting Techniques Recommended

- [ ] Grouping
- [x] Searching
- [ ] Clustering
- [x] Stack Counting
- [ ] Scatter Plots
- [ ] Box Plots
- [ ] Isolation Forests
