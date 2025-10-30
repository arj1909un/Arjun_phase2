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

In the given function, we can see that `buf2` is assigned 16 chars and the user input is being copied into buf2. This means, any input greater than 16 chars will overflow the buffer and give out the flag.

```c
void vuln(char *input){
  char buf2[16];
  strcpy(buf2, input);
}
```
What really caught my attention was the function called `sigsegv_handler` after some surfing i found out what it means.
```c
void sigsegv_handler(int sig)
```
This function gets called automatically when a SIGSEGV (segmentation fault) signal occurs, sig parameter receives the signal number and Returns void because signal handlers don't return values to the caller.
After looking at the first hint i found the way to my flag
`How can you trigger the flag to print?`
Provide input longer than 16 bytes to overflow buf2 in the vuln() function and this will corrupt the stack and cause a segmentation fault finally the SIGSEGV handler triggers and prints the flag.

On passing input of more than 16 chars, the program gave out the flag. 


Flag: `picoCTF{ov3rfl0ws_ar3nt_that_bad_ef01832d}`


