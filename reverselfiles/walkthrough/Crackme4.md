# Crackme4

```
$> ./crackme4
zsh: permission denied: ./crackme4
$> ls -la
-rw-r--r-- 1 archips 2021_paris 8740 Dec 17 16:51 crackme4
$> chmod +x
$> ./crackme4
Usage : ./crackme4 password
This time the string is hidden and we used strcmp
$> file crackme4
crackme4: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.24, BuildID[sha1]=862ee37793af334043b423ba50ec91cfa132260a, not stripped
```
Thanks to ghidra, we can decompile crackme4 to see what we've got under the hood.

```
...
void get_pwd(long param_1)

{
  int local_c;
  
  local_c = -1;
  while (local_c = local_c + 1, *(char *)(param_1 + local_c) != '\0') {
    *(byte *)(local_c + param_1) = *(byte *)(param_1 + local_c) ^ 0x24;
  }
  return;
}

void compare_pwd(char *param_1)

{
  int iVar1;
  long in_FS_OFFSET;
  undefined8 local_28;
  undefined8 local_20;
  undefined2 local_18;
  undefined local_16;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_28 = 0x7b175614497b5d49;
  local_20 = 0x547b175651474157;
  local_18 = 0x4053;
  local_16 = 0;
  get_pwd(&local_28);
  iVar1 = strcmp((char *)&local_28,param_1);
  if (iVar1 == 0) {
    puts("password OK");
  }
  else {
    printf("password \"%s\" not OK\n",param_1);
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    // WARNING: Subroutine does not return
    __stack_chk_fail();
  }
  return;
}

undefined8 main(int param_1,undefined8 *param_2)

{
  if (param_1 == 2) {
    compare_pwd(param_2[1]);
  }
  else {
    printf("Usage : %s password\nThis time the string is hidden and we used strcmp\n",*param_2);
  }
  return 0;
}


...
```

At this point we already have some interesting clues. The main calls the function `compare_pwd()` with the arg[1] (user input). That parameter is processed by the function `get_pwd()` during the exec of the `compare_pwd()` function. Then that input is compared,  using `strcmp()`, to the password we looking for.

Using `info functions` from gdb we get:

```
(gdb) info functions
All defined functions:                                                                                  
                                                                                                        
Non-debugging symbols:                                                                                  
0x00000000004004b0  _init                                                                               
0x00000000004004e0  puts@plt                                                                            
0x00000000004004f0  __stack_chk_fail@plt                                                                
0x0000000000400500  printf@plt                                                                          
0x0000000000400510  __libc_start_main@plt                                                               
0x0000000000400520  strcmp@plt                                                                          
0x0000000000400530  __gmon_start__@plt                                                                  
0x0000000000400540  _start                                                                              
0x0000000000400570  deregister_tm_clones                                                                
0x00000000004005a0  register_tm_clones                                                                  
0x00000000004005e0  __do_global_dtors_aux                                                               
0x0000000000400600  frame_dummy                                                                         
0x000000000040062d  get_pwd                                                                             
0x000000000040067a  compare_pwd                                                                         
0x0000000000400716  main                                                                                
0x0000000000400760  __libc_csu_init                                                                     
0x00000000004007d0  __libc_csu_fini                                                                     
0x00000000004007d4  _fini  
```

We can disas `compare_pwd()`:

```
(gdb) disas compare_pwd
Dump of assembler code for function compare_pwd:
   0x000000000040067a <+0>:     push   %rbp
   0x000000000040067b <+1>:     mov    %rsp,%rbp
   0x000000000040067e <+4>:     sub    $0x30,%rsp
   0x0000000000400682 <+8>:     mov    %rdi,-0x28(%rbp)
   0x0000000000400686 <+12>:    mov    %fs:0x28,%rax
   0x000000000040068f <+21>:    mov    %rax,-0x8(%rbp)
   0x0000000000400693 <+25>:    xor    %eax,%eax
   0x0000000000400695 <+27>:    movabs $0x7b175614497b5d49,%rax
   0x000000000040069f <+37>:    mov    %rax,-0x20(%rbp)
   0x00000000004006a3 <+41>:    movabs $0x547b175651474157,%rax
   0x00000000004006ad <+51>:    mov    %rax,-0x18(%rbp)
   0x00000000004006b1 <+55>:    movw   $0x4053,-0x10(%rbp)
   0x00000000004006b7 <+61>:    movb   $0x0,-0xe(%rbp)
   0x00000000004006bb <+65>:    lea    -0x20(%rbp),%rax
   0x00000000004006bf <+69>:    mov    %rax,%rdi
   0x00000000004006c2 <+72>:    call   0x40062d <get_pwd>
   0x00000000004006c7 <+77>:    mov    -0x28(%rbp),%rdx
   0x00000000004006cb <+81>:    lea    -0x20(%rbp),%rax
   0x00000000004006cf <+85>:    mov    %rdx,%rsi
   0x00000000004006d2 <+88>:    mov    %rax,%rdi
   0x00000000004006d5 <+91>:    call   0x400520 <strcmp@plt>
   0x00000000004006da <+96>:    test   %eax,%eax
   0x00000000004006dc <+98>:    jne    0x4006ea <compare_pwd+112>
   0x00000000004006de <+100>:   mov    $0x4007e8,%edi
   0x00000000004006e3 <+105>:   call   0x4004e0 <puts@plt>
   0x00000000004006e8 <+110>:   jmp    0x400700 <compare_pwd+134>
   0x00000000004006ea <+112>:   mov    -0x28(%rbp),%rax
   0x00000000004006ee <+116>:   mov    %rax,%rsi
   0x00000000004006f1 <+119>:   mov    $0x4007f4,%edi
   0x00000000004006f6 <+124>:   mov    $0x0,%eax
   0x00000000004006fb <+129>:   call   0x400500 <printf@plt>
   0x0000000000400700 <+134>:   mov    -0x8(%rbp),%rax
   0x0000000000400704 <+138>:   xor    %fs:0x28,%rax
   0x000000000040070d <+147>:   je     0x400714 <compare_pwd+154>
   0x000000000040070f <+149>:   call   0x4004f0 <__stack_chk_fail@plt>
   0x0000000000400714 <+154>:   leave  
   0x0000000000400715 <+155>:   ret    
End of assembler dump.
```

Doing so, we can now put a breakpoint at the address where `rdi` received the password, right before the call of `strcmp()` and run it with a random arg.

```
(gbd) b *0x00000000004006d2
Breakpoint 1 at 0x4006d2
(gdb) run password
Starting program: /mnt/nfs/homes/athirion/git/tryhackme/reversefile/crackme4 password
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, 0x00000000004006d2 in compare_pwd ()

```

After that, we can have a look at what the registers contains, and print the value stored in `rdi`

```
(gdb) info registers
rax            0x7fffffffe1f0      140737488347632
rbx            0x0                 0
rcx            0x11                17
rdx            0x7fffffffe6e1      140737488348897
rsi            0x7fffffffe6e1      140737488348897
rdi            0x7fffffffe1f0      140737488347632
rbp            0x7fffffffe210      0x7fffffffe210
rsp            0x7fffffffe1e0      0x7fffffffe1e0
r8             0x7ffff7f94f10      140737353699088
r9             0x7ffff7fc9040      140737353912384
r10            0x7ffff7fc3908      140737353890056
r11            0x7ffff7fde660      140737353999968
r12            0x7fffffffe348      140737488347976
r13            0x400716            4196118
r14            0x0                 0
r15            0x7ffff7ffd040      140737354125376
rip            0x4006d2            0x4006d2 <compare_pwd+88>
eflags         0x246               [ PF ZF IF ]
cs             0x33                51
ss             0x2b                43
ds             0x0                 0
es             0x0                 0
fs             0x0                 0
gs             0x0                 0
(gdb) x/s $rdi
***flag***
```
