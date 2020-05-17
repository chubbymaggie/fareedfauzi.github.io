---
title: "Cynet Incident Response Challenge: Write-up"
date: "2020-05-12"
layout: single
tags:
- CTF
- DFIR
categories:
- Write-Up
---

Hello everyone. Last week, Cynet conducted a CTF DFIR-based where particapted user can test their own skills in Digital Forensics and Incident Response.

There are 3 categories splitted by levels. Basic, medium and advanced.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-12-17-08-19.png)

I played this CTF with Imran to improve our skill in DFIR. The submition can be only submit one attempt only where if your flag is wrong, you cannot submit the right flag again. So, we need to be careful and take a high consideration before submit the question.

The flag submit algorithm is quiet weird for me because we didnt know that is our flag is right or wrong when we submitted it for the first time. Once you submitted right or wrong answer, there is no thing such as tell us that our flag is right/wrong. So, below writeups are the challenges that I confident about the flag. 

If you want the dump file of the challenges, feel free to reach me. The zip file of all challenges is approx 7.58 GB.

So, let's start.

# Solved

## Time machine 

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-22-29-50.png)

**Solution:**

They gave us a `$MFT` file and we need to find the filename and time stamp as organizer tell us in the instruction.

After few minutes spending time about MFT forensic, I found this website good for us to read to solve the challenge, [https://www.1337pwn.com/using-ftk-imager-to-find-file-artifacts-in-master-file-table/](https://www.1337pwn.com/using-ftk-imager-to-find-file-artifacts-in-master-file-table/) .

The question tell us that the file are reside on the Desktop, so that could be one of our hint to find the file.

We then continue our research on how to parse the MFT without using hex viewer.

I found this tool named [analyzeMFT](https://github.com/dkovar/analyzeMFT). AnalyzeMFT is designed to fully parse the MFT file from an NTFS filesystemand present the results as accurately as possible in multiple formats.

So, as soon as it parsed and give us the result in `.csv`, we then fire up file with excel and find any files that reside in Desktop.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-12-17-23-58.png)

After investigate these few files in FTK imager, I found that data.txt is the most latest time had been modified. 

To find the time stamp of the creation time of the file in FTK, just follow the blog's tutorial above.

Highlight this part, and at the left side. You will see the creation time in UTC.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-12-17-31-03.png)

The flag is `Data.txt` and `19-01-2020 09:53:06`

Update: The right answer is Mod-File.txt. Alternatively, we can use MFTtoCSV tool to solve this challenge.


## Hello DoK

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-22-31-33.png)

**Solution:**

In this challenge, they gave us these few files:

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-12-17-36-35.png)

 The `amcache.hve` file contain information about application shortcuts, device containers, and more. Some of the useful bits of data that can be found through analysis of the amcache include device serial numbers, descriptions (e.g. FriendlyName-like values), volume names, VID/PID data, and more.  When a USB storage device is connected to a system, multiple subkeys in the amcache are created under Root\InventoryDevicePnp.  

The others file is registry files.

Because our goal is to find USB uid, then we can find it in `amcache.hve` file or `SYSTEM` registry files  file. Here, I will go for SYSTEM registry files to find the USB uid using AccessData Registry Viewer tool.

I refer this blog post [https://df-stream.com/2015/02/leveraging-devicecontainers-key](https://df-stream.com/2015/02/leveraging-devicecontainers-key/) and found out the serial UID for Sandisk cruzer is `4C530000281008116284&0` at `SYSTEM\ControlSet001\Enum\USBSTOR\Disk&Ven_SanDisk&Prod_Cruzer_Blade&Rev_1.00\`

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-12-17-48-28.png)

We can also find the UID in `Amcache.hve`

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-22-51-10.png)

The flag is `4C530000281008116284` without `&0`

Update: Alternatively, we can use tool named RegRipper to analyze the registry.


## Bling bling 

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-22-53-52.png)

**Solution:**

Soon we downloaded the files, they gave us a few files of `AutomaticDestinations-ms`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-23-10-49.png)

Files created under `\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations-ms`  are created automatically when a user interacts with the system performing such acts as opening applications or accessing files.

The actual Jump List items are contained within OLE containers that are essentially named for the application for which the relevant file was accessed.  The application ID is normally set by either the application, or the OS when the application is run.

This is my PC's `AutomaticDestinations-ms` files looks like in `Recent Items`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-23-10-31.png)

We then copy all the evidence files into our PC `Recent Items` and view the `AutomaticDestinations-ms` using nirsoft tool called `JumpListsView`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-23-11-07.png)

As we can see there are a few files that could potentially be the file organizer talking about. It's look like the finance file is `Stats.txt` in user Daenerys's Desktop.

I dont confident about the flag, and just assume that the flag is `Stats.txt` and `Daenerys`.

Update: The flag is `Varys-Secret.txt`, we should use `CustomDestinations` jumplists instead of `AutomaticDestinations-ms` jumplists.

## Is that you?

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-23-17-33.png)

**Solution:**

We got a memory dump file for this challenge. We can analyse this file using volatility.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-00-11-05.png)

As you can see at the red arrow. The `Isaas.exe` looks malicious becase the child process of this process are `cmd.exe` and `powershell.exe` which are common pattern of malware execution.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-00-14-12.png)

So the flag is PID `232` and PPID `912`


## B4 Catch

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-23-18-32.png)

**Solution:**

The organizer gave us prefetch files of the compromised windows. In the challenge's story, organizer tell us about malicious `svchost.exe` was executed.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-12-18-26-31.png)

We then copy all `svchost.exe` into our host's prefetch and view our host's prefetch using nirsoft winprefetch viewer.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-12-14-55-16.png)

As you can see above, `SVCHOST.EXE-AAD33B2E.pf` doesnt have path to `System32` and it could be the potential suspicious binary. The counter of execution is 0, therefore the flag is Number executions: `0` and Time stamp: `08-02-2020 17:59`

## Titan

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-23-24-04.png)

**Solution:**

Enumerating the folders and found the C2 IP in crontabs where the command try to execute netcat reverse shell to that IP with port 4443.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-23-42-28.png)

The flag is `17.71.29.75`


## LNK files 

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-23-24-57.png)


**Solution:**


After enumerate all the folders, we found the flag in `\littlefinger\AppData\Roaming\Microsoft\Windows\Recent`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-12-18-40-01.png)

The flag is `F1a9-AFNIEJFJSSE`



## Pss

**Question:**
![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-03-57-09.png)


**Solution:**

Open up the Windows Powershell event in Windows event viewer and we can found the IP by scrolling it one by one.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-04-00-52.png)

The flag is `104.248.32.159`

## Root

**Question:**
![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-03-57-24.png)


**Solution:**

We follow the instruction in the PDF and a .doc will be downloaded.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-04-08-39.png)

Then, we open macro viewer in the `.doc` file and we can see the payload.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-04-09-10.png)

Navigate to [https://raw.githubusercontent.com/niro095/DocX-Stealer/master/DxS.ps1](https://raw.githubusercontent.com/niro095/DocX-Stealer/master/DxS.ps1) in browser amd we can get the password at the link I labelled `4` in below picture.

Follow my number below to understand how I understand that this link [https://raw.githubusercontent.com/niro095/DocX-Stealer/master/Secret](https://raw.githubusercontent.com/niro095/DocX-Stealer/master/Secret) containing the password.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-04-30-13.png)

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-04-28-54.png)

Convert the numbers (decimal) to ascii and the flag is `FlaG_[W0N-C0NGr@T5]`


## Layers

**Question:**
![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-10-01-53-34.png)


**Solution:**

After a few hours checking out all `.csv` Autorunsc report, I found `PSEXESVC.exe` (commonly uses in lateral movement) was run based on Lannister Autorunsc report.

The flag is `Lannister` and `PSEXESVC.exe`


## Frog Find

**Question:**
![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-10-03-21-17.png)


**Solution:**

`volatility -f THEEYRIE.dmp --profile=Win10x64_17134 malfind`

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-10-03-21-54.png)

`volatility -f THEEYRIE.dmp --profile=Win10x64_17134 pstree`

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-10-03-23-32.png)

`volatility -f THEEYRIE.dmp --profile=Win10x64_17134 procdump -D dump/ -p 1996` and run `strings` into the executable and we can get the flag.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-10-03-24-45.png)

The flag is `Frog-FWGA142FS`

---

# Unsolved

## Sports 

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-08-23-24-30.png)



## Cant touch this


**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-00-34-09.png)

Update: Use Shellbags explorer to solve this

**Solution:**

They gave us Windows Event file.

I've research about event ID of creation of file. And these event ID `4656, 4663, 4660, 4658` can help us the flag.

But unfortunately, there is no findings I found in the Windows event log.



## Copy PasTe

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-00-32-40.png)

Update: Use ANJP tool



## whoami 

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-00-34-35.png)

Update: `python PyWMIPersistenceFinder.py ../OBJECTS.DATA` can solve the answer.


## Kiwi 

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-00-35-10.png)


**Source:**

https://www.cyberark.com/threat-research-blog/detecting-pass-the-hash-with-windows-event-viewer/



## Seashell

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-03-08-18.png)




## Sneak

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-03-09-07.png)

## Universal

**Question:**
![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-03-32-32.png)


**Source:**

Below is registry that often uses to achive persistence.

```
1)  HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\BootExecute
2)  HKLM\System\CurrentControlSet\Services  (start value of 0 indicates kernel drivers, which load before kernel initiation)
3)  HKLM\System\CurrentControlSet\Services (start value of 2, auto-start and 3, manual start via SCM)

4)  HKLM\Software\Microsoft\Windows\CurrentVersion\RunServicesOnce
6)  HKLM\Software\Microsoft\Windows\CurrentVersion\RunServices
12) HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ShellServiceObjectDelayLoad
13) HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
14) HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnceEx
15) HKLM\Software\Microsoft\Windows\CurrentVersion\Run

18) HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run
22) HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\SharedTaskScheduler (XP, NT, W2k only)

8)  HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
9)  HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Userinit
10) HKCU\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\\Shell
11) HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\\Shell
20) HKCU\Software\Microsoft\Windows NT\CurrentVersion\Windows\load
21) HKLM\Software\Microsoft\Windows NT\CurrentVersion\Windows
23) HKLM\Software\Microsoft\Windows NT\CurrentVersion\Windows\\AppInit_DLLs

```

## Notes

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-03-56-25.png)


## Meow

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-15-01-07.png)


## Insurance 

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-17-44-15.png)


## 2nd Base

**Question:**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/cynet-ir/2020-05-09-14-37-43.png)



---

Others writeups:
1. [https://malwarenailed.blogspot.com/2020/05/cynet-incident-response-challenge-2020.html?m=1](https://malwarenailed.blogspot.com/2020/05/cynet-incident-response-challenge-2020.html?m=1)