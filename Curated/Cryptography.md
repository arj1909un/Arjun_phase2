# 1. All Signs Align


## Solution:
this was the given code in the file chal.py:

```python
from random import randint
from Crypto.Util.number import *
from secret import flag

p = 9129026491768303016811207218323770273047638648509577266210613478726929333106121387323539916009107476349319902011390210650434835260358014251332047605739279

def get_x():
    while True:
        x = randint(2,p-1)
        if pow(x,(p-1)//2,p) == 1:
            break
    return x

def get_y():
    return -get_x()%p

a = randint(2,p-1)
out = []

for b in bin(bytes_to_long(flag))[2:]:
    if b == '0':
        out.append((a*get_x())%p)
    else:
        out.append((a*get_y())%p)

with open('out.txt','w') as f:
    f.write(str(out)+'\n')
```
and we had a file called out.txt that held a list of large integers, those numbers represented the encrypted flag bits.
now we will break the code line by line and try to understand whats going on.

```
from random import randint
from Crypto.Util.number import *
from secret import flag
```
This script is telling us the following:

1.Python’s random.randint for picking random integers
2.Crypto.Util.number for big number & byte manipulation
3.A secret flag imported from secret.py
4.The variable p is a large prime number, acting as the modulus for all operations.

```
def get_x():
    while True:
        x = randint(2,p-1)
        if pow(x,(p-1)//2,p) == 1:
            break
    return x
```
This picks a random x such that it is a quadratic residue modulo p.

`pow(x, (p-1)//2, p)`

returns:
1. 1 → x is a quadratic residue
2. p-1 → x is a non-residue
3. The loop continues until an x with value 1 is found.

```
def get_y():
    return -get_x() % p
```
So y is simply the negative of another valid x.
after which the code tell us that a random multiplier a is chosen.
For each bit of the flag:
If the bit is 0: output a * x mod p
If the bit is 1: output a * y mod p
Thus every output number is either a multiple of some residue (x) or a multiple of its negation (y).

The list is saved to out.txt.

Now, the only difference between the output for bit 0 and bit 1 was that it was multiplied by -1 for bit 1. Since -1 is a non-quadratic residue of the given p, the bit could be decrypted by using the multiplicity property of quadratic and non-quadratic residues.
So `legs[]` contains either `1` or `p-1` based on which the correspoing bit can be found.
So there were 2 possible mappings for each bit, based on the Legrande symbol of `a`.
- If leg(a) = 1 then outputs are 1 (for bit 0) and p-1 (for bit 1) and if leg(a) = p-1 then outputs are p-1 (for bit 0) and 1 (for bit 1).
- Trying both configurations, the latter produced readable text.
- The code below gave the flag.


```python
from Crypto.Util.number import *

p = 9129026491768303016811207218323770273047638648509577266210613478726929333106121387323539916009107476349319902011390210650434835260358014251332047605739279


s = open("out.txt",'r').read()
s = s.strip()[1:-1]
out = list(map(int,s.split(',')))       # Take each individual number in list

legs=[]
for output in out:
    legs.append(pow(output, (p - 1) // 2, p))  # Legendre symbol

def bitConvert(legs):
    bits = ""
    for l in legs:
        if l==p-1:
            bits = bits + '0'
        elif l==1:
            bits = bits + '1'
    return bits
            
bits = bitConvert(legs)
bytes = long_to_bytes(int(bits,2))
print(bytes.decode())
```


## Flag:

```
nite{r3s1du35_f4ll1ng_1nt0_pl4c3}
```


# 2. Residue Refinery


## Solution:

When I checked how class Num multiplies values, I noticed it behaves like a simple linear transformation using two numbers at a time. So the encryption was basically a small matrix operation done on every 2-byte block.

The ciphertext had the bytes in the reverse order (each pair was stored as C2, C1 instead of C1, C2), so before decoding I had to swap every pair.

The challenge luckily gave ciphertext for the first two plaintext bytes. Using this, it was possible to mathematically work out the exact coefficients of the transformation matrix used in encryption.

Once the matrix was recovered, I computed its inverse modulo 257. Then I applied this inverse matrix to each ciphertext pair, which successfully gave back the original bytes. The output formed the flag.

```python
p = 257
ct_hex = "9813d3838178abd17836f3e2e752a99d5cd3fba291205f90c1d0a78b6eca"
ct = bytes.fromhex(ct_hex)
x1, x2 = 0x31, 0x6d

def modinv(a, m):
    return pow(a, -1, m)

a = x1
b = x2
C2_init, C1_init = ct[0], ct[1]

det = (a*a - 3*b*b) % p
inv_det = modinv(det, p)
k1 = (inv_det * ((a*C1_init - 3*b*C2_init) % p)) % p
k2 = (inv_det * ((-b*C1_init + a*C2_init) % p)) % p

coeffA, coeffB, coeffC, coeffD = k1, (3*k2) % p, k2, k1
detA = (coeffA * coeffD - coeffB * coeffC) % p
invA = modinv(detA, p)

plain = bytearray()
for i in range(0, len(ct), 2):
    C2, C1 = ct[i], ct[i+1]
    f0 = (invA * ((coeffD * C1 - coeffB * C2) % p)) % p
    f1 = (invA * ((-coeffC * C1 + coeffA * C2) % p)) % p
    plain.extend([f0, f1])

print(plain.decode())
```


## Flag:

```
nite{1mp0r7_m0dul3?_1_4M_7h3_m0dul3}
```



# 3. Quixorte


## Solution:

The given PNG file wasn’t normal—it was encrypted using two operations:
1. a sliding XOR, and
2. rotating each byte by a certain amount.

Luckily, PNG files always start with the same 8 fixed bytes. Because we know what the first 8 bytes should be, we can compare them with the encrypted ones and recover the key used for XOR.

The small script below calculates the key by rotating the known PNG header in the same way the encryption did, then XORing it with the encrypted header.

```python
png = bytes([0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A])

with open("quote.png.enc", "rb") as f:
    enc = f.read(8)
    
def ror(b, i):
    s = i % 8
    return ((b >> s) | (b << (8 - s)) & 0xFF) & 0xFF

rot = bytes(ror(png[i], i) for i in range(8))
S = [enc[i] ^ rot[i] for i in range(8)]

key = [S[0]]
for i in range(1, 8):
    key.append(S[i] ^ S[i-1])

print(bytes(key).hex())
```
Once we know the key, we can apply the usual XOR rule:
ciphertext XOR key = original plaintext

After undoing the XOR, the bytes still look wrong because they were rotated earlier.
To fix this, we rotate them back to the left to restore the original PNG bytes.
 
```python
key = bytes.fromhex("ec95e0220a3d5ab7")
enc = bytearray(open("quote.png.enc", "rb").read())

def rol(b, i):
    s = i % 8
    return ((b << s) & 0xFF) | (b >> (8 - s))

for i in reversed(range(len(enc) - len(key) + 1)): # Undo XOR
    for j in range(len(key)):
        enc[i + j] ^= key[j]

flag = bytearray(rol(enc[i], i) for i in range(len(enc))) # Undo Rotation

image = open("quote.png", "wb")
image.write(flag)
```



## Flag:

```
nite{t0_b3_X0R_n0t_t0_b3333}
```


## Concepts learnt:

XOR rules:

- plaintext ^ key = ciphertext
- ciphertext ^ key = plaintext
- plaintext ^ ciphertext = key
- How left and right byte rotations work
- Magic numbers (fixed header bytes used to identify file types)



