# Crackme2

```
$> ./crackme2
zsh: permission denied: ./crackme2
$> ls -la
-rw-r--r-- 1 archips 2021_paris 5884 Dec 17 16:25 crackme2
$> chmod +x
$> ./crackme2
Usage: ./crackme2 password
$> ./crackme2 password
acces denied
$> strings crackme2
...
t$,U                                                                                                    
[^_]                                                                                                    
Usage: %s password                                                                                      
super_secret_password                                                                                   
Access denied.                                                                                          
Access granted.                                                                                         
;*2$"(                                                                                             
GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.9) 5.4.0 20160609                                                     
crtstuff.c 
...
$> ./crackme2 super_secret_password
Access granted.
***flag***
```
