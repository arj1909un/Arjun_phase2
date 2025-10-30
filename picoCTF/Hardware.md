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

