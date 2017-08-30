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
![image](image/7-1.jpg)



