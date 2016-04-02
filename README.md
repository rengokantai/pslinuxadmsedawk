#### pslinuxadmsedawk

#####getting started
######sed
```
sed -n '1 p' /etc/passwd  //only print 1st line. without -n, will print all.
sed -i.bak '/^$/d' /etc/passwd 
```
######awk
get version
```
ask -W version
```
#####regular ex
######identify boud
word boundaries = space , -  
\s = space,empty line...


#####fundamental sed
######intro sed
-n : supress standard output so only matched lines apply  
```
sed -n '1,5 p" file   #print 1-5
sed -n '5,$ p" file   #print 5-last
```
######substitute
```
sed '1,3 s/old/new/' file
sed '/^root/ s@/bin/bash@/bin/sh@ ' file          ##change delimiter
```
######using substitute
print line number
```
nl filename
```
```
sed '6,9 s/^/   /g' file     #sub line 6-9 prepend space
```

######insert,append delete
insert = prepend (insert newline before a line)
```
sed ' /^root/ i newline' filename
sed ' /^root/ i newline' filename
```


######multiple exp
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
######remote edits with ssh
```
ssh -t user@server sudo sed -i.bak -f /remote/func.sed /remote/file
```

#####subsitiution grouping with sed
######introducing
```
sed 's@\([^,]*\),\([^,]*\)@\U\1\L\2@' file          //sub first group with uppercase, second with lowercase
```

######numerical group
in sed, all parentheses need to be escaped
```
s/\(^\|[^0-9.]\)\([0-9]\+\)/\1\2/g
```
sed can be applied to all files in a folder
```
sed -f func.sed folder/*
```

#####execute commands with sed
######sed
we have a file list,content is
```
/etc/hosts
```
we can execute
```
sed 's/^/ls -l \e' list      //must have a space after -l
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

######sed in vim
```
%s/old/new/g       //all doc
2,3s/old/new/g     //2-3line
```
multireplace
```
/^stat/s/^/  /             //replace, add few spaces when line starts from stat

5,7s/^/  /      //line5-7
```
######read write files
in file 1:
```
4,10 w while
```
in file 2:
```
r while
```

#####fundamental awk
######intro awk
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
######employee application
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
#####displaying records from flat files using awk
let's see sed version. Delete all empty line, and append newline after </Virtualhost> /a means append.  \ means new line
```
sed ' /^\s*$/d;/^<\/Virtual/a \ ' host.conf
```
now is awk version  RS = record seperator, ~ means search
```
BEGIN {RS="\n\n";}    #only print until current paragraph ends
$0 ~ search {print }
```
use
```
awk -f virtual.awk search=keywordyouchoose virtual.conf
```
######using multiple field seperator, the new application release
OFS = output seperator, for example print $2 $3   then OFS will print between $2 and $3
```
BEGIN {FS="[><]"; RS="\n\n";OFS=" ";}  # >< are seperators
$0 ~ search {print }
```

#####analyze web logs awk
######creating summary 
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
