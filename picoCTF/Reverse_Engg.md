# 1.GDB_babystep1

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

# 2.ARMssembly 1 

after surfing the web i found out that the Command required to compile ARM Assembly on x86 is:

```
aarch64-linux-gnu-gcc -o chall_1 chall_1.S 
qemu-aarch64 -L /usr/aarch64-linux-gnu ./chall_1 <argument_in_integer>
```


We are provided with a file called chall_1.S, lets trace the ARM assembly program to check what it does. 

```
	.arch armv8-a
	.file	"chall_1.c"
	.text
	.align	2
	.global	func
	.type	func, %function
func:
	sub	sp, sp, #32      ; sp=sp-32  ; sp = stack pointer, allocate 32 bytes on the stack  
	str	w0, [sp, 12]     ; store register w0 at mem location sp+12, which is the user input 
	mov	w0, 83           ; move 83 in w0 register, w0 = 83 
	str	w0, [sp, 16]     ; store value of w0 in sp+16, sp+16 = 83 
	str	wzr, [sp, 20]    ; store value of wzr in sp+20, sp+20 = 0 ; wzr is a zero register
	mov	w0, 3            ; move 3 in w0 register, w0 = 3
	str	w0, [sp, 24]     ; store value of w0 in sp+24, sp+24 = 3
	ldr	w0, [sp, 20]     ; load from mem addr sp+20 to register w0, w0 = 0
	ldr	w1, [sp, 16]     ; load from mem addr sp+16 to register w1, w1 = 83
	lsl	w0, w1, w0       ; logical shift left, w0 = w1 << w0 => w0=83<<0=83
	str	w0, [sp, 28]     ; store w0 at sp+28, sp+28 = w0 = 83
	ldr	w1, [sp, 28]     ; load from mem addr sp+28 to register w1, w1=83
	ldr	w0, [sp, 24]     ; load from mem addr sp+24 to register w0, w0=3
	sdiv	w0, w1, w0   ; signed division, w0 = w1 / w0 => w0 = 83/3 = 27 (integer division)
	str	w0, [sp, 28]     ; sp+28 = w0 = 27
	ldr	w1, [sp, 28]     ; load from mem addr sp+28 to register w1, w1=27
	ldr	w0, [sp, 12]     ; w0 = sp+12, w0 = input 
	sub	w0, w1, w0       ; w0 = w1 - input = 27 - input 
	str	w0, [sp, 28]     ; sp+28 = w0 
	ldr	w0, [sp, 28]     ; load from mem addr sp+28 to register w0 
	add	sp, sp, 32       ; sp=sp+32, free 32 allocated bytes  
	ret                  ; return w0, which is 27 - input  
	.size	func, .-func
	.section	.rodata
	.align	3
.LC0:
	.string	"You win!"
	.align	3
.LC1:
	.string	"You Lose :("
	.text
	.align	2
	.global	main
	.type	main, %function
main:
	stp	x29, x30, [sp, -48]!
	add	x29, sp, 0
	str	w0, [x29, 28]
	str	x1, [x29, 16]
	ldr	x0, [x29, 16]
	add	x0, x0, 8
	ldr	x0, [x0]
	bl	atoi
	str	w0, [x29, 44]
	ldr	w0, [x29, 44]    ; w0 = x29+44
	bl	func             ; branch with link instruction, used for function calls, links w0 of func to w0 of main 
	cmp	w0, 0            ; compare w0 with 0, if 27-input = 0, then run .LC0
	bne	.L4              ; branch not equal to, run if 27-input not 0  
	adrp	x0, .LC0
	add	x0, x0, :lo12:.LC0
	bl	puts
	b	.L6
.L4:
	adrp	x0, .LC1
	add	x0, x0, :lo12:.LC1
	bl	puts
.L6:
	nop
	ldp	x29, x30, [sp], 48
	ret
	.size	main, .-main
	.ident	"GCC: (Ubuntu/Linaro 7.5.0-3ubuntu1~18.04) 7.5.0"
	.section	.note.GNU-stack,"",@progbits

```

Now, we can conclude that, we need to input 27 as an argument to make the `w0` register 0 and return You Win. 


Converting 3370 to hex, we get 0xD2A 
Padded to 32 bits (8 hex digits): 0x00000D2A
Lowercase, no 0x prefix: 00000d2a

abracadabra we found it !!

# flag: `picoCTF{00000d2a}`



# 3.Vault_Door3

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
