# Week 7.1

## Task Manger and CMD
We ended processes: Apple Push, iCloud Services, iPod Service, iTunesHelper, Skype, Apple Push, and TeamViewer
We disabled on startup everything except for Microsoft OneDrive and Windows Security notification icon

```console  
echo Bashlining Reporting > report.txt
type report.txt
echo Created by Matthew >> report.txt
echo %OS% system report created on %date% with logged in user %user% >> report.txt       
type report.txt                                                                
dir "\Program Files" >> report.txt
dir "\Program Files (x86)" >> report.txt
Type report.txt
```

## Users, Groups, and Password Policies
```console
net user
Net user Alex >> report.txt
Net localgroup >> report.txt
Net accounts >> report.txt
Type report.txt
```

## Creating Users and Setting Passwrod Policies
```console
Net user alex Ilovesales123! /add
Net user andrew Ilovedevelopment123! /add
Net localgroup administrators andrew /add
Net user andrew
Net accounts - after changing in gpedit
```

# Week 7.2
## Move and Create Directories
```console
Set-Location c:\
move-item -path "c:\users\alex\desktop\contracts" -destination "c:\"
New-item "Logs", "Backups", "Scripts" -itemtype "directoy" -force
rename-item -path "c:\contracts" -newname "Contracts"
Get-childitem
```
## Generating Windows Event Log Files with Parameters and Pipelines
```console
get-winevent -logname security -maxevents 100 | convertto-json | out-file -filepath "c:\logs\RecentSecurityLogs.json"
get-winevent -logname application -maxevents 100 | convertto-json | out-file -filepath "c:\logs\RecentApplicationLogs.json"
```

## Removing Unnecessary Packages with PowerShell
We will use windows powershell ISE to edit our script
```console
Set-Location C:\Users\azadmin\Documents\Activity\choco\
Get-childitem
./removepackages.ps1
```
**removepackages.ps1**
```console
$csv = Import-Csv -path .\chocoactivity.csv
foreach ($package in $csv) {
	choco uninstall -y $package.name
	write-output $package.name removed!
}
```
# Week 7.3
## Create Domain OUs, Users, and Groups
This one was just using the UI of Server manager > Tools > Active Directory Users and Computers. Because it was so straight forward I am not posting images or a walkthrough.
Using the UI you just add the OU and sub-OU. Add the users and groups. Assign the users to the groups.
There was a bonus of moving the computer to a new computer OU.

## Create Group Policy with Group Policy Objects
This one was just using the UI of Server manager > Tools > Group policy manager. I created a GPO named Limit Settings. The policy was User Configuration > Policies > Administrative Templates > Control Panel called Settings Page Visibility.
Under Settings Page Visibility, enter showonly:about;themes.
Then I needed to add the Sales group to the Remote Desktop Users group in the Active Directory Users and Computers menu.
From here I log in as system admin and gpupdate to update the new policy. 
When logged in as the user I have limited access to settings.

# Week 7.4
## Task Scheduling
Use the user, azadmin, to create a newly scheduled task. <br>
Launch the Task Scheduler GUI application from the Start menu.<br>
Create a new task.<br>
Navigate to the scheduling section.<br>
Schedule the task to execute every day.<br>
Make sure the time is set to execute after a minute or two from your current time (so that we can make sure it executes properly).<br>
Save your changes to the schedule.<br>

## Invesitgation User Space and Services
Open Computer Management and navigate to Local Users and Groups. Identify accounts with elevated permissions and propose adjustments.<br>

Next, using file explorer we will investigate common malware hiding spots. <br>
C:\Windows\Tasks<br>
C:\Windows\Temp<br>
C:\Users\<username>\AppData<br>

Using Task Manager and sorting processes by CPU usage. <br>
Note the top 5 highest-consuming processes. <br>
Cross-reference their names and details online to confirm legitimacy. <br>
Use tools like Process Explorer for deeper insights.<br>

Open the Services tool and identify services with unclear or unfamiliar names. 
Google them to validate their purpose and origin. 
For any suspicious services, document the service name, description, and executable path.

