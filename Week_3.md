# Week 3.1

## Take Five and Practice the Command Line

```console  
cd /03-student/day1/take_5/  
mkdir Internal_Investigation_Employee_A  
cd Internal_Investigation_Employee_A  
pwd  
touch email_evidence log_evidence web_evidence  
rm web_evidence  
ls  
clear  
```

## Find your Milky Way

```console  
cd /03-student/day1/take_5/  
mkdir Internal_Investigation_Employee_B  
mv /03-student/day1/take_5/Internal_Investigation_Employee_A/email_evidence /03-student/day1/take_5/Internal_Investigation_Employee_B/email_evidence  
cp /03-student/day1/take_5/Internal_Investigation_Employee_A/log_evidence /03-student/day1/take_5/Internal_Investigation_Employee_B/log_evidence  
ls -r  
```

# Week 3.2

## Oh Henry, What Did You Do?

```console  
cd /03-student/day1/oh_henry/  
cd henry  
head File1 File2 File3  
rm File2mc  
mv File1 file1-10_11_2019  
mv File2 file1-10_11_2019  
```

##  Internal Investigation—Find the Kit cat Burglar

```console  
cd find_kit_cat_burglar/  
mkdir Evidence_for_authorities  
cd henry/emails  
cp * /03-student/day1/find_kit_cat_burglar/Evidence_for_authorities  
cp * /Evidence_for_authorities  
cat * >> Candy-evidence.txt  
```

## Learn New Commands

```console
cd /03-student/day2/learning_new_commands/
wc -w * >> Connections_by_website
cat Connections_by_website
```

# Week 3.3

## Warm-up
```console
cd /03-student/day2/warmup/
ls
mkdir additional_evidence
cd physical_access_logs/
head physical1 physical2 physical3 physical4 physical5 
cat physical4 physical5 > Physical_Access_evidence
mv /03-student/day2/warmup/physical_access_logs/Physical_Access_evidence /03-student/day2/warmup/additional_evidence
```

## Find You Way
```console
cd /03-student/day2/finding_your_way/PeanutButtery.net/
find -type d -iname *secret*
find -type f -iname *recipe*
find -type f -iname *recipe* -a -iname *peanut*
```

## grep
```console
cd /03-student/day2/
cd finding_your_way/
cd PeanutButtery.net/other/disregard/candysecretrecipes
grep -i guavaberries *
grep -i 'guavaberries\|optional' *
```

## Gath Evidence
```console
cd /03-student/day2/Gathering_Evidence/
mkdir Sugar_evidence
cd email
grep -il sugar * > sugar_email_evidence
grep -il sugar * | wc -l >> sugar_email_evidence
cd web_logs
grep -il 13.16.23.234 * > sugar_web_evidence
grep -i 13.16.23.234 * | wc -l >> sugar_web_evidence
cd ../../
mv ./Gathering_Evidence/email/sugar_email_evidence ./Gathering_Evidence/Sugar_evidence/
mv ./Gathering_Evidence/web_logs/sugar_web_evidence ./Gathering_Evidence/Sugar_evidence/
cd Gathering_Evidence/Sugar_evidence/
cat sugar_email_evidence sugar_web_evidence > Sugar_evidence_for_authorities
cat Sugar_evidence_for_authorities
```
