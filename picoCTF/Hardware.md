# IQ Test

As an example, entering x = 34359738368 gives (y0, ..., y11), so the flag would be nite{010000000011}.

Given input: `30478191278`
Converting that to binary: `11100011000101001000100101010101110`
There are total 36 inputs and 12 outputs. 
But the binary number we got was 35 digits and not 36. 
Therefore, a 0 has to be prepended to the given binary number to make it fit into the 36 inputs. 
By using Boolean algebra, the following result was obtained: 

Input: `011100011000101001000100101010101110`

<img width="587" height="863" alt="image" src="https://github.com/user-attachments/assets/48ca5afe-a4e4-4bed-85d4-0587a4553654" />

After solving this beautiful(sacrcasm) logic gate of course after some revision i got this :
Output: `100010011000`

flag: `nite{100010011000}`

# 2.i_like_logic

Along with the description there was a file given called `challenge.sal`, where _.sal_ was a new extension for me so the first thing I searched about was this extension and this file was not opening, so methods of opening this file, so first I found this: `A .sal extension can refer to several different file types, most commonly data capture files from the Saleae Logic 2 software, or source code files for languages like SQL Application Language or Scalable Application Language`, so this is all about the logic2 software, after searching about how to open it I found that I'll have to download this logic2 software to open this file, which I did and dragged and dropped to find this:

<img width="1188" height="775" alt="Screenshot 2025-10-31 at 1 46 10 AM" src="https://github.com/user-attachments/assets/14560262-a965-4a94-9e85-a1fa0b32f443" />

Yeah here, this looked like of no help so I looked around the options, then I fount this option called `add analyzer`, which looked like it could help, then I searched: "what analyzer to add to get content out of a .sal file on logic 2", then found the `async serial` analyzer , then I tried it and put up input channels as 3 and then 4, then in the terminal it printed some story kind of thing I copy pasted it into my texteditor and went through it to find something flag looking: 

 <img width="250" height="292" alt="Screenshot 2025-10-31 at 1 55 20 AM" src="https://github.com/user-attachments/assets/e86c1d9c-7dca-4d7a-b47e-5a20abf3b7b6" />

## Flag
`FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}`



# 3. bare-metal-alchemist

One file was provided `firmware.elf`
Downloading the `elf` file, I first tried to execute it locally but I got an error.
```
 $ ./firmware.elf
zsh: exec format error: ./firmware.elf
```
 Then looking at the file format, I found out that it is `Atmel AVR` elf.
```
sparsh@LAPTOP-F80QI4V2 /mnt/d $ file firmware.elf
firmware.elf: ELF 32-bit LSB executable, Atmel AVR 8-bit, version 1 (SYSV), statically linked, with debug_info, not stripped
```
 I searched around the net for the this specific elf format, through that I found that `AVR` is a family of microcontrollers by the company `Atmel`.
 I installed avr-objdump on my system and got the assembly code, but I couldn't manage to rea it
```
 $ avr-objdump -d firmware.elf

firmware.elf:     file format elf32-avr


Disassembly of section .text:

00000000 <__vectors>:
   0:   0c 94 4b 00     jmp     0x96    ; 0x96 <__ctors_end>
   4:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
   8:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
   c:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  10:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  14:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  18:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  1c:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  20:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  24:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  28:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  2c:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  30:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  34:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  38:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  3c:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  40:   0c 94 71 00     jmp     0xe2    ; 0xe2 <__vector_16>
  44:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  48:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  4c:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  50:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  54:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  58:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  5c:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  60:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>
  64:   0c 94 68 00     jmp     0xd0    ; 0xd0 <__bad_interrupt>

00000068 <__trampolines_end>:
  68:   f1 e3           ldi     r31, 0x31       ; 49
  6a:   e6 e6           ldi     r30, 0x66       ; 102
  6c:   f1 e3           ldi     r31, 0x31       ; 49
  6e:   de f1           brts    .+118           ; 0xe6 <__vector_16+0x4>
  70:   cd 94 d6 fa     jmp     0x33f5ac        ; 0x33f5ac <__TEXT_REGION_LENGTH__+0x31f5ac>
  74:   94 d6           rcall   .+3368          ; 0xd9e <__stack+0x49f>
  76:   fa d6           rcall   .+3572          ; 0xe6c <__stack+0x56d>
  78:   ca c8           rjmp    .-3692          ; 0xfffff20e <__eeprom_end+0xff7ef20e>
  7a:   96 fa           bst     r9, 6
  7c:   d6 94           lsr     r13
  7e:   c8 d5           rcall   .+2960          ; 0xc10 <__stack+0x311>
  80:   c9 96           adiw    r24, 0x39       ; 57
  82:   fa 91           ld      r31, -Y
  84:   d7 c1           rjmp    .+942           ; 0x434 <__LOCK_REGION_LENGTH__+0x34>
  86:   d0 94           com     r13
  88:   cb ca           rjmp    .-2666          ; 0xfffff620 <__eeprom_end+0xff7ef620>
  8a:   fa c3           rjmp    .+2036          ; 0x880 <__LOCK_REGION_LENGTH__+0x480>
  8c:   94 d7           rcall   .+3880          ; 0xfb6 <__stack+0x6b7>
  8e:   c8 d2           rcall   .+1424          ; 0x620 <__LOCK_REGION_LENGTH__+0x220>
  90:   91 d7           rcall   .+3874          ; 0xfb4 <__stack+0x6b5>
  92:   c0 d8           rcall   .-3712          ; 0xfffff214 <__eeprom_end+0xff7ef214>
        ...

00000096 <__ctors_end>:
  96:   11 24           eor     r1, r1
  98:   1f be           out     0x3f, r1        ; 63
  9a:   cf ef           ldi     r28, 0xFF       ; 255
  9c:   d8 e0           ldi     r29, 0x08       ; 8
  9e:   de bf           out     0x3e, r29       ; 62
  a0:   cd bf           out     0x3d, r28       ; 61

000000a2 <__do_copy_data>:
  a2:   11 e0           ldi     r17, 0x01       ; 1
  a4:   a0 e0           ldi     r26, 0x00       ; 0
  a6:   b1 e0           ldi     r27, 0x01       ; 1
  a8:   e4 ee           ldi     r30, 0xE4       ; 228
  aa:   f2 e0           ldi     r31, 0x02       ; 2
  ac:   02 c0           rjmp    .+4             ; 0xb2 <__do_copy_data+0x10>
  ae:   05 90           lpm     r0, Z+
  b0:   0d 92           st      X+, r0
  b2:   ae 34           cpi     r26, 0x4E       ; 78
  b4:   b1 07           cpc     r27, r17
  b6:   d9 f7           brne    .-10            ; 0xae <__do_copy_data+0xc>

000000b8 <__do_clear_bss>:
  b8:   21 e0           ldi     r18, 0x01       ; 1
  ba:   ae e4           ldi     r26, 0x4E       ; 78
  bc:   b1 e0           ldi     r27, 0x01       ; 1
  be:   01 c0           rjmp    .+2             ; 0xc2 <.do_clear_bss_start>

000000c0 <.do_clear_bss_loop>:
  c0:   1d 92           st      X+, r1

000000c2 <.do_clear_bss_start>:
  c2:   a7 35           cpi     r26, 0x57       ; 87
  c4:   b2 07           cpc     r27, r18
  c6:   e1 f7           brne    .-8             ; 0xc0 <.do_clear_bss_loop>
  c8:   0e 94 bb 00     call    0x176   ; 0x176 <main>
  cc:   0c 94 70 01     jmp     0x2e0   ; 0x2e0 <_exit>

000000d0 <__bad_interrupt>:
  d0:   0c 94 00 00     jmp     0       ; 0x0 <__vectors>

000000d4 <_ZL2z1v>:
  d4:   8b b1           in      r24, 0x0b       ; 11
  d6:   87 70           andi    r24, 0x07       ; 7
  d8:   8b b9           out     0x0b, r24       ; 11
  da:   85 b1           in      r24, 0x05       ; 5
  dc:   8c 7f           andi    r24, 0xFC       ; 252
  de:   85 b9           out     0x05, r24       ; 5
  e0:   08 95           ret

000000e2 <__vector_16>:
  e2:   1f 92           push    r1
  e4:   0f 92           push    r0
  e6:   0f b6           in      r0, 0x3f        ; 63
  e8:   0f 92           push    r0
  ea:   11 24           eor     r1, r1
  ec:   2f 93           push    r18
  ee:   3f 93           push    r19
  f0:   8f 93           push    r24
  f2:   9f 93           push    r25
  f4:   af 93           push    r26
  f6:   bf 93           push    r27
  f8:   80 91 53 01     lds     r24, 0x0153     ; 0x800153 <timer0_millis>
  fc:   90 91 54 01     lds     r25, 0x0154     ; 0x800154 <timer0_millis+0x1>
 100:   a0 91 55 01     lds     r26, 0x0155     ; 0x800155 <timer0_millis+0x2>
 104:   b0 91 56 01     lds     r27, 0x0156     ; 0x800156 <timer0_millis+0x3>
 108:   30 91 52 01     lds     r19, 0x0152     ; 0x800152 <timer0_fract>
 10c:   23 e0           ldi     r18, 0x03       ; 3
 10e:   23 0f           add     r18, r19
 110:   2d 37           cpi     r18, 0x7D       ; 125
 112:   58 f5           brcc    .+86            ; 0x16a <__vector_16+0x88>
 114:   01 96           adiw    r24, 0x01       ; 1
 116:   a1 1d           adc     r26, r1
 118:   b1 1d           adc     r27, r1
 11a:   20 93 52 01     sts     0x0152, r18     ; 0x800152 <timer0_fract>
 11e:   80 93 53 01     sts     0x0153, r24     ; 0x800153 <timer0_millis>
 122:   90 93 54 01     sts     0x0154, r25     ; 0x800154 <timer0_millis+0x1>
 126:   a0 93 55 01     sts     0x0155, r26     ; 0x800155 <timer0_millis+0x2>
 12a:   b0 93 56 01     sts     0x0156, r27     ; 0x800156 <timer0_millis+0x3>
 12e:   80 91 4e 01     lds     r24, 0x014E     ; 0x80014e <__data_end>
 132:   90 91 4f 01     lds     r25, 0x014F     ; 0x80014f <__data_end+0x1>
 136:   a0 91 50 01     lds     r26, 0x0150     ; 0x800150 <__data_end+0x2>
 13a:   b0 91 51 01     lds     r27, 0x0151     ; 0x800151 <__data_end+0x3>
 13e:   01 96           adiw    r24, 0x01       ; 1
 140:   a1 1d           adc     r26, r1
 142:   b1 1d           adc     r27, r1
 144:   80 93 4e 01     sts     0x014E, r24     ; 0x80014e <__data_end>
 148:   90 93 4f 01     sts     0x014F, r25     ; 0x80014f <__data_end+0x1>
 14c:   a0 93 50 01     sts     0x0150, r26     ; 0x800150 <__data_end+0x2>
 150:   b0 93 51 01     sts     0x0151, r27     ; 0x800151 <__data_end+0x3>
 154:   bf 91           pop     r27
 156:   af 91           pop     r26
 158:   9f 91           pop     r25
 15a:   8f 91           pop     r24
 15c:   3f 91           pop     r19
 15e:   2f 91           pop     r18
 160:   0f 90           pop     r0
 162:   0f be           out     0x3f, r0        ; 63
 164:   0f 90           pop     r0
 166:   1f 90           pop     r1
 168:   18 95           reti
 16a:   26 e8           ldi     r18, 0x86       ; 134
 16c:   23 0f           add     r18, r19
 16e:   02 96           adiw    r24, 0x02       ; 2
 170:   a1 1d           adc     r26, r1
 172:   b1 1d           adc     r27, r1
 174:   d2 cf           rjmp    .-92            ; 0x11a <__vector_16+0x38>

00000176 <main>:
 176:   cf 93           push    r28
 178:   df 93           push    r29
 17a:   00 d0           rcall   .+0             ; 0x17c <main+0x6>
 17c:   cd b7           in      r28, 0x3d       ; 61
 17e:   de b7           in      r29, 0x3e       ; 62
 180:   78 94           sei
 182:   84 b5           in      r24, 0x24       ; 36
 184:   82 60           ori     r24, 0x02       ; 2
 186:   84 bd           out     0x24, r24       ; 36
 188:   84 b5           in      r24, 0x24       ; 36
 18a:   81 60           ori     r24, 0x01       ; 1
 18c:   84 bd           out     0x24, r24       ; 36
 18e:   85 b5           in      r24, 0x25       ; 37
 190:   82 60           ori     r24, 0x02       ; 2
 192:   85 bd           out     0x25, r24       ; 37
 194:   85 b5           in      r24, 0x25       ; 37
 196:   81 60           ori     r24, 0x01       ; 1
 198:   85 bd           out     0x25, r24       ; 37
 19a:   80 91 6e 00     lds     r24, 0x006E     ; 0x80006e <__TEXT_REGION_LENGTH__+0x7e006e>
 19e:   81 60           ori     r24, 0x01       ; 1
 1a0:   80 93 6e 00     sts     0x006E, r24     ; 0x80006e <__TEXT_REGION_LENGTH__+0x7e006e>
 1a4:   10 92 81 00     sts     0x0081, r1      ; 0x800081 <__TEXT_REGION_LENGTH__+0x7e0081>
 1a8:   80 91 81 00     lds     r24, 0x0081     ; 0x800081 <__TEXT_REGION_LENGTH__+0x7e0081>
 1ac:   82 60           ori     r24, 0x02       ; 2
 1ae:   80 93 81 00     sts     0x0081, r24     ; 0x800081 <__TEXT_REGION_LENGTH__+0x7e0081>
 1b2:   80 91 81 00     lds     r24, 0x0081     ; 0x800081 <__TEXT_REGION_LENGTH__+0x7e0081>
 1b6:   81 60           ori     r24, 0x01       ; 1
 1b8:   80 93 81 00     sts     0x0081, r24     ; 0x800081 <__TEXT_REGION_LENGTH__+0x7e0081>
 1bc:   80 91 80 00     lds     r24, 0x0080     ; 0x800080 <__TEXT_REGION_LENGTH__+0x7e0080>
 1c0:   81 60           ori     r24, 0x01       ; 1
 1c2:   80 93 80 00     sts     0x0080, r24     ; 0x800080 <__TEXT_REGION_LENGTH__+0x7e0080>
 1c6:   80 91 b1 00     lds     r24, 0x00B1     ; 0x8000b1 <__TEXT_REGION_LENGTH__+0x7e00b1>
 1ca:   84 60           ori     r24, 0x04       ; 4
 1cc:   80 93 b1 00     sts     0x00B1, r24     ; 0x8000b1 <__TEXT_REGION_LENGTH__+0x7e00b1>
 1d0:   80 91 b0 00     lds     r24, 0x00B0     ; 0x8000b0 <__TEXT_REGION_LENGTH__+0x7e00b0>
 1d4:   81 60           ori     r24, 0x01       ; 1
 1d6:   80 93 b0 00     sts     0x00B0, r24     ; 0x8000b0 <__TEXT_REGION_LENGTH__+0x7e00b0>
 1da:   80 91 7a 00     lds     r24, 0x007A     ; 0x80007a <__TEXT_REGION_LENGTH__+0x7e007a>
 1de:   84 60           ori     r24, 0x04       ; 4
 1e0:   80 93 7a 00     sts     0x007A, r24     ; 0x80007a <__TEXT_REGION_LENGTH__+0x7e007a>
 1e4:   80 91 7a 00     lds     r24, 0x007A     ; 0x80007a <__TEXT_REGION_LENGTH__+0x7e007a>
 1e8:   82 60           ori     r24, 0x02       ; 2
 1ea:   80 93 7a 00     sts     0x007A, r24     ; 0x80007a <__TEXT_REGION_LENGTH__+0x7e007a>
 1ee:   80 91 7a 00     lds     r24, 0x007A     ; 0x80007a <__TEXT_REGION_LENGTH__+0x7e007a>
 1f2:   81 60           ori     r24, 0x01       ; 1
 1f4:   80 93 7a 00     sts     0x007A, r24     ; 0x80007a <__TEXT_REGION_LENGTH__+0x7e007a>
 1f8:   80 91 7a 00     lds     r24, 0x007A     ; 0x80007a <__TEXT_REGION_LENGTH__+0x7e007a>
 1fc:   80 68           ori     r24, 0x80       ; 128
 1fe:   80 93 7a 00     sts     0x007A, r24     ; 0x80007a <__TEXT_REGION_LENGTH__+0x7e007a>
 202:   10 92 c1 00     sts     0x00C1, r1      ; 0x8000c1 <__TEXT_REGION_LENGTH__+0x7e00c1>
 206:   8a b1           in      r24, 0x0a       ; 10
 208:   88 6f           ori     r24, 0xF8       ; 248
 20a:   8a b9           out     0x0a, r24       ; 10
 20c:   84 b1           in      r24, 0x04       ; 4
 20e:   83 60           ori     r24, 0x03       ; 3
 210:   84 b9           out     0x04, r24       ; 4
 212:   52 98           cbi     0x0a, 2 ; 10
 214:   8b b1           in      r24, 0x0b       ; 11
 216:   87 70           andi    r24, 0x07       ; 7
 218:   8b b9           out     0x0b, r24       ; 11
 21a:   85 b1           in      r24, 0x05       ; 5
 21c:   8c 7f           andi    r24, 0xFC       ; 252
 21e:   85 b9           out     0x05, r24       ; 5
 220:   95 ea           ldi     r25, 0xA5       ; 165
 222:   b9 2e           mov     r11, r25
 224:   20 e0           ldi     r18, 0x00       ; 0
 226:   c2 2e           mov     r12, r18
 228:   20 e0           ldi     r18, 0x00       ; 0
 22a:   d2 2e           mov     r13, r18
 22c:   89 b1           in      r24, 0x09       ; 9
 22e:   98 2f           mov     r25, r24
 230:   99 0f           add     r25, r25
 232:   89 27           eor     r24, r25
 234:   82 ff           sbrs    r24, 2
 236:   23 c0           rjmp    .+70            ; 0x27e <main+0x108>
 238:   88 e6           ldi     r24, 0x68       ; 104
 23a:   e8 2e           mov     r14, r24
 23c:   80 e0           ldi     r24, 0x00       ; 0
 23e:   f8 2e           mov     r15, r24
 240:   00 e0           ldi     r16, 0x00       ; 0
 242:   f7 01           movw    r30, r14
 244:   84 91           lpm     r24, Z
 246:   88 23           and     r24, r24
 248:   61 f0           breq    .+24            ; 0x262 <main+0xec>
 24a:   e8 2f           mov     r30, r24
 24c:   eb 25           eor     r30, r11
 24e:   85 3a           cpi     r24, 0xA5       ; 165
 250:   41 f0           breq    .+16            ; 0x262 <main+0xec>
 252:   89 b1           in      r24, 0x09       ; 9
 254:   98 2f           mov     r25, r24
 256:   99 0f           add     r25, r25
 258:   89 27           eor     r24, r25
 25a:   82 fd           sbrc    r24, 2
 25c:   18 c0           rjmp    .+48            ; 0x28e <main+0x118>
 25e:   0e 94 6a 00     call    0xd4    ; 0xd4 <_ZL2z1v>
 262:   1a 82           std     Y+2, r1 ; 0x02
 264:   19 82           std     Y+1, r1 ; 0x01
 266:   89 81           ldd     r24, Y+1        ; 0x01
 268:   9a 81           ldd     r25, Y+2        ; 0x02
 26a:   8c 32           cpi     r24, 0x2C       ; 44
 26c:   91 40           sbci    r25, 0x01       ; 1
 26e:   48 f4           brcc    .+18            ; 0x282 <main+0x10c>
 270:   00 00           nop
 272:   89 81           ldd     r24, Y+1        ; 0x01
 274:   9a 81           ldd     r25, Y+2        ; 0x02
 276:   01 96           adiw    r24, 0x01       ; 1
 278:   9a 83           std     Y+2, r25        ; 0x02
 27a:   89 83           std     Y+1, r24        ; 0x01
 27c:   f4 cf           rjmp    .-24            ; 0x266 <main+0xf0>
 27e:   0e 94 6a 00     call    0xd4    ; 0xd4 <_ZL2z1v>
 282:   c1 14           cp      r12, r1
 284:   d1 04           cpc     r13, r1
 286:   91 f2           breq    .-92            ; 0x22c <main+0xb6>
 288:   0e 94 00 00     call    0       ; 0x0 <__vectors>
 28c:   cf cf           rjmp    .-98            ; 0x22c <main+0xb6>
 28e:   e0 53           subi    r30, 0x30       ; 48
 290:   10 e0           ldi     r17, 0x00       ; 0
 292:   ee 34           cpi     r30, 0x4E       ; 78
 294:   20 f4           brcc    .+8             ; 0x29e <main+0x128>
 296:   f0 e0           ldi     r31, 0x00       ; 0
 298:   e0 50           subi    r30, 0x00       ; 0
 29a:   ff 4f           sbci    r31, 0xFF       ; 255
 29c:   10 81           ld      r17, Z
 29e:   0e 94 6a 00     call    0xd4    ; 0xd4 <_ZL2z1v>
 2a2:   10 fd           sbrc    r17, 0
 2a4:   5b 9a           sbi     0x0b, 3 ; 11
 2a6:   11 fd           sbrc    r17, 1
 2a8:   5c 9a           sbi     0x0b, 4 ; 11
 2aa:   12 fd           sbrc    r17, 2
 2ac:   5d 9a           sbi     0x0b, 5 ; 11
 2ae:   13 fd           sbrc    r17, 3
 2b0:   5e 9a           sbi     0x0b, 6 ; 11
 2b2:   14 fd           sbrc    r17, 4
 2b4:   5f 9a           sbi     0x0b, 7 ; 11
 2b6:   15 fd           sbrc    r17, 5
 2b8:   28 9a           sbi     0x05, 0 ; 5
 2ba:   16 fd           sbrc    r17, 6
 2bc:   29 9a           sbi     0x05, 1 ; 5
 2be:   80 2f           mov     r24, r16
 2c0:   8f 71           andi    r24, 0x1F       ; 31
 2c2:   83 5d           subi    r24, 0xD3       ; 211
 2c4:   81 50           subi    r24, 0x01       ; 1
 2c6:   38 f0           brcs    .+14            ; 0x2d6 <main+0x160>
 2c8:   ef e9           ldi     r30, 0x9F       ; 159
 2ca:   ff e0           ldi     r31, 0x0F       ; 15
 2cc:   31 97           sbiw    r30, 0x01       ; 1
 2ce:   f1 f7           brne    .-4             ; 0x2cc <main+0x156>
 2d0:   00 c0           rjmp    .+0             ; 0x2d2 <main+0x15c>
 2d2:   00 00           nop
 2d4:   f7 cf           rjmp    .-18            ; 0x2c4 <main+0x14e>
 2d6:   ff ef           ldi     r31, 0xFF       ; 255
 2d8:   ef 1a           sub     r14, r31
 2da:   ff 0a           sbc     r15, r31
 2dc:   0b 5d           subi    r16, 0xDB       ; 219
 2de:   b1 cf           rjmp    .-158           ; 0x242 <main+0xcc>

000002e0 <_exit>:
 2e0:   f8 94           cli

000002e2 <__stop_program>:
 2e2:   ff cf           rjmp    .-2             ; 0x2e2 <__stop_program>
```

 Then I tried using `dogbolt` to try and get the source code of the file: I got this output from `Ghidra` on `dogbolt`. 
```c
#include "out.h"



// WARNING: This function may have set the stack pointer

void __vectors(void)

{
  undefined1 *puVar1;
  int __status;
  undefined1 *puVar2;
  undefined1 *puVar3;
  
  SREG = 0;
  puVar3 = &__data_load_start;
  for (puVar2 = &CSWTCH_14;
      (byte)puVar2 != 0x4e || (char)((uint)puVar2 >> 8) != (char)(((byte)puVar2 < 0x4e) + '\x01');
      puVar2 = puVar2 + 1) {
    puVar1 = (undefined1 *)ZEXT23(puVar3);
    puVar3 = puVar3 + 1;
    *puVar2 = *puVar1;
  }
  for (puVar2 = &timer0_overflow_count;
      (byte)puVar2 != 0x57 || (char)((uint)puVar2 >> 8) != (char)(((byte)puVar2 < 0x57) + '\x01');
      puVar2 = puVar2 + 1) {
    *puVar2 = 0;
  }
  Mem08fd = 0x66;
  __status = main();
                    // WARNING: Subroutine does not return
  exit(__status);
}



// WARNING: This function may have set the stack pointer

void __dtors_end(void)

{
  undefined1 *puVar1;
  int __status;
  undefined1 *puVar2;
  undefined1 *puVar3;
  
  SREG = 0;
  puVar3 = &__data_load_start;
  for (puVar2 = &CSWTCH_14;
      (byte)puVar2 != 0x4e || (char)((uint)puVar2 >> 8) != (char)(((byte)puVar2 < 0x4e) + '\x01');
      puVar2 = puVar2 + 1) {
    puVar1 = (undefined1 *)ZEXT23(puVar3);
    puVar3 = puVar3 + 1;
    *puVar2 = *puVar1;
  }
  for (puVar2 = &timer0_overflow_count;
      (byte)puVar2 != 0x57 || (char)((uint)puVar2 >> 8) != (char)(((byte)puVar2 < 0x57) + '\x01');
      puVar2 = puVar2 + 1) {
    *puVar2 = 0;
  }
  Mem08fd = 0x66;
  __status = main();
                    // WARNING: Subroutine does not return
  exit(__status);
}



// WARNING: This function may have set the stack pointer

void __vector_22(void)

{
  undefined1 *puVar1;
  int __status;
  undefined1 *puVar2;
  undefined1 *puVar3;
  
  SREG = 0;
  puVar3 = &__data_load_start;
  for (puVar2 = &CSWTCH_14;
      (byte)puVar2 != 0x4e || (char)((uint)puVar2 >> 8) != (char)(((byte)puVar2 < 0x4e) + '\x01');
      puVar2 = puVar2 + 1) {
    puVar1 = (undefined1 *)ZEXT23(puVar3);
    puVar3 = puVar3 + 1;
    *puVar2 = *puVar1;
  }
  for (puVar2 = &timer0_overflow_count;
      (byte)puVar2 != 0x57 || (char)((uint)puVar2 >> 8) != (char)(((byte)puVar2 < 0x57) + '\x01');
      puVar2 = puVar2 + 1) {
    *puVar2 = 0;
  }
  Mem08fd = 0x66;
  __status = main();
                    // WARNING: Subroutine does not return
  exit(__status);
}



// WARNING: Unknown calling convention -- yet parameter storage is locked
// z1()

byte z1(void)

{
  byte bVar1;
  
  bVar1 = DAT_mem_000b;
  DAT_mem_000b = bVar1 & 7;
  bVar1 = DAT_mem_0005;
  DAT_mem_0005 = bVar1 & 0xfc;
  return bVar1 & 0xfc;
}



undefined2 __vector_16(undefined2 param_1)

{
  int iVar1;
  undefined1 uVar2;
  undefined1 uVar3;
  char cVar4;
  byte bVar5;
  char cVar6;
  byte bVar7;
  uint uVar8;
  char in_Cflg;
  bool bVar9;
  char in_Zflg;
  char in_Nflg;
  char in_Vflg;
  char in_Sflg;
  char in_Hflg;
  char in_Tflg;
  char in_Iflg;
  
  uVar2 = timer0_millis._0_1_;
  uVar3 = timer0_millis._1_1_;
  uVar8 = CONCAT11(uVar3,uVar2);
  bVar5 = timer0_millis._2_1_;
  cVar6 = timer0_millis._3_1_;
  cVar4 = timer0_fract;
  bVar7 = cVar4 + 3;
  if (bVar7 < 0x7d) {
    iVar1 = 1;
    bVar9 = 0xfffe < uVar8;
  }
  else {
    bVar7 = cVar4 + 0x86;
    iVar1 = 2;
    bVar9 = 0xfffd < uVar8;
  }
  timer0_fract = bVar7;
  timer0_millis._0_1_ = (char)(uVar8 + iVar1);
  timer0_millis._1_1_ = (char)(uVar8 + iVar1 >> 8);
  timer0_millis._2_1_ = bVar5 + bVar9;
  timer0_millis._3_1_ = cVar6 + CARRY1(bVar5,bVar9);
  uVar2 = timer0_overflow_count;
  uVar3 = DAT_mem_014f;
  uVar8 = CONCAT11(uVar3,uVar2);
  bVar7 = DAT_mem_0150;
  cVar4 = DAT_mem_0151;
  timer0_overflow_count = (char)(uVar8 + 1);
  DAT_mem_014f = (char)(uVar8 + 1 >> 8);
  DAT_mem_0150 = bVar7 + (0xfffe < uVar8);
  DAT_mem_0151 = cVar4 + CARRY1(bVar7,0xfffe < uVar8);
  SREG = in_Cflg == '\x01' | (in_Zflg == '\x01') << 1 | (in_Nflg == '\x01') << 2 |
         (in_Vflg == '\x01') << 3 | (in_Sflg == '\x01') << 4 | (in_Hflg == '\x01') << 5 |
         (in_Tflg == '\x01') << 6 | (in_Iflg == '\x01') << 7;
  return param_1;
}



// WARNING: Removing unreachable block (code,0x000144)

void main(void)

{
  char cVar1;
  uint uVar2;
  byte bVar3;
  byte bVar4;
  byte bVar5;
  char cVar6;
  int iVar7;
  undefined1 auStack_4 [4];
  
  bVar3 = DAT_mem_0024;
  DAT_mem_0024 = bVar3 | 2;
  bVar3 = DAT_mem_0024;
  DAT_mem_0024 = bVar3 | 1;
  bVar3 = DAT_mem_0025;
  DAT_mem_0025 = bVar3 | 2;
  bVar3 = DAT_mem_0025;
  DAT_mem_0025 = bVar3 | 1;
  bVar3 = DAT_mem_006e;
  DAT_mem_006e = bVar3 | 1;
  DAT_mem_0081 = 0;
  bVar3 = DAT_mem_0081;
  DAT_mem_0081 = bVar3 | 2;
  bVar3 = DAT_mem_0081;
  DAT_mem_0081 = bVar3 | 1;
  bVar3 = DAT_mem_0080;
  DAT_mem_0080 = bVar3 | 1;
  bVar3 = DAT_mem_00b1;
  DAT_mem_00b1 = bVar3 | 4;
  bVar3 = DAT_mem_00b0;
  DAT_mem_00b0 = bVar3 | 1;
  bVar3 = DAT_mem_007a;
  DAT_mem_007a = bVar3 | 4;
  bVar3 = DAT_mem_007a;
  DAT_mem_007a = bVar3 | 2;
  bVar3 = DAT_mem_007a;
  DAT_mem_007a = bVar3 | 1;
  bVar3 = DAT_mem_007a;
  DAT_mem_007a = bVar3 | 0x80;
  DAT_mem_00c1 = 0;
  bVar3 = DAT_mem_000a;
  DAT_mem_000a = bVar3 | 0xf8;
  bVar3 = DAT_mem_0004;
  DAT_mem_0004 = bVar3 | 3;
  bVar3 = DAT_mem_000a;
  DAT_mem_000a = bVar3 & 0xfb;
  bVar3 = DAT_mem_000b;
  DAT_mem_000b = bVar3 & 7;
  bVar3 = DAT_mem_0005;
  DAT_mem_0005 = bVar3 & 0xfc;
  do {
    while (bVar3 = DAT_mem_0009, ((bVar3 ^ bVar3 * '\x02') & 4) == 0) {
      z1();
    }
    uVar2 = 0x68;
    bVar3 = 0;
    while( true ) {
      bVar5 = *(byte *)(uint3)uVar2;
      if ((bVar5 == 0) || (bVar5 == 0xa5)) goto LAB_code_000131;
      bVar4 = DAT_mem_0009;
      if (((bVar4 ^ bVar4 * '\x02') & 4) == 0) break;
      bVar5 = (bVar5 ^ 0xa5) - 0x30;
      bVar4 = 0;
      if (bVar5 < 0x4e) {
        bVar4 = *(byte *)CONCAT11(1,bVar5);
      }
      z1();
      if ((bVar4 & 1) != 0) {
        bVar5 = DAT_mem_000b;
        DAT_mem_000b = bVar5 | 8;
      }
      if ((bVar4 & 2) != 0) {
        bVar5 = DAT_mem_000b;
        DAT_mem_000b = bVar5 | 0x10;
      }
      if ((bVar4 & 4) != 0) {
        bVar5 = DAT_mem_000b;
        DAT_mem_000b = bVar5 | 0x20;
      }
      if ((bVar4 & 8) != 0) {
        bVar5 = DAT_mem_000b;
        DAT_mem_000b = bVar5 | 0x40;
      }
      if ((bVar4 & 0x10) != 0) {
        bVar5 = DAT_mem_000b;
        DAT_mem_000b = bVar5 | 0x80;
      }
      if ((bVar4 & 0x20) != 0) {
        bVar5 = DAT_mem_0005;
        DAT_mem_0005 = bVar5 | 1;
      }
      if ((bVar4 & 0x40) != 0) {
        bVar5 = DAT_mem_0005;
        DAT_mem_0005 = bVar5 | 2;
      }
      cVar6 = (bVar3 & 0x1f) + 0x2d;
      while (cVar1 = cVar6 + -1, cVar6 != '\0') {
        iVar7 = 3999;
        do {
          iVar7 = iVar7 + -1;
          cVar6 = cVar1;
        } while (iVar7 != 0);
      }
      uVar2 = CONCAT11((char)(uVar2 >> 8) - (((char)uVar2 != -1) + -1),(char)uVar2 + '\x01');
      bVar3 = bVar3 + 0x25;
    }
    z1();
LAB_code_000131:
    auStack_4._0_3_ = 0;
    while( true ) {
      if (auStack_4[1] != '\0' && (auStack_4[0] < 0x2c) <= (byte)(auStack_4[1] - 1U)) break;
      auStack_4[2] = 0;
      auStack_4._0_2_ = auStack_4._0_2_ + 1;
    }
  } while( true );
}



void exit(int __status)

{
  do {
                    // WARNING: Do nothing block with infinite loop
  } while( true );
}




```
 In the `main` function, I could see how the values was getting stored at `0x68` address XORed with `0xa5`
```c
do {
    while (bVar3 = DAT_mem_0009, ((bVar3 ^ bVar3 * '\x02') & 4) == 0) {
      z1();
    }
    uVar2 = 0x68;
    bVar3 = 0;
    while( true ) {
      bVar5 = *(byte *)(uint3)uVar2;
      if ((bVar5 == 0) || (bVar5 == 0xa5)) goto LAB_code_000131;
      bVar4 = DAT_mem_0009;
      if (((bVar4 ^ bVar4 * '\x02') & 4) == 0) break;
      bVar5 = (bVar5 ^ 0xa5) - 0x30;
      bVar4 = 0;
      if (bVar5 < 0x4e) {
        bVar4 = *(byte *)CONCAT11(1,bVar5);
      }
```
 Then I opened `hexedit` and looked at the values stored at the offset `0x68`, I found the following values
```
 F1 E3 E6 E6  F1 E3 DE F1  CD 94 D6 FA 94 D6 FA D6  CA C8 96 FA  D6 94 C8 D5  C9 96 FA 91  D7 C1 D0 94  CB CA FA C3 94 D7 C8 D2  91 D7 C0 D8  00
```
 Decrypting using the cipher key `0xA5` on `dcode.fr` with XOR Cipher, I got the following output,
	
## Flag: `TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}`





