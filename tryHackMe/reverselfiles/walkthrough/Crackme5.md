# Crackme5

```
$> ./crackme5
zsh: permission denied: ./crackme5
$> ls -la
-rw-r--r-- 1 archips 2021_paris 8968 Dec 17 18:00 crackme5
$> chmod +x
$> ./crackme5
enter your input:
password
Always dig deeper
$> file crackme5
crackme5: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=a426dcf8ed3de8cb02f3ee4f38ee36b4ed568519, not stripped
```

Decompiling crackme5 with BinaryNinja:

```
...
int32_t main(int32_t argc, char** argv, char** envp)
{
    int32_t argc_1 = argc;
    char** argv_1 = argv;
    void* fsbase;
    int64_t rax = *(fsbase + 0x28);
    char var_38;
    __builtin_strncpy(&var_38, "OfdlDSA|3tXb32~X3tX@sX`4tXtz", 0x1c);
    puts("Enter your input:");
    void var_58;
    __isoc99_scanf(&data_400966, &var_58);
    if (strcmp_(&var_58, &var_38) != 0)
    {
        puts("Always dig deeper");
    }
    else
    {
        puts("Good game");
    }
    if (rax == *(fsbase + 0x28))
    {
        return 0;
    }
    __stack_chk_fail();
    /* no return */
}
...
```

We observe that there's a comparison between var_58 (user input) and the actual password, var_38. Before the `strcmp(&var_58, &var_38)`, we see that var_38 is filled we a password of 28 chars using the function `strncpy()`. That our password !
