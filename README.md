# pslinuxadmsedawk

## 1. getting started
### 2 An Introduction to grep
```
grep ^user /etc/passwd    #line starting with user
```
### 3 Introducing sed
```
sed -n 'p' /etc/passwd   # print matching line and duplicate
sed -n 'p' /etc/passwd   # print matching line
```
```
sed -n '1 p' /etc/passwd  # only print 1st line. without -n, will print all.
sed -n '1,5 p' /etc/passwd  # print line 1 to line 5
sed -n ' /^user/ p' /etc/passwd  # print startwith user
sed -i.bak '/^$/ d' /etc/passwd    # delete empty line
sed -i.bak '/^#/ d ; /^$/ d' /etc/passwd    # delete empty line and comment line
```
### 4 DIY 'r' Us Welcome awk
get version
```
ask -W version
```

note the space
```
awk ' BEGIN { print "ntp.conf" } { print } END { print NR } ' /etc/ntp.conf
```

print fields with string match
```
ifconfig eth1 | awk -F":" '/string/{print toupper($2 $3)}'
```


## 3. Regular Expressions
### 4 Identify Boundaries with Strings
word boundaries = space , -  
\s = space,empty line...

### 6 Building an Application Process toValidate Employee Records
```
grep -vE '\b[0-9]{3}-[0-9]{2}-[0-9]{4}\b' text
```


## 4. Fundamentals of sed
### 2 Print Command
-n : supress standard output so only matched lines apply  
```
sed -n '1,5 p" file   #print 1-5
sed -n '5,$ p" file   #print 5-last
```
### 3 Introducing the Substitute Feature
```
sed '1,3 s/old/new/' file
sed '/^root/ s@/bin/bash@/bin/sh@ ' file          # change delimiter, the first character following the s  represents the delimiters
```
### 4 Using the Substitute Feature
print line number
```
nl filename
```
```
sed '6,9 s/^/   /g' file     #sub line 6-9 prepend space
```

### 5 Insert,Append, and Delete
insert = prepend (insert newline before a line)
```
sed ' /^root/ i newline' filename
sed ' /^root/ i newline' filename
```
a =append
```
sed ' /^server 3/ a anewline' /etc/ntp.conf
```

### 7 Using Multiple Expressions Within sed
inline
```
sed '{/^server 0/ i newline /^server\s[0-9]\.ub/ d }' /etc/conf
```
code reuse
func.sed
```
/^root/ i newline
/^server/ d
```
call it
```
sed -f func.sed filename
```

__Nothing is written to the file unless the -i option is used__
### 7 Remote Edits Using sed Over with ssh
```
scp ntp.sed user@192.168.0.1:/tmp/
ssh -t user@server sudo sed -i.bak -f /tmp/func.sed /remote/file    # -t means assign a TTY allowing for sudo password
```

## 5. subsitiution grouping with sed
### 1 Introducing Substitution Groups
```
sed 's@\([^,]*\),\([^,]*\)@\U\1\L\2@' file          //sub first group with uppercase, second with lowercase
```

### 2 Understanding Numerical Groups
in sed, all parentheses need to be escaped
```
s/\(^\|[^0-9.]\)\([0-9]\+\)/\1\2/g
```
sed can be applied to all files in a folder
```
sed -f func.sed folder/*
```

## 6. Execute Commands with sed
### 1.Introduction
we have a file list,content is
```
/etc/hosts
```
we can execute
```
sed 's/^/ls -l \e' list      //must have a space after -l
```

```
sed ' /^\// s/^/tar -rf new.tar /e' cat.lise
```

we have a file user,content is
```
user1
user2
```
we can execute
```
sed 's/^/sudo useradd \e' user     //sudo useradd user1,sudo useradd user2....
```

```
tar -tf x.tar  //see names of tar file
```

### 3. Using sed Within vim
```
%s/old/new/g       //all doc
2,3s/old/new/g     //2-3line
```
multireplace
```
/^stat/s/^/  /             //replace, add few spaces when line starts from stat

5,7s/^/  /      //line5-7
```
### 4. Reading and Writing to Files
in file 1:
```
4,10 w while
```
in file 2:
```
r while
```

## 7. Fundamentals of awk
### 1 Introduction to awk
user.awk (a function)
```
BEGIN {FS=":";print"header"}          # awk -F":"
{print $1}
END {print "total user =" NR}
```
call this function
```
awk -f user.awk /etc/passwd
```

other function:
add a condition:
```
BEGIN {FS=":";print"header"}          
$3 > 300 {print $1}           #only print UID > 300
END {print "total user =" NR}
```

Regex+counter
```
BEGIN {FS=":";print"header"}          
/^root/{print $1;count++}           #do not initialize count
END {print "total user =" count}    #print count
```
### 3 The Employee Application
```
BEGIN {FS=":";print"header"}          
$3 > 300 {print toupper($1)}           #toupper function
END {print "total user =" NR}
```
```
BEGIN {FS=":";print"header"}          
!(/^root/||/^bob/){print $1;count++}           #Select all lines not starting from root or bob
END {print "total user =" count}    #print count
```
NF number of fields
```
BEGIN{
printf "%8s %11s\n","Username","Logindate"
print "============"
}
!(/Never logged in/||/^Username/||/^root/){
count++
if(NF==8)
printf "%8s %2s %3s %4s\n",$1,$5,$4,$8
else
printf "%8s %2s %3s %4s\n",$1,$6,$5,$9
}
END{
print "total users" ,count
}
```
check user log
```
lastlog -u root
lastlog -b 90  //last record before 90 day
```
call:
```
lastlog | awk -f lastlog.awk
```
## 8. Displaying Records from Flat Files Using awk
let's see sed version. Delete all empty line, and append newline after </Virtualhost> /a means append.  \ means new line
```
sed ' /^\s*$/d;/^<\/Virtual/a \ ' host.conf
```
now is awk version  RS = record seperator, ~ means search
```
BEGIN {RS="\n\n";}    #only print until current paragraph ends,  use \n\n as `field seperator`
$0 ~ search {print }
```
#### 2:40
use
```
awk -f virtual.awk search=keywordyouchoose virtual.conf
```
### 6 Using Multiple Field Seperator, the new application release
OFS = output seperator, for example print $2 $3   then OFS will print between $2 and $3
```
BEGIN {FS="[><]"; RS="\n\n";OFS=" ";}  # >< are seperators
$0 ~ search {print }
```

## 9. Analyze Web Logs with awk
### 3 Creating Summary with sed and awk
#### 1:00
count.awk
```
BEGIN{FS=" ";print "log access:"}
{ip[$1]++}
END{for(i in ip)
print i , " has accessed ",ip[i], " times."
}
```

get most popular browser:(add a simple get max function)
brower.awk
```
BEGIN{FS=" ";print "Most polular browser:"}
{browser[$13]++}
END{for(i in browser)

if(max<browser[i]){
max=browser[i];
maxbrowser=i;
}
print maxbrowser, " and " , max
}
```
