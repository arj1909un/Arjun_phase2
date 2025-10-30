# 1.RSA Oracle 

Contents of password.enc: 
```
1765037049764047724348114634473658734830490852066061345686916365658618194981097216750929421734812911680434647401939068526285652985802740837961814227312100
```

One of the hints of the challenge asked me to take a look at Chosen Plaintext attack:. 
#### Chosen Ciphertext Attack

Firstly, we have a piece of ciphertext we'll denote by :

```
C = t^e mod n 
```

Which is just normal RSA. What we have is normal ciphertext C. Now we can choose a plaintext, so lets say we choose 2 as our plaintext. We can now compute `Ca = 2^e mod n`. However to our program we send `Cb = Ca * C`, so now : 

```
Cb = C.2^e = (t^e*2^e) mod n 
```

Now since this is a chosen plaintext attack we're reliant on having access to the decryption of our substituted value - so now the program computes: 

```
(Cb)^d = [t^e*2^e]^d = (t^e)^d.(2^e)^d mod n 
```

However for any plaintext `x`, we know that `C = x^e` and `x = C^d`.  So: 

```
(x^e)^d = x mod n 
```

Therefore from the preceding result: 

```
(Cb)^d = t.2 mod n
```

Hence, when we get that value back, we can easily calculate `t`, but just simple halving it. 
This is possible with any integer, we took 2 here just as an example. 


#### Solve: 

So, this is how I implemented this in the given challenge: 
```
*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you?
E --> encrypt D --> decrypt.
E
enter text to encrypt (encoded length must be less than keysize): 1
1

encoded cleartext as Hex m: 31

ciphertext (m ^ e mod n) 4374671741411819653095065203638363839705760144524191633605358134684143978321095859047126585649272872908765432040943055399247499744070371810470682366100689
```
Here, the program accepts only characters as input, so I chose `1`, whose value in ASCII is 49, which is 0x31 in hex. therefore in the end we need to divide the hex by 0x31.  

Now we input Cb = C * Ca into the decryption program. 

```
what should we do for you?
E --> encrypt D --> decrypt.
D
Enter text to decrypt: 7721457704147667243014822210342015623441070646730334329369170960482434424280036173642970043315437595085392083961667520112650054794430310042790658505492250245959249047521482534760405619663668251566587006095670290533332380625235238831595584837302490911416483426164534051847266716546285422298258161162428036900
decrypted ciphertext as hex (c ^ d mod n): ac2c1742ee9
decrypted ciphertext:
ÂÁt.é
```

Now, we got a hex `ac2c1742ee9`, which we can now divide by `0x31` to get the final hex value, which is the original ciphertext. 


Now, `t = 0xac2c1742ee9 // 0x31 = 0x3838316439`

Now, converting this hex to ASCII, we get: `881d9`

Now this can be passed to `openssl` to decode the `secret.enc` file. 


```
> openssl enc -aes-256-cbc -d -in secret.enc -k  881d9
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
picoCTF{su((3ss_(r@ck1ng_r3@_881d93b6}
```

# flag: `picoCTF{su((3ss_(r@ck1ng_r3@_881d93b6}`





# 3.miniRSA 

After downlaoding the given file i found this text so  during citadel i learned about this website called dcode.fr(https://www.dcode.fr)

Ciphertext: 

```text
N: 29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673
e: 3

ciphertext (c): 2205316413931134031074603746928247799030155221252519872649649212867614751848436763801274360463406171277838056821437115883619169702963504606017565783537203207707757768473109845162808575425972525116337319108047893250549462147185741761825125 
```

So in that decoder i searched up RSA decoder after finding that it asked for
1.) Value of the cipher message (Integer) C=
2.) Public Key E (Usually E=65537) E=
3.) Public Key value (Integer) N=

All of which were given in the file provided, so i put all the values and dcode did its magic.

# flag: `picoCTF{n33d_a_lArg3r_e_ccaa7776}`






