# Assembly #
## HOW TO GET ASSEMBLY CODE? ##
### What is assembly? ### 
C codes (or any high level programs) are verydifferent from the machine code
```C
#include <stdio.h>
int main(){
printf(“Hello World!”);
return 0;
}
```
```C
section .text
 global _start
_start:
mov edx, le

mov ecx, msg
mov ebx, 1
mov eax, 4
int 0x80
mov eax, 1
int 0x80

section .data

msg db 'Hello,World!',0xa
len equ $ - msg
```

### How to get the assembly code? ###
- In the terminal,use gcc -S ex1.c to get the assembly code of ex1.c
- In today’s lecture, we use x86 CPU and intelstyle’s assembly
  * gcc -S -masm=intel ex1.c
- To generate executable,you can compile the assembly code directly
  * gcc ex1.s
## FROM　C TO ASSEMBLY CODE ##
### Example 1: assignment 1 ###
- C code
```C
  int a;
  int main(Void){
      a = 3;
```
- Assembly code
```C
  mov DWORD PTR _a,3
```
### MOV instruction ###
- MOV is an instruction in x86's architecture
  ```C
   MOV destination, source
  ```
- In example 1,destination is a memory address,named _a,and source is a constant value(3).
- DWORD specifies the data size.

|Type Specifier|Bytes addressed|
|--------------|---------------|
|BYTE|1|
|WORD|2|
|DWORD|4|
|QWORD|8|
### Example 2:assignment 2 ###
- C code
 ```C
 int main(void){
   int a;
    a = 3;
 }
 ```
- Assembly code
 ```C
 mov DWORD PTR [esp+12],3
 ```
 What's differences between ex1 and ex2 ?
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
||Global variable|Local variable|
|--|--------|--------|
|Creation|Program starts|Function is called|
|Life time|Program stops|Function returns|
|Copies|Only one|The number of function called.|
|Address|In the data seg|In the stack|
|Accessible|Everywhere|Within the function|
### Example 3: assignment 3 ###
```C
int main(void){
int a, b;
b = a;
}
```
```C
mov eax, DWORD PTR [esp+12]
mov DWORD PTR [esp+8], eax
```
### Syntax of MOV ###
```
MOV destination, source
```
- In the syntax of MOV, none of them are destination=mem and source=mem
- <mem> can be a direct address or an indirect address
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
int a, b, c;
c = a + b;
 }
 ```
 - Assembly code
 ```C
 mov eax, DWORD PTR [esp+8]
 mov edx, DWORD PTR [esp+12]
 add eax, edx
 mov DWORD PTR [esp+4], eax
 ```
 - eax, edx are registers in x86 CPU
### ADD instruction ###
 - The add instruction adds together its two operands, storing the result in its first operand.
 - Ex: add BYTE PTR [var], 10


add 10 to the single byte stored at memory
address var
