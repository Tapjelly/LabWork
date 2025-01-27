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



# Week 5.2

