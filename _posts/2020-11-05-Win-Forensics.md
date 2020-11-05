---
title: "Windows Forensic Notes, Cheatsheet"
date: "2020-11-05"
layout: single
tags:
- DFIR
categories:
- Notes
---

Hi, good to see you again.

Let's jump to DFIR thingy where this note may help us in approaching suspected/infected Windows machine in DFIR manner. Here we go.

# Grabbing artifacts

Grabbing artifacts is the first and important stage when it come to digital forensic. I usually will use this three tools.

1. gkape
2. Triage IR
3. Redline Comprehensive

## gkape

Kroll Artifact Parser and Extractor (KAPE) is primarily a triage program that will target a device or storage location, find the most forensically relevant artifacts (based on your needs), and parse them within a few minutes. Because of its speed, KAPE allows investigators to find and prioritize the more critical systems to their case. Additionally, KAPE can be used to collect the most critical artifacts prior to the start of the imaging process. While the imaging completes, the data generated by KAPE can be reviewed for leads, building timelines, etc.

gkape is simple GUI for the KAPE that can help you quickly enumerate all the target and module options. You can choose variety of artifacts options meet your own disire. What you want, and what you not wanted to.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfirnote/2020-11-05-13-19-39.png)

## Triage IR

Triage IR is a Scripted collection of system information valuable to a Forensic Analyst. Instead of copying everything, collecting some key files can solve this issue.

IRTriage will collect:

- system information
- network information
- registry hives
- disk information, and
- dump memory.

One of the powerful capabilities of IRTriage is collecting information from "Volume Shadow Copy" which can defeat many anti-forensics techniques.

The IRTriage is itself just an autoit script that depend on other tools such as:

- Win32 | 64dd
- Sysinternals Suite
- The Sleuth Kit
- Regripper
- NirSoft => MFTDump and WinPrefetchView
- md5deep and sha1deep
- CSVFileView
- 7zip
- and some windows built-in commands.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfirnote/2020-11-05-13-25-36.png)

Just click `Select All` and hit `Run` then the tool will do his job.

## Redline comprehensive

A Redline Collector package contains an executable script to collect data from a potentially compromised endpoint. 

There are three types of Redline Collectors: 
- Standard Collector,
- Comprehensive Collector, 
- and IOC Search Collector (Windows only).

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfirnote/2020-11-05-13-29-05.png)

- `Standard Collector` = The Standard Collector configures scripts to gather the minimum amount of data to complete an analysis.
- `Comprehensive Collector` = The Comprehensive Collector configures scripts to gather most of the data that Redline collects and analyzes. Use this type of Redline Collector if you intend to do a full analysis or if you have only one opportunity to collect data from a computer.

For me, just choose the `Comprehensive Collector` as it give us full analysis and juicy info rather than using the `Standard` one.

How to create and run `Comprehensive Collector`?
1. Choose `Create a Comprehensive Collector`
2. Choose `Windows`
3. Check the box `Acquire Memory Image` to dump the memory image.
4. Choose the location of the `Comprehensive Collector` script and files to be save.
5. Copy the folder of the `Comprehensive Collector` to removable media.
6. On the evidence Windows machine you want to audit, run the `RunRedlineAudit.bat` script, preferably from removable media (e.g. a USB Hard Drive).

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfirnote/2020-11-05-13-37-40.png)

## Manual grab?

You guys can refer this wonderful and completed cheatsheet by Jai Minton, https://www.jaiminton.com/cheatsheet/DFIR/#

Also, below I attached the common artifacts' locations:

### Windows Event logs
| Artifact name/path | Description |
| --- | --- |
| `%system root%\System32\winevt\logs\ *`| Windows logs |

### Registry file and few other artifact

| Artifact name/path | Description |
| --- | --- |
| `C:\Windows\System32\config\*` | All registry files (Contain a lot of artifact) |

### File execution activities

| Artifact name/path | Description |
| --- | --- |
| `NTUSER.DAT` | UserAssist, Last-Visited MRU, RunMRU Start->Run |
| `SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache` | Windows Application Compatibility Database |
| `C:\%USERPROFILE%\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations ` |  Jump Lists |
|  `C:\Windows\Prefetch` |  Prefetch |
|  `C:\Windows\AppCompat\Programs\Amcache.hve` and `C:\Windows\AppCompat\Programs\RecentFilecache.bcf` |  Amacache.hve or RecentFileCache.bcf |


#### File Download activities

| Artifact name/path | Description |
| --- | --- |
| `NTUSER.DAT` | Dig into user activities like all of the mounted disks including USB thumb drives and many more |
| `%USERPROFILE%\AppData\Local\Microsoft\Outlook` | E-mail Attachments |
| `C:\%USERPROFILE%\AppData\Roaming\Skype\<skype-name>` | Skype History |
| `%USERPROFILE%\AppData\Local\Google\Chrome\UserData\Default\History` | Chrome Artifacts |
| `%userprofile%\AppData\Roaming\Mozilla\ Firefox\Profiles\<random text>.default\places.sqlite Table:moz_annos` | Firefox Artifacts |
| `%USERPROFILE%\AppData\Local\Microsoft\Windows\WebCache\WebCacheV*.dat` | IE artifacts |
| `%userprofile%\AppData\Roaming\Mozilla\ Firefox\Profiles\<random text>.default\downloads.sqlite` | Firefox downloads |
| `%USERPROFILE%\AppData\Local\Microsoft\Windows\WebCache\ WebCacheV*.dat` | IE Downloads |



### File/Folder Opening
| Artifact name/path | Description |
| --- | --- |
| `NTUSER.DAT`  | Open/Save MRU, Last-Visited MRU , Recent Files, Office Recent Files |
|  `USRCLASS.DAT` |  Shell Bags |
|`C:\%USERPROFILE%\AppData\Roaming\Microsoft\Windows\Recent\ ` or `C:\%USERPROFILE%\Recent` |  Shortcut (LNK) Files |
| `C:\%USERPROFILE%\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations ` |  Jump Lists |
|  `C:\Windows\Prefetch` |  Prefetch |

### Deleted File or File Knowledge

| Artifact name/path | Description |
| --- | --- |
| `NTUSER.DAT`  | XP Search – ACMRU, Search – WordWheelQuery, Last-Visited MRU,  |
| `C:\%USERPROFILE%\AppData\Local\Microsoft\Windows\Explorer` | Thumbscache |
| `C:\RECYCLER` | XP Recycle Bin |
| `C:\$Recycle.bin`  |  Win7/8/10 Recycle Bin |


### Other browser artifacts
| Artifact name/path | Description |
| --- | --- |
| Registry files | Timezone, Network History, |
|`%USERPROFILE%\AppData\Local\Microsof\Windows\INetCookies `| Cookies IE |
| `%USERPROFILE%\AppData\Roaming\Mozilla\Firefox\Profiles\<randomtext>.default\cookies.sqlite `| Cookies Firefox |
| `%USERPROFILE%\AppData\Local\Google\Chrome\UserData\Default\Local Storage `| Cookies Chrome |
| `%USERPROFILE%\AppData\Local\Microsoft\Windows\WebCache\WebCacheV*.dat `| IE Browser Search Terms |
| `%userprofile%\AppData\Roaming\Mozilla\Firefox\Profiles\<randomtext>.default\places.sqlite `|  Firefox Browser Search Terms|


### External device / USB device
| Artifact name/path | Description |
| --- | --- |
| `C:\Windows\inf\setupapi.dev.log` and `C:\Windows\setupapi.log` | First/Last Times of devices connected |
| Registry files | Key Identification,  First/Last Times, User, Volume Serial Number, Drive Letter & Volume Name |
|`C:\%USERPROFILE%\AppData\Roaming\Microsoft\Windows\Recent\ ` or `C:\%USERPROFILE%\Recent` |  Shortcut (LNK) Files |
| `System.evtx` | PnP Events | 

### Account usage
| Artifact name/path | Description |
| --- | --- |
| SAM in Registry files | Last login |
| SAM in Registry files | Last password change |
| Security event | Success/Fail Logons |
| Security event | RDP Usage |

### Browser Usage

| Artifact name/path | Description |
| --- | --- |
|`%USERPROFILE%\AppData\Local\Microsoft\Windows\WebCache\WebCacheV*.dat` | IE History |
|`%USERPROFILE%\AppData\Roaming\Mozilla\Firefox\Profiles\<random text>.default\places.sqlite` | Firefox History |
|`%USERPROFILE%\AppData\Local\Google\Chrome\User Data\Default\History` | Chrome History  |
|`%USERPROFILE%\AppData\Local\Packages\microsoft.microsoftedge_<APPID>\AC\MicrosoftEdge\Cache `| IE Cache |
| `%USERPROFILE%\AppData\Local\Mozilla\Firefox\Profiles\<randomtext>.default\Cache`| Firefox Cache |
| `%USERPROFILE%\AppData\Local\Google\Chrome\User Data\Default\Cache\ - data_# and f_######` | Chrome Cache |
| `%USERPROFILE%/AppData/Local/Microsoft/Internet Explorer/Recovery` | IE Session Restore |
| `%USERPROFILE%\AppData\Roaming\Mozilla\Firefox\Profiles\<randomtext>.default\sessionstore.js` | Firefox Session Restore |
| `%USERPROFILE%\AppData\Local\Google\Chrome\User Data\Default\` | Chrome Session Restore |


### Others

 1. Memory dump
 2. MFT