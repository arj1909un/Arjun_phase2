# 1. Hide and Seek

## Description
Sakamoto’s at it again with a game of hide and seek, but this time, it’s not with Shin or his daughter. An old friend hid some secret data in this image. Can you find it before the others do?

Hint:

Even in retirement, Sakamoto never loses at hide and seek. Maybe stegseek can help you keep up.

## Solution
There was an image file attached with it called sakamoto.jpg 

So reading the description one can easily notice that it is clearly written "hid some secret data in this image", so there is obviously some hidden data in this image, then the biggest giveaway was in the hint "Maybe stegseek can help you keep up.", so I directly started to read about stegseek, where I found that the same way steghide is used to hide data into images, stegseek is used to crack that data i.e 'seek' it, so basically "it is a lightning fast steghide cracker that is thousands of times faster than other crackers" , this is what I found written in one of the resources that I was looking through while learning about stegseek, then this `rockyou.txt`, which this tool runs through is a wordlist that I looked up about and found: "The rockyou.txt wordlist, which emerged from a major data breach in 2009 contains stolen passwords and is commonly used by security professionals and hackers alike to brute-force passwords.", so it is basically a list of probable passwords, so it basically works something like this:

So i started by using stegseek on the image provided and i got an error of this sort:

```
arjun@arjun:~/Downloads$ stegseek sakamoto.jpg
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[!] error: could not open the wordlist "/usr/share/wordlists/rockyou.txt".

```

After surfing the web i found out that linux does not have the rockyou wordlist by default.
I had to download it online, then used the following command:

```
arjun@arjun:~/Downloads$ stegseek sakamoto.jpg -wl rockyou.txt
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "iloveyou1"
[i] Original filename: "flag.txt".
[i] Extracting to "sakamoto.jpg.out".


```

Finally I got the password and the hidden contents in the .out file and expected that it contained the flag because the orginal file name is also given as flag.txt, then on catting it out I found the flag!!!


## Flag
`nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}`

## Resources
- `https://weakpass.com/wordlists/rockyou.txt`, to install the wordlist and use it with stegseek.


# 2. Nutrela Chunks

## Description
One of my favorite foods is soya chunks. But as I was enjoying some Nutrela today, I noticed a few chunks weren’t quite right. Seems like something’s off with their structure. Could you help me fix these broken chunks so I can enjoy my meal again?

## Solution
There was a `.png`, image with the description, `a few chunks weren’t quite right`, `Seems like something’s off with their structure`, this gave me an idea to check the file's binary data, which must  have been corrupted, as is mentioned in the description "a few chunks are not right", so a few bytes must be wrong,so i checked the hexdump of the file.

<img width="1391" height="363" alt="Screenshot 2025-11-08 at 8 16 23 PM" src="https://github.com/user-attachments/assets/db3ab5ce-f484-4709-a9d0-9da8c32b04b3" />

Here are the first few lines, so here one would notice the bytes and corresponding to them, ASCII or text representation of those bytes, so I first checked the header for the `.png` extension, and I found that it is supposed to be `89 50 4E 47`, but was `89 70 6E 67`, so I first changed that and found that the .png written in the text representation changes to .PNG, i.e lowercase turned to upper case, then there was some more text written like `ihdr`, `idat`, which i knew about, then I opened a normal not damaged .png file and compared the hex dump with our corrupted image, and found that ihdr was written in caps and idat also written in caps, so I changed those, changing  `69 64 61 74` to `49 44 41 54`, and `69 68 64 72` to `49 48 44 52` using Hexeditor after which it still wouldnt open,the i went to the end of the hexdump and I found `6E 64 AE 42`, which was supposed to be `4E 44 AE 42`, then what I expected happened, the image openend and looked like:

<img width="1000" height="1000" alt="nutrela-2" src="https://github.com/user-attachments/assets/de10833e-42b2-4b15-83c9-45af6b51257b" />

## Flag
`nite{nOw_yOu_knOw_abOut_PNG_chunk5}`


# 3. RAR of the Abyss

## Description
Two philosophers peer into the networked abyss and swap a secret. Use the secret to decrypt the Abyss’ RAwR and pull your flag from the void.

## Solution
With the description there was a file attached called `abyss.pcap`, so this is a packet caputre file, which by prior knowledge I knew that it is supposed to be worked upon in wireshark, so I directly opened the file in wireshark, by the description I could figure out that two “philosophers” are exchanging a secret, and we've to first get the secret and then use it to decrypt the "Abyss’ RAwR", and pull the flag from it, and I'd read about rar somewhere before in another challenge it was an extension `.rar`, on searching about it I found that a .rar file is a proprietary archive file format, short for Roshal ARchive, that stores multiple files in a compressed form.

So I knew that wireshark lets one see at every message sent over a network and it is a network protocol analyzer used to capture and inspect live network traffic, that was the feature that I wanted to make use of.

So I typed onto google "how to see message sent over a netwrok in wireshark", where I found a method called `follow stream`, which is specifically used to see the complete data stream of a specific conversation.

This looked like the conversation itself that the two "philosphers" had, at the bottom I could see the format that it is in and the size and the stream option and some other stuff.

stream 2:

```
Rar!........!.....dE7.'.a.U...u..gC...f.C..._R...wOdp..&...ka*.sG.......qxr..]._....o......[*...V.DP..k.I..T.w~............1...:o:....KOd.q..9...Z.....|..aY.R...[..q8......}...A.!\..E..7L!.|.../.........)).{ja.....t....=...........A...j%..X..........y...k...q2....Ku..v.g...\y{.....R.J.
```
which obviously looked like ascii conversion of hex dump, which I saw while doing other challenge

after checking all the previous streams i stumbled at stream 4:

`Nietzsche: b3y0ndG00dand3vil`, which i figured was the Password.

Now I had to use it to find the flag.

I tried to use binwalk on abyss.pcap to extract any hidden data and i found a file named 530.rar and when i tried to open it i found out that it required a password, lucky for us we had found out the 
the password before, so i put the password and voila i got the flag.txt file which obviously had the flag we were looking for.


## Flag
`nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}`



# 4. NineTails
## Description:

Looks like I got a little too clever and hid the flag as a password in Firefox, tucked away like one of NineTails’ many tails. Recover the "logins" and the "key4" and let it guide you to the flag.

Hint:

I named my Ninetails "j4gjesg4", quite a peculiar name isn't it?

## Soution
We got a file called ninetails.rar. I extracted it:
`unar ninetails.rar`
This gave me a file named ninetails.ad1.

I looked up .ad1 and found that it is a forensic image used by AccessData.
To open it, we must use FTK Imager, so I opened the file in FTK Imager on a Windows system.

Inside it, I saw the Firefox profile folders. I searched for the one that matched the hint and found:

j4gjesg4.default-release

Inside it, I found the two important files:

key4.db

logins.json

These are used by Firefox to store and encrypt saved passwords.

The JSON file (logins.json) had encrypted usernames and passwords.
To decrypt them, I searched online and found a tool called firefox_decrypt.
I made a folder ctf_profile and copied:

key4.db
logins.json

Then I tried running the tool:

`python3 firefox_decrypt.py ~/downloads/ctf_profile`


It gave an error saying it could not initialize NSS.
This happens when the profile is missing another file: cert9.db.

So I went back to FTK Imager, found cert9.db, and copied it into ctf_profile.

Now the folder had:

key4.db
logins.json
cert9.db

I ran the tool again:

python3 firefox_decrypt.py ~/downloads/ctf_profile

This time it worked, and I got the decrypted passwords:

rehack.xyz →        GCTF{m0zarella
ctftime.org →       CHEEEEEEEEEEEEEEEEEEEEEEEEEESE
reddit →            _f1ref0x_
facebook →          SIKE
warlocksmurf.github.io → p4ssw0rd}


Joining all the password parts gives the final flag:

GCTF{m0zarellaCHEEEEEEEEEEEEEEEEEEEEEEEEEESE_f1ref0x_SIKEp4ssw0rd}

## Flag
`GCTF{m0zarellaCHEEEEEEEEEEEEEEEEEEEEEEEEEESE_f1ref0x_SIKEp4ssw0rd}`





