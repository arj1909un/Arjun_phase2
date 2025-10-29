# 1.Trivial Flag Transfer Protocol

I found a file named `tftp.pcapng`.

I had to lookup what a `.pcapng` file was and from what I could understand, it had to do something with packets and file transfer. This file can be opened with an application called Wireshark. `.pcapng` file is a packet capture file where packets are sniffed from a given network. 

I exported all the files from `tftp.pcapng` to a new folder and got the following(File->Export Objects->TFTP): 


1) `program.deb` -> This file was the installer for a Steganography tool called **Steghide**

2) `instructions.txt` -> Contained gibberish and had to run it through Cipher Identifier from https://dcode.fr. The tool detected that it was encoded using ROT13 Cipher. This is what it said after decoding: 
   `TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN`

3) `plan` -> This was a plain text file and contained gibberish. On running it through Cipher Identifier tool from https://dcode.fr, it found out that it was encoded using ROT13 Cipher.  On decoding this is what it said: 
   `IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS`

4) `picture1.bmp`, `picture2.bmp`, `picture3.bmp` , I used **Steghide** to extract data from these images.
   
   Command used: `steghide extract -sf picture*.bmp`
   it asked for the passphrase and using the `plan` file I assumed it would be `  `. On inputting the passphrase in `picture3.bmp`, it gave me a file called `flag.txt`, which contained the answer for the challenge. (**Note**: `picture1.bmp` and `picture2.bmp` resulted in error when using the above passphrase, only the third one worked which was the answer)


# flag: `picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}`



 # 2.tunn3l v1s10n 

Provided with a file called `tunn3l_v1s10n` . 
first instinct and the only thing that came into my mind was this:  
```
file tunn3l_v1s10n
tunn3l_v1s10n: data 
strings tunn3l_v1s10n

```

I could find anything interesting with the `file` and the `strings` command. Now my next thought was to look for the magic bytes. 
Magic bytes are the first few bytes in the header of a file, which actually define what type of file it is and its structure is. 
I used `xxd` to get the hexdump of the header.  

```
xxd -l 64 tunn3l_v1s10n
00000000: 424d 8e26 2c00 0000 0000 bad0 0000 bad0  BM.&,...........
00000010: 0000 6e04 0000 3201 0000 0100 1800 0000  ..n...2.........
00000020: 0000 5826 2c00 2516 0000 2516 0000 0000  ..X&,.%...%.....
00000030: 0000 0000 0000 231a 1727 1e1b 2920 1d2a  ......#..'..) .*
```

The `bad0` bytes is kinda suspicious. My next idea was to check which file type's initial bytes are `424d 8e26 2c00 0000`. 


After surfing the web i found out that this given file is a bitmap image file with some corruption.

This is how the windows bitmap header looks like: 
- `42 4D` → "BM" = signature for a Windows Bitmap file (.bmp)
- `8E 26 2C 00` → file size in bytes 
- `00 00` + `00 00` → reserved fields
- `36 00 00 00` → pixel array starts at offset 54 bytes, in my case it was `bad0`
- `28 00 00 00` → DIB header size = 40 bytes, `bad0` in my case

I have to replace the `bad0` bytes with the correct format. 

I used `hexedit` to replace the first `bad0` with `36 00` and the second one with `28 00`. 


This is how the header looked like after the initial correction: 
``` 
xxd -l 128 tunn3l_v1s10n_width_fixed
00000000: 424d 8e26 2c00 0000 0000 3600 0000 2800  BM.&,.....6...(.
00000010: 0000 6e04 0000 3201 0000 0100 1800 0000  ..n...2.........
00000020: 0000 5826 2c00 2516 0000 2516 0000 0000  ..X&,.%...%.....
00000030: 0000 0000 0000 231a 1727 1e1b 2920 1d2a  ......#..'..) .*
00000040: 211e 261d 1a31 2825 352c 2933 2a27 382f  !.&..1(%5,)3*'8/
00000050: 2c2f 2623 332a 262d 2420 3b32 2e32 2925  ,/&#3*&-$ ;2.2)%
00000060: 3027 2333 2a26 382c 2836 2b27 392d 2b2f  0'#3*&8,(6+'9-+/
00000070: 2623 1d12 0e23 1711 2916 0e55 3d31 9776  &#...#..)..U=1.v
```

The bytes after `36 00` and `28 00` are the ones which tell the width and the height of the image.  

 Width: `6e04 0000`, 0x046E = 1134 pixels
 Height:  `3201 0000`, 0x0132 = 306 pixels

I changed the height of the image to match the width and make it a proper square. 
  
Changed `3201 0000` -> `6e04 0000` using `hexedit`. 

The flag is now clearly visible in the picture. 
# flag: `picoCTF{qu1t3_a_v13w_2020}`




# 3.m00nwalk 


Provided with a file called `message.wav`. 


The first hint helped me find out which type of signal was contained in the wav file. 

Hint: How did pictures from the moon landing get sent back to Earth?
After looking up on google, this is what I found: 
-> NASA selected a scan converter manufactured by RCA to convert the black-and-white SSTV signals from the Apollo 7, 8, 9 and 11 missions.

SSTV -> Slow Scan Television.

By making use of this project on github, I can directly get the data without running it through a specialised program. 

https://github.com/colaclanth/sstv

By directly running the command : `sstv -d message.wav -o result.png` I got my image. 


# flag: `picoCTF{beep_boop_im_in_space}`





