#### pslinuxadmsedawk

#####getting started
######sed
```
sed -n '1 p' /etc/passwd  //only print 1st line. without -n, will print all.
sed -i.bak '/^$/d' /etc/passwd 
```
