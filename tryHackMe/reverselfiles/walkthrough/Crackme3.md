# Crackme3

```
$> ./crackme3
zsh: permission denied: ./crackme3
$> ls -la
-rw-r--r-- 1 archips 2021_paris 9632 Dec 17 16:34 crackme3
$> chmod +x
$> ./crackme3
Usage: ./crackme3 PASSWORD
$> file crackme3
crackme3: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.24, BuildID[sha1]=4cf7250afb50109f0f1a01cc543fbf5ba6204a73, stripped
```

Thanks to the `file` command we notice that the binary is stripped, meaning that it contains no debugging information. Gdb won't really help us this time.
Using the command `strings` we get :

```
UWVS                                                                                                    
[^_]                                                                                                    
Usage: %s PASSWORD                                                                                      
malloc failed                                                                                           
ZjByX3kwdXJfNWVjMG5kX2xlNTVvbl91bmJhc2U2NF80bGxfN2gzXzdoMW5nNQ==                                        
Correct password!                                                                                       
Come on, even my aunt Mildred got this one!                                                             
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/                                        
;*2$"8                                                                                                  
GCC: (Ubuntu/Linaro 4.6.3-1ubuntu5) 4.6.3
```

Let's try `ZjByX3kwdXJfNWVjMG5kX2xlNTVvbl91bmJhc2U2NF80bGxfN2gzXzdoMW5nNQ==` as input.

```
$> ./crackme3 ZjByX3kwdXJfNWVjMG5kX2xlNTVvbl91bmJhc2U2NF80bGxfN2gzXzdoMW5nNQ==
Come on, even my aunt Mildred got this one!
```

It doesn't work at all. We can notice/guess two things about that input. It looks like something encrypted, and its length equals 64. Using a base64 decoder we get `***flag***`.

```
$> ./crackme3 ***flag***
Correct password!
```

### Tools

[base64 dcode](https://www.dcode.fr/code-base-64)
