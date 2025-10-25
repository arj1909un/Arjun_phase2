# 1.GDB_babystep1
## Thought Process
After downloading the given file in pico i went to my downloads directory where the file was stored using this cmnd
`cd ~/Downloads`
then i used the cmnd
`gdb debugger0_a` to debug the given file as that is what gnu does
after which it loaded gdb for me, then i gave this cmnd a try
`gdb disassemble main`, "Disassembly" translates machine code to assembly language
which gave me this
```
   0x0000000000001129 <+0>:	endbr64
   0x000000000000112d <+4>:	push   %rbp
   0x000000000000112e <+5>:	mov    %rsp,%rbp
   0x0000000000001131 <+8>:	mov    %edi,-0x4(%rbp)
   0x0000000000001134 <+11>:	mov    %rsi,-0x10(%rbp)
   0x0000000000001138 <+15>:	mov    $0x86342,%eax
   0x000000000000113d <+20>:	pop    %rbp
   0x000000000000113e <+21>:	ret
```
after which i found eax and then converted `0x86342`, which basically tells us the value that is being moved into the eax register,then we convert those digits from hex to decimal which gave the digits `549698`
 ## Flag: `picoCTF{549698}`


# 3.Vault_Door3
 ## Thought Process
So in this Challenge we received a source java code after going through this code i understood that there is a target password given 
which is not the right one so we need to reverse it following the instructions inside the for loop, there can be two ways to do this 
### 1.manually solving using pen and paper which is not what i did at all.
### 2.writing a code that reverses the given string to the desired password i want,here is my code :
```

void unscramble(char *buffer, char *password) {
    int i;
    
    for (i = 0; i < 8; i++) {
        password[i] = buffer[i];
    }
    
    for (i = 8; i < 16; i++) {
        password[23-i] = buffer[i];
    }
    
    for (i = 16; i < 32; i += 2) {
        password[46-i] = buffer[i];
    }
    
    for (i = 31; i >= 17; i -= 2) {
        password[i] = buffer[i];
    }
    
    password[32] = '\0';
}

int main() {
    char target[] = "jU5t_a_sna_3lpm18gb41_u_4_mfr340";
    char password[33];
    
    unscramble(target, password);
    printf("Password: %s\n", password);
 
    
    return 0;
}


```
after running this code i got the target string aka the correct password.
## Flag : picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_1fb380}
