# Crackme6

```
$> ./crackme6
zsh: permission denied: ./crackme6
$> ls -la
-rw-r--r-- 1 archips 2021_paris 8635 Dec 17 18:15 crackme6
$> chmod +x
$> ./crackme6
Usage : ./crackme6 password
Good luck, read the source
$> file crackme6
crackme6: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.24, BuildID[sha1]=022f1a8e479cab9f7263af75bcdbb328bda7f291, not stripped
```

Using Ghidra to decompile the binary we get:

```
...
undefined8 my_secure_test(char *param_1)

{
  undefined8 uVar1;
  
  if ((*param_1 == '\0') || (*param_1 != '1')) {
    uVar1 = 0xffffffff;
  }
  else if ((param_1[1] == '\0') || (param_1[1] != '3')) {
    uVar1 = 0xffffffff;
  }
  else if ((param_1[2] == '\0') || (param_1[2] != '3')) {
    uVar1 = 0xffffffff;
  }
  else if ((param_1[3] == '\0') || (param_1[3] != '7')) {
    uVar1 = 0xffffffff;
  }
  else if ((param_1[4] == '\0') || (param_1[4] != '_')) {
    uVar1 = 0xffffffff;
  }
  else if ((param_1[5] == '\0') || (param_1[5] != 'p')) {
    uVar1 = 0xffffffff;
  }
  else if ((param_1[6] == '\0') || (param_1[6] != 'w')) {
    uVar1 = 0xffffffff;
  }
  else if ((param_1[7] == '\0') || (param_1[7] != 'd')) {
    uVar1 = 0xffffffff;
  }
  else if (param_1[8] == '\0') {
    uVar1 = 0;
  }
  else {
    uVar1 = 0xffffffff;
  }
  return uVar1;
}

void compare_pwd(undefined8 param_1)

{
  int iVar1;
  
  iVar1 = my_secure_test(param_1);
  if (iVar1 == 0) {
    puts("password OK");
  }
  else {
    printf("password \"%s\" not OK\n",param_1);
  }
  return;
}

undefined8 main(int param_1,undefined8 *param_2)

{
  if (param_1 == 2) {
    compare_pwd(param_2[1]);
  }
  else {
    printf("Usage : %s password\nGood luck, read the source\n",*param_2);
  }
  return 0;
}
...
```

This one is pretty straightforward. Indeed, `argv[1]` is used as parameter for the function `compare_pwd()`. During the execution of this latter, the function `my_secure_test()`, each char of the user input will be compared to those of the actual password. 
