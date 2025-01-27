# Week 4.1

## Process Investigation

X to search cpu usage
M to search memory usage
User to search specific user
```console  
top
```
![image](https://github.com/Tapjelly/Linux_labs/blob/Images/Week3.1_img1.png)

**I have underlined the below questions related to the top command.**
How many tasks have been started on the host?
How many of these are sleeping?
Which process uses the most memory?
Review all the processes started by the root or sysadmin user.

## Installing Packages

```console  
echo "export PATH='/usr/games:$PATH'" >> .bashrc && source .bashrc
sudo apt -y install cowsay emacs
```

# Week 4.2

## Let's talk to John
```console
cd /home/sysadmin
sudo cp /etc/shadow copy_shadow
sudo nano copy_shadow
```
Within the nano screen we delete all lines that are not out target users.
```console
sudo john copy_shadow
```
![image](https://github.com/Tapjelly/Linux_labs/blob/Images/Week4.1_img1.png)
Here we use the John the Ripper tool to take our Username:PasswordHash and output it to plaintext.

## Sudo Wrestling

```console
Whoami
sudo -l
sudo grep less /etc/sudoers
sudo -lU max
sudo -lU max >> user_permissions
sudo su max
sudo less shopping_list.txt
! bash
ls -la /home/max
sudo su
grep less /etc/sudoers
sudo visudo
```
Within visudo we see all the sudo permissions available to our users. Inlcuding:
max ALL=(ALL) /usr/bin/less
This means our user "max" has root permissions when using less which is a preveledge escalation vulnerability. 
We will delete this line from the file.

## Users and Groups
```console
id billy >> ~/research/user_id_class.txt
groups billy
groups billy >> ~/research/user_groups_class.txt
sudo deluser –remove-home billy
sudo addgroup developers
sudo usermod -G developers billy
sudo delgroup hax0rs
sudo chgrp engineers /home/engineers/
```

# Week 4.3

## Permissions
```console
sudo chmod 600 /etc/shadow
sudo chmod 644 /etc/group
sudo chmod 600 /etc/gshadow
sudo getent shadow max
sudo grep “!” /etc/shadow
grep "x:0" /etc/passwd
sudo nano passwd
```
Using grep to search "!" in the shadow file will show us any user that does not have a password set.
Using grep to search "x:0" in the shadow file will show us any user that has the root UID.
We then use nano to edit that user to a UID of over 1000.

## Managing Services
```console
systemctl -t service --all
sudo systemctl stop xinetd
sudo systemctl disable xinetd
sudo apt remove xinetd
sudo systemctl status xinetd
```
In this lab we were tasked with killing services that were labeled insecure. I am only including the killing of xinetd.service (Telnet). In this lab I also killed vsftpd.service (FTP), apache2.service (HTTP), nginx.service (HTTP), dovecot.service (IMAP and POP3)

## Service Users
```console
grep “ftp\|dove” etc/passwd
sudo deluser --remove-all-files dovecot
sudo adduser --system --no-create-home tripwire
id tripwire
sudo visudo
```
In visudo we are adding tripwire ALL= NOPASSWD: /usr/sbin/tripwire under the Users section to give this user sudo priviledges.
```console
which tripwire
sudo chmod 700 /usr/sbin/tripwire
ls -l /usr/sbin/tripwire
```
