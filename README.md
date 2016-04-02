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