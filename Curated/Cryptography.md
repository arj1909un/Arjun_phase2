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



# 4. Willy's Chocolate Experience


## Solution:

- The challenge takes the flag, converts it into a big integer using bytes_to_long, and calls that number the ticket.

- We don’t know the ticket, but the program gives us two outputs of a function:
  f(m) = 13^m + 37^m (mod p)
  for two consecutive values of m.
  From these two results, our goal is to recover m, which eventually gives us the flag.

- After rearranging and simplifying the expressions, the problem becomes a discrete logarithm problem.

- To solve this, we use:
- Baby-Step Giant-Step (BSGS) for faster brute-force over groups
- Pohlig–Hellman algorithm, which breaks the problem into smaller parts using factorization

- Each smaller solution gives a modular result, and all these pieces are combined using the Chinese Remainder Theorem (CRT) to get the final value of (m – 1).

- Once we add 1 to this result, we get the correct m.
  Converting m back to bytes reveals the flag.

```python
from collections import Counter
from Crypto.Util.number import inverse, long_to_bytes

p = 396430433566694153228963024068183195900644000015629930982017434859080008533624204265038366113052353086248115602503012179807206251960510130759852727353283868788493357310003786807
A = 208271276785711416565270003674719254652567820785459096303084135643866107254120926647956533028404502637100461134874329585833364948354858925270600245218260166855547105655294503224
B = 124499652441066069321544812234595327614165778598236394255418354986873240978090206863399216810942232360879573073405796848165530765886142184827326462551698684564407582751560255175
g = 37

x = ((A - 13 * B) * inverse(24, p)) % p

def factorTrial(n, bound=2000000):
    f = Counter()
    d = 2
    while d * d <= n and d <= bound:
        while n % d == 0:
            f[d] += 1
            n //= d
        d = 3 if d == 2 else d + 2
    return f, n

def bsgs(base, target, modp, limit):
    m = int(pow(limit,0.5)) + 1
    table = {}
    cur = 1
    for j in range(m):
        if cur not in table:
            table[cur] = j
        cur = (cur * base) % modp

    step = pow(inverse(base, modp), m, modp)
    gamma = target
    for i in range(m + 1):
        if gamma in table:
            return (i * m + table[gamma]) % limit
        gamma = (gamma * step) % modp
    return None

def hellman(base, target, modp):
    N = modp - 1
    facs, rem = factorTrial(N)
    if rem != 1:
        facs[int(rem)] += 1

    parts = []
    for q, e in facs.items():
        pe = q ** e
        g0 = pow(base, N // pe, modp)
        h0 = pow(target, N // pe, modp)
        t = bsgs(g0, h0, modp, pe)
        parts.append((t, pe))

    M = 1
    for _, ni in parts:
        M *= ni

    xcrt = 0
    for ai, ni in parts:
        mi = M // ni
        xcrt = (xcrt + ai * mi * inverse(mi, ni)) % M
    return xcrt

k = hellman(g, x, p)
m = k + 1
print(long_to_bytes(m).decode())
```


## Flag:

```
nite{g0ld3n_t1ck3t_t0_gl4sg0w}
```


## Concepts learnt:

- Pohlig–Hellman algorithm for solving discrete logs
- Baby-Step Giant-Step for faster discrete log solvin
- Chinese Remainder Theorem for combining modular solutions



