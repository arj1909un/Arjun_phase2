# Thought Process
After downloading the given file in pico i went to my downloads directory where the file was stored using this cmnd
`cd ~/Downloads`
then i used the cmnd
`gdb debugger0_a` to debug the given file as that is what gnu does
after which i got some text which didn't really help me so i decided to give this cmnd a try
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
 # Flag: `picoCTF{549698}`




