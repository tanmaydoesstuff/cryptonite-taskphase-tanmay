## gdb baby step 1

**Flag: `picoCTF{549698}`**
**Difficulty:** `medium`

We use `gdb` to find all the functions. 
Then disassemble the `main` function.

Here is the terminal snippet,
```
tnmyhere@Luna:~/Desktop$ gdb debugger0_a
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04.2) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from debugger0_a...
(No debugging symbols found in debugger0_a)
(gdb) set disassembly-flavor intel
(gdb) info functions
All defined functions:

Non-debugging symbols:
0x0000000000001000  _init
0x0000000000001030  __cxa_finalize@plt
0x0000000000001040  _start
0x0000000000001070  deregister_tm_clones
0x00000000000010a0  register_tm_clones
0x00000000000010e0  __do_global_dtors_aux
0x0000000000001120  frame_dummy
0x0000000000001129  main
0x0000000000001140  __libc_csu_init
0x00000000000011b0  __libc_csu_fini
0x00000000000011b8  _fini
(gdb) disassemble main
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   rbp
   0x000000000000112e <+5>:     mov    rbp,rsp
   0x0000000000001131 <+8>:     mov    DWORD PTR [rbp-0x4],edi
   0x0000000000001134 <+11>:    mov    QWORD PTR [rbp-0x10],rsi
   0x0000000000001138 <+15>:    mov    eax,0x86342
   0x000000000000113d <+20>:    pop    rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
```

We find that the eax register value is `0x86342`. Converting this to decimal gives us `549698`.

**Learnt:** eax registers, gdb commands

**Challenges/Wrong Approaches:**
I had to change the disassembly flavor to `intel` from `at&t`.

**References:**
https://visualgdb.com/gdbreference/commands/disassemble
https://keleshev.com/eax-x86-register-meaning-and-history/

---
## vault door 3

**Flag: `picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_c79a21}`**
**Difficulty:** `medium`

In this challenge, we are given a challenge file. We try to understand and reverse the code to obtain the flag.

```
def rev():
    str = "jU5t_a_sna_3lpm12g94c_u_4_m7ra41"

    pswd = [None] * 32

    for i in range(8):
        pswd[i] = str[i]

    for i in range(8, 16):
        pswd[23 - i] = str[i]

    for i in range(16, 32, 2):
        pswd[46 - i] = str[i]

    for i in range(31, 16, -2):
        pswd[i] = str[i]

    new_pswd = ""
    for char in pswd:
        if char is not None:
            new_pswd += char
    return new_pswd

flag = rev()
print("The flag is: picoCTF{" + flag + "}")
```

We obtain the flag `picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_c79a21}`

**Learnt:** reversing a program, byte arrays in java

**Challenges/Wrong Approaches:**
Faced some problems understanding the code, but it became clearer on reading up on byte arrays in java.

**References:**
https://bito.ai/resources/java-byte-array-java-explained

---
## ARMssembly 1
**Flag: `picoCTF{0000001b}`**
**Difficulty:** `hard`

In this challenge we have been given an assembly file, going through it bit by bit,
```
	.arch armv8-a
	.file	"chall_1.c"
	.text
	.align	2
	.global	func
	.type	func, %function
```

This gives us information that the architecture being used is ARM v8a. We can refer to online ARM64 documentation to get well versed with it.

Now we have the `func`,
```
func:
	sub	sp, sp, #32
	str	w0, [sp, 12]
	mov	w0, 81
	str	w0, [sp, 16]
	str	wzr, [sp, 20]
	mov	w0, 3
	str	w0, [sp, 24]
	ldr	w0, [sp, 20]
	ldr	w1, [sp, 16]
	lsl	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 24]
	sdiv	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 12]
	sub	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w0, [sp, 28]
	add	sp, sp, 32
	ret
	.size	func, .-func
	.section	.rodata
	.align	3
```

The following operations happen on the stack:

We can understand these one by one to understand how the stack operates here. First, the stack is initialized by subtracting 32 from the stack pointer (`sp`), allocating space on the stack.

Then, a `w0` (which will be very useful later) is stored at `sp + 12`.
Then `81` is written to `w0` (moved to it).
Then `w0` is stored at `sp + 16`. (`sp + 16` = `81`)
Then a `wzr` (zero register) is stored at `sp + 20`. (`sp + 20` = `0`)
Now, `3` is written to `w0`. (`w0` = `3`)
Now `w0` is stored at `sp + 24` (`sp + 24` = `3`)
Then, the value at `sp + 20` (which is `0`) is loaded into `w0`.
The value at `sp + 16` (which is `81`) is loaded into `w1`.
Then, an LSL (Logical Shift Left) operation is performed, where `w1` (which is `81`) is shifted left by `w0` (which is `0`). This results in `w0` being `81`.
Now `w0` (which is `81`) is stored at `sp + 28`.
Then, the value at `sp + 28` (which is `81`) is loaded into `w1`.
Next, the value at `sp + 24` (which is `3`) is loaded into `w0`.
Now, a signed division (`sdiv`) is performed, where `w1` (which is `81`) is divided by `w0` (which is `3`). This results in `w0` being `27`.
Then, the value of `w0` (which is `27`) is stored at `sp + 28`.
The value at `sp + 28` (which is `27`) is loaded into `w1`.
Next, the value at `sp + 12` (the original value of `w0` passed into the function) is loaded into `w0`.
Then, `w1` (which is `27`) is subtracted by `w0` (the original value). The result is stored back into `w0`.
Now `w0` (the result of the subtraction, `27-w0`) is stored at `sp + 28`.
Then, the value at `sp + 28` (which is the result of the subtraction) is loaded back into `w0`.

Finally, the stack pointer is restored by adding 32 to `sp`, effectively deallocating the stack space that was reserved earlier. The function is returned.

Looking at the main function,
```
stp    x29, x30, [sp, -48]!    // Pushes x29 (Frame Pointer) and x30 (Link Register) by 48 i.e. allocates 48 bytes
add    x29, sp, 0              // Sets x29 (FP) to current sp
str    w0, [x29, 28]           // Stores w0 (argc) [argc is the number of arguments stored (the first argument is always the program name)] at x29+28
str    x1, [x29, 16]           // Store x1 (argv) [a pointer to an array of these arguments (argv[0] =program name for instance)] at x29+16
ldr    x0, [x29, 16]           // Load argv
add    x0, x0, 8               // Move to argv[1] (the first input argument)
ldr    x0, [x0]                // Dereference argv[1] meaning x0 now stores the actual value of argv[1] instead of address
bl     atoi                    // Call atoi with argv[1] i.e convert it into an integer
str    w0, [x29, 44]           // Store atoi result
ldr    w0, [x29, 44]           // Load the result
bl     func                    // Call func with atoi result as argument
cmp    w0, 0                   // Compare return value with 0


```

In the `main` function, the stack is initialized by first pushing the frame pointer (`x29`) and link register (`x30`) to allocate space for local variables. The program's argument count (`argc`) is stored at `x29+28`, and the argument vector (`argv`) is stored at `x29+16`. The first input argument (`argv[1]`) is loaded, and `atoi` is called to convert it into an integer, storing the result on the stack. The integer is passed to `func`, and its return value is compared to zero. If the result is zero, the message "You win!" is printed.

At the end, we know that func needs `27 - input = 0` (compare between `w0` and `0` at the end of `main`). Hence `input = 27`. Converting this to hex gives the flag `picoCTF{0000001b}`

**Learnt:** 
1. workings of a little bit of Assembly

**Challenges/Wrong Approaches:**
I didn't understand the code at all, at the start. After a lot of googling, wandering, and ChatGPT, I was able to work my way through. Hoof!

**References:**
https://courses.cs.washington.edu/courses/cse469/19wi/arm64.pdf

