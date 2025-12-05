# Nutrela Chunks
## Description
One of my favorite foods is soya chunks. But as I was enjoying some Nutrela today, I noticed a few chunks weren’t quite right. Seems like something’s off with their structure. Could you help me fix these broken chunks so I can enjoy my meal again?

## Solution
There was a `.png`, image with the description, `a few chunks weren’t quite right`, `Seems like something’s off with their structure`, this gave me an idea to check the file's binary data, which must  have been corrupted, as is mentioned in the description "a few chunks are not right", so a few bytes must be wrong,so i checked the hexdump of the file.

<img width="1391" height="363" alt="Screenshot 2025-11-08 at 8 16 23 PM" src="https://github.com/user-attachments/assets/db3ab5ce-f484-4709-a9d0-9da8c32b04b3" />

Here are the first few lines, so here one would notice the bytes and corresponding to them, ASCII or text representation of those bytes, so I first checked the header for the `.png` extension, and I found that it is supposed to be `89 50 4E 47`, but was `89 70 6E 67`, so I first changed that and found that the .png written in the text representation changes to .PNG, i.e lowercase turned to upper case, then there was some more text written like `ihdr`, `idat`, which i knew about, then I opened a normal not damaged .png file and compared the hex dump with our corrupted image, and found that ihdr was written in caps and idat also written in caps, so I changed those, changing  `69 64 61 74` to `49 44 41 54`, and `69 68 64 72` to `49 48 44 52` using Hexeditor after which it still wouldnt open,the i went to the end of the hexdump and I found `6E 64 AE 42`, which was supposed to be `4E 44 AE 42`, then what I expected happened, the image openend and looked like:

<img width="1000" height="1000" alt="nutrela-2" src="https://github.com/user-attachments/assets/de10833e-42b2-4b15-83c9-45af6b51257b" />

## Flag
`nite{nOw_yOu_knOw_abOut_PNG_chunk5}`


