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



