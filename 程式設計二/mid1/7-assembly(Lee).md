# Assembly #
## Outline ##
- How to get assembly code?
- From C to assembly code
- gcc inline assembly
- Option: advanced syntaxes in assembly
  * Pointers
  * Arrays
  * Function calls
## HOW TO GET ASSEMBLY CODE? ##
### What is assembly? ###
C codes (or any high level programs) are very different from the machine code
- C code 
```C
 #include<stdio.h>
 
 int main(){
     printf("Hello World");
     return 0;
 }
```
- Assembly code
```
  section .text
      global _start
  _start:
    mov edx, len
    mov ecx, msg
    mov ebx, 1
    mov eax, 4
    int 0x80
    mov eax, 1
    int 0x80
  section .data
  
  msg db 'Hello, world!',0xa
  len equ $ - msg    
```
### How to get the assembly code? ###
- In the terminal, use gcc -S ex1.c to get the assembly code of ex1.c 
- In today’s lecture, we use x86 CPU and intel style’s assembly
  * gcc -S -masm=intel ex1.c
- To generate executable, you can compile the assembly code directly
  * gcc ex1.s
## FROM C TO ASSEMBLY CODE ##
### Example 1: assignment 1 ###
- C code
```C
  int a;
  int main(void){
      a = 3;
  }
```
- Assembly code
```C
   mov DWORD PTR _a, 3 
```
### MOV instruction ###
- MOV is an instruction in x86’s architecture
```
MOV destination, source
```
- In example 1, destination is a memory address, named _a, and source is a constant value (3).
- DWORD specifies the data size.

|Type Specifier|Bytes addressed|
|-----------|-------------|
|BYTE|1|
|WORD|2|
|DWORD|4|
|QWORD|8|
### Example 2: assignment 2 ###
- C code
 ```C
 int main(void){
    int a;
    a = 3;
 }
 ```
- Assembly code
```
mov DWORD PTR [esp+12], 3
```
What’s differences between ex1 and ex2?
### Registers in x86 CPU ###
![image](images/7-1.JPG)
### Indirect memory access ###
- Register esp holds an address of memory.
- [esp+12] is a memory address, whose cell is 12 bytes below the memory cell addressed [esp].
### Analogy of seat arrangement ###
- Number all seats from 0, 4, 8, …
- Write a number on the blackboard as the content of esp.
- Ask where is [esp+12], and put 3 to that seat.
- Change the number of esp, and ask again.
### Global variable vs. local variable ###
Variable a in example 1 is a global variable,and in example 2 is a local variable.
![image](images/7-2.JPG)
### Example 3: assignment 3 ###
- C code
```C
int main(void){
  int a, b;
  b = a;
}
```
- Assembly code
```C
mov eax, DWORD PTR [esp+12]
mov DWORD PTR [esp+8], eax
```
### Syntax of MOV ###
```
MOV destination, source
```
- In the syntax of MOV, none of them are destination=mem and source=mem
  *  Why?
- mem can be a direct address or an indirect address
- A single b=a needs 2 instructions.
```
Valid MOV syntax
mov <reg>,<reg>
mov <reg>,<mem>
mov <mem>,<reg>
mov <reg>,<const>
mov <mem>,<const>    
```
### Example 4: Add ###
- C code
```C
 int main(void){
 int a,b,c;
 c = a + b;
 }
```
- Assembly code
```
mov eax, DWORD PTR [esp+8]
mov edx, DWORD PTR [esp+12]
add eax, edx
mov DWORD PTR [esp+4], eax
```
### ADD instruction ###
- The add instruction adds together its two operands, storing the result in its first operand.
- Ex: add BYTE PTR [var], 10 
   * add 10 to the single byte stored at memory address var
- Ex: add eax, 10
   * EAX ← EAX + 10
```
Valid add syntax
add <reg>,<reg>
add <reg>,<mem>
add <mem>,<reg>
add <reg>,<con>
add <mem>,<con>
```
### Example 5: If-else ###
- C code
```C
int main(void){
int a, b;
if (a>3)
b++;
}
```
- Assembly code
```
cmp DWORD PTR [esp+12], 3
jle L3
add DWORD PTR [esp+8], 1
L3:
  leave
```
### Cmp and jle instructions ###
- cmp compares the values of the two specified operands, setting the condition codes in the machine status word.
  * Machine status word is another register in x86 CPU
- jle label jumps to the label if the machine status word shows “less than or equal to”
### Conditional jump ###
- je <label> (jump when equal)
- jne <label> (jump when not equal)
- jz <label> (jump when last result was zero)
- jg <label> (jump when greater than)
- jge <label> (jump when greater than or equal to)
- jl <label> (jump when less than)
- jle <label> (jump when less than or equal to)
### Instruction pointer (IP) ###
- The program (a list of instructions) is stored in memory.
- Which instruction to be executed is specified by a special register, called instruction pointer.
   * In some place, it is called “program counter” (PC).
- Normally, IP increases 4 bytes (an instruction).But for jump <label>, it will become the address of the instruction after the label.
- <label> represents a memory address.
### Example 6: while loop ###
- C code
```C
int main(void){
int a, b;
while(a<=3) b++;
}
```
- Assembly code
```C
   jmp L2
L3:
   add DWORD PTR [esp+12], 1
L2:
   cmp DWORD PTR [esp+8], 3
   jle L3
   leave
```
