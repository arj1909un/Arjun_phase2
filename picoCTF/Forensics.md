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

