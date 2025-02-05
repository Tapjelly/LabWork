# Week 6.1
## Compound Commands
1. We will copy create a directory and copy our logs, hosts, shadow, and passwd files all in one command using &&. *note:* We use -p to check if the directory already exists to ensure we dont cause errors.
2. We will use the find command to locate all .txt files starting from the root directory and we will output it to text_files. Finally we want to tail that file all in one line.
3. Again we will use find to search for all files with the executable permission for all users. We will then output this to our exec_lst.txt file.
4. Next we use ps with awk to parse the data readably, we want to sort our data by the memory it uses, and we only want columms 1,2,3,4,11 to be sent to our new file.
5. Lastly, we will send a list of home to our new file, then cat the contents of passwd piping it to our awk which we will use -F to specify we are searching for ":" *which is used to separate user and passwords*. Since we only want users above 1000 we will specify that in our if condition and send all of this into our file.
```console
mkdir -p ~/research && cp -r /var/log/* /etc/passwd /etc/shadow /etc/hosts ~/research
file $(find / -iname '*.txt' 2>/dev/null) > ~/Desktop/text_files ; tail ~/Desktop/text_files
sudo find / -type f \( -perm -u=x -o -perm -g=x -o -perm -o=x \) > ~/research/exec_lst.txt
ps aux --sort -%mem | awk {'print $1, $2, $3, $4, $11'} | head -n 11 > ~/research/top_processes.txt
ls /home > ~/research/users.txt && cat /etc/passwd | awk -F ":"' {if ($3 >= 1000) print $0}' >> ~/research/users.txt
```

## Creating Aliases
Here we will create an alias and write it to our bashrc file. We must then source the file to update the alias in the system
```console
echo "alias lsa='ls -a'" >> ~/.bashrc && source ~/.bashrc
echo "alias docs='cd ~/Documents'" >> ~/.bashrc && source ~/.bashrc
echo "alias dwn='cd ~/Downloads'" >> ~/.bashrc && source ~/.bashrc
echo "alias etc='cd /etc'" >> ~/.bashrc && source ~/.bashrc
echo "alias logs='mkdir ~/research && cp /var/log/* /etc/passwd /etc/shadow /etc/hosts ~/research'" >> ~/.bashrc && source ~/.bashrc
echo "alias rr='source ~/.bashrc'" >> ~/.bashrc && source ~/.bashrc
```

## Sysadmin Bash Scripts
```console
touch sys_info.sh
chmod +x sys_info.sh
nano sys_info.sh
./sys_info.sh
```
sys_info.sh
```console
#!/bin/bash

echo "A Quick System Audit Script"
date
echo ""
echo "Machine Type Info:"
echo $MACHTYPE
echo -e "Uname info: $(uname -a) \n"
echo -e "IP Info: $(ip addr | grep inet | tail -2 | head -1) \n"
echo "Hostname: $(hostname -s) "
echo "DNS Servers: "
cat /etc/resolv.conf
echo "Memory Info:"
free
echo -e "\nCPU Info:"
lscpu | grep CPU
echo -e "\nDisk Usage:"
df -H | head -2
echo -e "\nWho is logged in: \n $(who -a) \n"
```


## Custom Commands
```console mkdir ~/research 2> /dev/null
echo -e "\nExecutable Files:" >> ~/research/sys_info.txt
find / -type f \( -perm -u=x -o -perm -g=x -o -perm -o=x \) >> ~/research/sys_info.txt
echo -e "\nTop 10 Processes" >> ~/research/sys_info.txt
ps aux -m | head -11 | awk {'print $1, $2, $3, $4, $11'} >> ~/research/sys_info.txt
nano sys_info.sh
./sys_info.sh
less ~/research/sys_info.txt
```
adding >> ~/research/sys_info.txt to our sys_info.sh script
```console
#!/bin/bash
mkdir ~/research 2> /dev/null
echo "A Quick System Audit Script" >> ~/research/sys_info.txt
date >> ~/research/sys_info.txt
echo "" >> ~/research/sys_info.txt
echo "Machine Type Info:" >> ~/research/sys_info.txt
echo $MACHTYPE >> ~/research/sys_info.txt
echo -e "Uname info: $(uname -a) \n" >> ~/research/sys_info.txt
echo -e "IP Info: $(ip addr | grep inet | tail -2 | head -1) \n" >> ~/research/sys_info.txt
echo "Hostname: $(hostname -s) " >> ~/research/sys_info.txt
echo -e "\nExecutable Files:" >> ~/research/sys_info.txt
find / -type f \( -perm -u=x -o -perm -g=x -o -perm -o=x \) >> ~/research/sys_info.txt
echo -e "\nTop 10 Processes" >> ~/research/sys_info.txt
ps aux -m | head -11 | awk {'print $1, $2, $3, $4, $11'} >> ~/research/sys_info.txt
```

# Week 6.2
## Variables and if Statements
```console
if [ ! -d $HOME/research ]
then
  mkdir $HOME/research
fi
```
Updating our sys_info.sh script to use variables
```console
#!/bin/bash
#Check if script was run as root. Exit if false.
if [ $UID -ne 0 ]
then
  echo "Please run this script with sudo."
  exit
fi
# Define Variables
output=$HOME/research/sys_info.txt
ip=$(ip addr | grep inet | tail -2 | head -1)
execs=$(find /home -type f -perm 777 2> /dev/null)
# Check for research directory. Create it if needed.
if [ ! -d $HOME/research ]
then
  mkdir $HOME/research
fi

# Check for output file. Clear it if needed.
if [ -f $output ]
then
  rm $output
fi

echo "A Quick System Audit Script" >> $output
date >> $output
echo "" >> $output
echo "Machine Type Info:" >> $output
echo -e "$MACHTYPE \n" >> $output
echo -e "Uname info: $(uname -a) \n" >> $output
echo -e "IP Info:" >> $output
echo -e "$ip \n" >> $output
echo -e "Hostname: $(hostname -s) \n" >> $output
echo "DNS Servers: " >> $output
cat /etc/resolv.conf >> $output
echo -e "\nMemory Info:" >> $output
free >> $output
echo -e "\nCPU Info:" >> $output
lscpu | grep CPU >> $output
echo -e "\nDisk Usage:" >> $output
df -H | head -2 >> $output
echo -e "\nWho is logged in: \n $(who -a) \n" >> $output
echo -e "\nexec Files:" >> $output
echo $execs >> $output
echo -e "\nTop 10 Processes" >> $output
ps aux --sort -%mem | awk {'print $1, $2, $3, $4, $11'} | head >> $output

```

## Lists and Loops
``` console
touch for_loops.sh
chmod +x for_loops.sh
nano for_loops.sh
```

```console
#!/bin/bash
# List of hosts to check
hosts=(
  "8.8.8.8"
  "8.8.4.4"
  "1.1.1.1"
)
# List of ports to check
ports=(
  "80"
  "443"
)

  # Loop through each host
for host in "${hosts[@]}"; do
  echo "Checking connectivity to host: $host"
  # Check if host is reachable
  if ping -c 1 "$host" &> /dev/null; then
    echo "Host $host is reachable."
    # Nested loop to check each port on the current host
    for port in "${ports[@]}"; do
      if nc -z -w 1 "$host" "$port" &> /dev/null; then
        echo "Port $port is open on host $host."
      else
        echo "Port $port is NOT open on host $host."
      fi
    done
  else
    echo "Host $host is NOT reachable."
  fi

  # Add a blank line for readability
  echo
done
```
bonus
```console
execs=$(find /home -type f -perm 777 2> /dev/null)
for exec in ${execs[@]}
do
  echo $exec
done
```
## Sysadmin Loops

The final edit to sys_info.sh
```console
#!/bin/bash

# Check if script was run as root. Exit if false.
if [ $UID -ne 0 ]
then
  echo "Please do not run this script as root."
  exit
fi

# Define Variables
output=$HOME/research/sys_info.txt
ip=$(ip addr | grep inet | tail -2 | head -1)
execs=$(sudo find /home -type f -perm 777 2> /dev/null)
cpu=$(lscpu | grep CPU)
disk=$(df -H | head -2)

# Define Lists to use later
commands=(
  'date'
  'uname -a'
  'hostname -s'
)
files=(
  '/etc/passwd'
  '/etc/shadow'
)

# Check for research directory. Create it if needed.
if [ ! -d $HOME/research ]
then
  mkdir $HOME/research
fi

# Check for output file. Clear it if needed.
if [ -f $output ]
then
  > $output
fi
##################################################
# Start Script
echo "A Quick System Audit Script" >> $output
echo "" >> $output

for x in {0..2};
do
  results=$(${commands[$x]})
  echo "Results of \"${commands[$x]}\" command:"
  echo $results
  echo ""
done

# Display Machine type
echo "Machine Type Info:" >> $output
echo -e "$MACHTYPE \n" >> $output

# Display IP Address info
echo -e "IP Info:" >> $output
echo -e "$ip \n" >> $output

# Display Memory usage
echo -e "\nMemory Info:" >> $output
free >> $output

# Display CPU usage
echo -e "\nCPU Info:" >> $output
lscpu | grep CPU >> $output

# Display Disk usage
echo -e "\nDisk Usage:" >> $output
df -H | head -2 >> $output

# Display login information for the current user
echo -e "\nCurrent user login information: \n $(who -a) \n" >>
$output

# Display DNS Info
echo "DNS Servers: " >> $output
cat /etc/resolv.conf >> $output

# List exec files
echo -e "\nexec Files:" >> $output
for exec in $execs;
do
  echo $exec >> $output
done

# List top 10 processes
echo -e "\nTop 10 Processes" >> $output
ps aux --sort -%mem | awk {'print $1, $2, $3, $4, $11'} | head >>
$output

# Check the permissions on files
echo -e "\nThe permissions for sensitive /etc files: \n" >> $output
for file in ${files[@]};
do
  ls -l $file >> $output
done
```


