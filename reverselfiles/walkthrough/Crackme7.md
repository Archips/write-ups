# Crackme7

```
$> ./crackme7
zsh: permission denied: ./crackme7
$> ls -la
-rw-r--r-- 1 archips 2021_paris 6372 Dec 17 18:21 crackme7
$> chmod +x
$> ./crackme7
Menu:

[1] Say hello
[2] Add numbers
[3] Quit

[>] 

$> file crackme7
crackme7: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=7ee4206d91718e7b0bef16a7c03f8fa49c4a39e7, not stripped
```

Once again, thanks to Ghidra we notice:

```
undefined4 main(void)

{
  int iVar1;
  undefined4 *puVar2;
  byte bVar3;
  undefined4 local_80 [25];
  int local_1c;
  int local_18;
  int local_14;
  undefined *local_10;
  
  bVar3 = 0;
  local_10 = &stack0x00000004;
  while( true ) {
    while( true ) {
      puts("Menu:\n\n[1] Say hello\n[2] Add numbers\n[3] Quit");
      printf("\n[>] ");
      iVar1 = __isoc99_scanf(&DAT_08048814,&local_14);
      if (iVar1 != 1) {
        puts("Unknown input!");
        return 1;
      }
      if (local_14 != 1) break;
      printf("What is your name? ");
      puVar2 = local_80;
      for (iVar1 = 0x19; iVar1 != 0; iVar1 = iVar1 + -1) {
        *puVar2 = 0;
        puVar2 = puVar2 + (uint)bVar3 * -2 + 1;
      }
      iVar1 = __isoc99_scanf(&DAT_0804883a,local_80);
      if (iVar1 != 1) {
        puts("Unable to read name!");
        return 1;
      }
      printf("Hello, %s!\n",local_80);
    }
    if (local_14 != 2) {
      if (local_14 == 3) {
        puts("Goodbye!");
      }
      else if (local_14 == 0x7a69) {
        puts("Wow such h4x0r!");
        giveFlag();
      }
      else {
        printf("Unknown choice: %d\n",local_14);
      }
      return 0;
    }
    printf("Enter first number: ");
    iVar1 = __isoc99_scanf(&DAT_08048875,&local_18);
    if (iVar1 != 1) break;
    printf("Enter second number: ");
    iVar1 = __isoc99_scanf(&DAT_08048875,&local_1c);
    if (iVar1 != 1) {
      puts("Unable to read number!");
      return 1;
    }
    printf("%d + %d = %d\n",local_18,local_1c,local_18 + local_1c);
  }
  puts("Unable to read number!");
  return 1;
}



void giveFlag(void)

{
  int iVar1;
  undefined4 *puVar2;
  undefined4 *puVar3;
  char local_ca [34];
  undefined4 local_a8 [34];
  uint local_20;
  
  puVar2 = &DAT_080488e0;
  puVar3 = local_a8;
  for (iVar1 = 0x22; iVar1 != 0; iVar1 = iVar1 + -1) {
    *puVar3 = *puVar2;
    puVar2 = puVar2 + 1;
    puVar3 = puVar3 + 1;
  }
  memset(local_ca,0x41,0x22);
  for (local_20 = 0; local_20 < 0x22; local_20 = local_20 + 1) {
    local_ca[local_20] = (char)local_a8[local_20] + local_ca[local_20];
  }
  puts(local_ca);
  return;
}
```

In the `main()` function we notice in the while loop several "if" to navigate in the menu. If we look closely in the code, we see that there's an hidden option `31337` or in hex `0x7a69`. Giving that number as input, the function `giveFlag()` is executed.
