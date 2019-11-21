# Bypass Application Whitelisting with InstallUtil.exe
## Technique ID
T1118


## Description
The Installer tool is a command-line utility that allows you to install and uninstall server resources by executing the installer components in specified assemblies. This tool works in conjunction with classes in the System.Configuration.Install namespace [Source](https://docs.microsoft.com/en-us/dotnet/framework/tools/installutil-exe-installer-tool). 

InstallUtil is located in the .NET directory on a Windows system: C:\Windows\Microsoft.NET\Framework\v<version>\InstallUtil.exe.
InstallUtil.exe is digitally signed by Microsoft. Adversaries may use InstallUtil to proxy execution of code through a trusted Windows utility. InstallUtil may also be used to bypass process whitelisting through use of attributes within the binary that execute the class decorated with the attribute \[System.ComponentModel.RunInstaller(true)] [Source](https://attack.mitre.org/wiki/Technique/T1118).

| Argument | Description |
|--------|---------| 
| assemblyname | The name of the assembly in which to execute the installer components. |

| Option | Description |
|--------|---------| 
| /h\[elp] | Displays command syntax and options for the tool. |
| /help assemblypath | Displays any additional options recognized by individual installers within the specified assembly. |
| /? | Displays command syntax and options for the tool. |
| /? assemblypath | Displays any additional options recognized by individual installers within the specified assembly. |
| /LogFile=\[filename] | Specifies the name of the log file where install progress is recorded. The default is assemblyname.InstallLog. |
| /AssemblyName assemblyName | Specifies the name of an assembly. The assembly name must be fully qualified with the version, culture, and public key token of the assembly. The fully qualified name must be surrounded by quotes. |
| \[,Version=major.minor.build.revision] \[,Culture=locale] \[,PublicKeyToken=publicKeyToken]] | For example, "myAssembly, Culture=neutral, PublicKeyToken=0038abc9deabfle5, Version=2.0.0.0" is a fully qualified assembly name. |
| /LogToConsole={true \| false} |If true, displays output to the console. If false (the default), suppresses output to the console. |
| /ShowCallStack | Prints the call stack to the log if an exception occurs at any point during installation. |
| /u\[ninstall] | Uninstalls an assembly. Unlike other options, /u applies to all assemblies regardless of where it appears on the command line. |


## Hypothesis
Adversaries might be bypassing application whitelisting controls within my organization by using InstallUtil.exe.

## Attack Simulation

| Script  | Short Description | Author | 
|---------|---------|---------|
| [InstallUtil.exe](https://github.com/redcanaryco/atomic-red-team/blob/a0bda9537a3486c3a0fa826419772ebcfb8cc619/atomics/T1118/T1118.md#atomic-test-1---installutil-uninstall-method-call)| Adversaries may use InstallUtil to proxy execution of code through a trusted Windows utility. | [atomic-red-team](https://github.com/redcanaryco/atomic-red-team/blob/a0bda9537a3486c3a0fa826419772ebcfb8cc619/atomics/T1118/T1118.md#atomic-test-1---installutil-uninstall-method-call) |

## Recommended Data Sources

| ATT&CK Data Source | Event Log |
|---------|---------|
|Process Monitoring| Sysmon|
|Process Monitoring|WinEvent| 


## Specific Events

| Source | EventID | EventField | Details | Reference | 
|--------|---------|-------|---------|-----------|
| Sysmon | 1 | Image or ParentImage | InstallUtil.exe | [Subtee]((https://twitter.com/subTee/status/896904055028461568)) | 
| Sysmon | 1 | CommandLine | "/logfile= /LogToConsole=false /U" | [Subtee](https://twitter.com/subTee/status/896904055028461568) |


## Recommended Configuration(s)
| Title | Description | Reference|
|---------|---------|---------|
| InstallUtil.exe | Sysmon Configuration | [T1118\_installutil.xml](https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/blob/master/attack_matrix/windows/sysmon_configs/T1118_installutil.xml)

## Data Analytics 

| Analytic Type  | Analytic Logic | Analytic Data Object |
|--------|---------|---------|
| Situational Awareness| process WHERE process\_name == "InstallUtil.exe" AND process\_command\_line == "*" | [process](https://github.com/bfuzzy/OSSEM/blob/master/detection_data_model/data_objects/process.md) | 


## Hunter Notes
* You could start looking for the command line arguments showed by Subtee.
* If the use of InstallUtil.exe is common within your environment, then it would be good to start stacking the values of what gets executed by InstallUtil.exe.


## Hunting Techniques Recommended

- [ ] Grouping
- [x] Searching
- [ ] Clustering
- [X] Stack Counting
- [ ] Scatter Plots
- [ ] Box Plots
- [ ] Isolation Forests
