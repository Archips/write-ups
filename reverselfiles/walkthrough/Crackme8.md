# Crackme8

```
$> ./crackme8
zsh: permission denied: ./crackme8
$> ls -la
-rw-r--r-- 1 archips 2021_paris 5884 Dec 17 18:59 crackme8
$> chmod +x
$> ./crackme8
Usage: ./crackme8 password
$> ./crackme8 password
Access denied.
$> file crackme8
crackme8: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=fef76e38b5ff92ed0d08870ac523f9f3f8925a40, not stripped
```

For this last level, the idea is exactly the same, we decompile it with Ghidra:

```
...
undefined4 main(int param_1,undefined4 *param_2)

{
  undefined4 uVar1;
  int iVar2;
  
  if (param_1 == 2) {
    iVar2 = atoi((char *)param_2[1]);
    if (iVar2 == -0x35010ff3) {
      puts("Access granted.");
      giveFlag();
      uVar1 = 0;
    }
    else {
      puts("Access denied.");
      uVar1 = 1;
    }
  }
  else {
    printf("Usage: %s password\n",*param_2);
    uVar1 = 1;
  }
  return uVar1;
}

void giveFlag(void)

{
  int iVar1;
  undefined4 *puVar2;
  undefined4 *puVar3;
  char local_14c [60];
  undefined4 local_110 [60];
  uint local_20;
  
  puVar2 = &DAT_080486a0;
  puVar3 = local_110;
  for (iVar1 = 0x3c; iVar1 != 0; iVar1 = iVar1 + -1) {
    *puVar3 = *puVar2;
    puVar2 = puVar2 + 1;
    puVar3 = puVar3 + 1;
  }
  memset(local_14c,0x41,0x3c);
  for (local_20 = 0; local_20 < 0x3c; local_20 = local_20 + 1) {
    local_14c[local_20] = (char)local_110[local_20] + local_14c[local_20];
  }
  puts(local_14c);
  return;
}
...
```

We notice in the `main()` function that `atoi(argv[1])` should be equaled to the hex value `-0x35010ff3` or in decimal `-889262067`. Using the decimal value as input gives us the flag we're looking for.
