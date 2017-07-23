# week 12 非指標
### 內容：
insertion sort  
複習 permutation  
指標+陣列+遞迴  
FILE 指標  
Sentence reversal  

## insertion sort
insertion sort reference : [wiki - insertion sort](https://zh.wikipedia.org/wiki/%E6%8F%92%E5%85%A5%E6%8E%92%E5%BA%8F)  
```c
int main ()
{
    int a[10] = {2,23,9,57,19,0,5};
    int len = 7;
    int i = 0, j = 0;
    int temp;
    for ( i = 1; i < 7; i++)
    {
        temp = a[i];
        j = i - 1;
        for (; j >= 0 && arr[j] > temp; j--) 
        {
            a[j + 1] = a[j];
        }
        a[j + 1] = temp;
    }

}
```
insertion sort 圖解 :
![](https://i.imgur.com/jzjChVp.gif)

## 複習 permutation 指標+陣列+遞迴

```c
#include <stdio.h>
#include <string.h>
void show(char *p);
void swap(char *p, char *q);
void permutation(char *p);
int main(void)
{
    char str[10];
    str[0] = '\0';
    scanf("%8s", str+1);
    permutation(str+1);
    return 0;
}
void show(char * p)
{
    if (*p!='\0') {
        show(p-1);
        printf("%c", *p);
    }
}
void swap(char *p, char *q)
{
    char tmp;
    tmp = *p;
    *p = *q;
    *q = tmp;
}
void permutation(char * p)
{
    int i;
    if (p[0] == '\0') {
        show(p-1);
        printf("\n");
    } else {
        for (i=0; i<strlen(p); i++) {
            swap(p, p+i);
            permutation(p+1);
            swap(p, p+i);
        }
    }
}
```

## FILE 指標

請下載檔案夾中的 test.txt，並跟 c 檔放在一起，接著看下面的範例 code
```c
#include <stdio.h>
int a[10000];
int main(void)
{
    FILE *fin, *fout;
    int i, j, tmp;
    int len;

    fin = fopen("test.txt", "r");
    for (i = 0; i < len; i++) 
    {
        fscanf(fin, "%d", &a[i]);
    }

    for ( i = 1; i < 7; i++)
    {
        temp = a[i];
        j = i - 1;
        for (; j >= 0 && arr[j] > temp; j--) 
        {
            a[j + 1] = a[j];
        }
        a[j + 1] = temp;
    }

    fout = fopen("result.txt", "w");
    
    for (i=0; i<num_data; i++) 
    {
        fprintf(fout, "%d ", data[i]);
    }
    fclose (fin);
    fclose (fout);
    return 0;
}
```
FILE reference : [wiki - stdio.h](https://zh.wikipedia.org/wiki/Stdio.h)  
(進去裡面後 ctrl + F 搜尋 file )  
- fopen 的用法 :   
```
fopen("檔案路徑和檔名", "開檔方式")
```
"r" : read ，對檔案進行讀取  
"w" : write，對檔案進行寫入  
> NOTE :   
> 使用相對位置時需注意程式執行檔和 test.txt 是在同一個資料夾下  
> 不然就要用絕對路徑  

- fscanf 的用法 :   
```
fscanf(檔案指標, "讀取格式", 變數的位址)
```
fscanf 得用法跟 scanf 一樣，只是前面要多傳一個指標指向要讀取的資料。讀取完後在用 fclose 把檔案關閉  

## 字串處理
### strcmp 和 strncmp  
```c
int strcmp(const char *s1, const char *s2);
int strncmp(const char *s1, const char *s2, size_t size);
```
reference : [ cplusplus - strcmp](http://www.cplusplus.com/reference/cstring/strcmp/)  
reference : [ cplusplus - strncmp](http://www.cplusplus.com/reference/cstring/stnrcmp/)  

```c
#include <stdio.h>
#include <string.h>
int main(void)
{
    char str1[101];
    char str2[101];
    int n = 3;
    
    scanf("%100s", str1);   // 限制使用者只能輸入 100 個字
    scanf("%100s", str2);
    
    printf("str1 : %s\n", str1);
    printf("str2 : %s\n", str2);
    
    printf("-------------------\n");
    
    printf("strcmp\n");
    printf("return value : %d\n", strcmp(str1, str2));
    
    printf("-------------------\n");
    
    printf("strncmp\n");
    printf("return value : %d\n", strncmp(str1, str2, n));
    
    
    return 0;
}
```
輸入  
```
abcd
abce
```
輸出  
```
str1 : abcd
str2 : abce
-------------------
strcmp
return value : -1
-------------------
strncmp
return value : 0
```
結論：


| 回傳的值   | 意義     | 
| -------- | -------- |  
| < 0      | 表示前面那個字串的 ASCII code 比較小      |  
| = 0      | 表示兩字串相同                          |  
| > 0      | 表示後面那個字串的 ASCII code 比較小      |  


### strcpy 跟 strncpy
```c
char * strcpy ( char * destination, char * source )
char * strncpy ( char * destination, char * source, size_t num );
```
strcpy 跟 strncpy 兩者皆是用來複製字串的，差別在於 strcpy 是整個字串複製過去，而 strncpy 能指定複製幾個字元  
範例 1  
```c
#include <stdio.h>
#include <string.h>
int main(void)
{
    char a[100] = "A apple a day keeps the doctor away.";
    char b[100] = "hello world";
    strcpy(b, a);
    printf("%s\n", b);
    
    return 0;
}
```
輸出
```
A apple a day keeps the doctor away.
```
> Note :   
> 放在前面的字串是要 destination，後面才是原本的字串，在寫程式的時候，記得不要寫錯了  
  
範例 2  
```c
#include <stdio.h>
#include <string.h>
int main(void)
{
    char a[100] = "A apple a day keeps the doctor away.";
    char b[100];
    strncpy(b, a, 1);
    printf("%s\n", b);
    
    return 0;
}
```
輸出  
```
A
```
範例 3   
```c
#include <stdio.h>
#include <string.h>
int main(void)
{
    char a[100] = "A apple a day keeps the doctor away.";
    char b[100] = "hello world";
    strncpy(b, a, 1);
    printf("%s\n", b);
    
    return 0;
}
```
輸出  
```
Aello world
```
由範例二和範例三可以發現，如果 destination 字串原本有東西，那麼再複製時，並不會把後面的東西給刪掉  

### strcat
```c
char * strcat ( char * destination, char * source );
```
strcat 能將一個字串的東西複製並貼在另一個字串的後面，但在使用時要特別注意字串的長度是否充足，否則程式會出錯
```c
#include <stdio.h>
#include <string.h>
int main(void)
{
    char a[20] = "hello ";
    char b[20] = "world.";
    strcat(a, b);
    printf("%s\n", a);
    
    return 0;
}
```
輸出
```
hello world.
```




### memset 跟 memcpy
```c
void * memset ( void * ptr, int value, size_t num )
void * memcpy ( void * destination, const void * source, size_t num );
```


- memeset : Sets the first num bytes of the block of memory pointed by ptr to the specified value (interpreted as an unsigned char).
- memcpy : Copies the values of num bytes from the location pointed to by source directly to the memory block pointed to by destination.

reference : [cplusplus - memeset](http://www.cplusplus.com/reference/cstring/memset/) , [cplusplus - memcpy](http://www.cplusplus.com/reference/cstring/memcpy/)
 ```c
#include <stdio.h>
#include <string.h>
#define SIZE 100
int main(void)
{
int a[SIZE];
int b[SIZE];
int i;

memset(a, 0, SIZE*sizeof(int));
for (i=0; i<SIZE; i++) {
printf("%d ", a[i]);
if ((i+1)%10 == 0) printf("\n");
}

for (i=0; i<SIZE; i++) {
a[i] = i*i;
}

memcpy(b, a, SIZE*sizeof(int));
for (i=0; i<SIZE; i++) {
printf("%5d", b[i]);
if ((i+1)%10 == 0) printf("\n");
}

return 0;
}
 
```

## Sentence reversal

輸入一個英文句子字串
包含多個英文單字
每個英文單字之間用單個空白字元隔開
沒有其他標點符號
例如
this is a book

用程式將上面的句子以單字為單位反轉
輸出
book a is this

讀取輸入的字串可以用下面的方式
 
char a[100];

fgets(a, 99, stdin);

a[strlen(a)-1] = '\0';

不使用 scanf "%s" 讀取字串的原因是因為 scanf 讀到空白字元就會停止
用 fgets 則會讀到換行才停止
由於 fgets 會把換行字元也讀進來
所也要多一個步驟 a[strlen(a)-1] = '\0';
把換行字元去掉
詳細使用說明可參考 http://www.gnu.org/software/libc/manual/html_node/Line-Input.html
char * fgets(char *s, int count, FILE *stream)
Thefgetsfunction reads characters from the streamstreamup to and including a newline character and stores them in the strings, adding a null character to mark the end of the string. You must supplycountcharacters worth of space ins, but the number of characters read is at mostcount− 1. The extra character space is used to hold the null character at the end of the string.

If the system is already at end of file when you callfgets, then the contents of the arraysare unchanged and a null pointer is returned. A null pointer is also returned if a read error occurs. Otherwise, the return value is the pointers.

Warning:If the input data has a null character, you can't tell. So don't usefgetsunless you know the data cannot contain a null. Don't use it to read files edited by the user because, if the user inserts a null character, you should either handle it properly or print a clear error message. We recommend usinggetlineinstead offgets.


 
#include <stdio.h>
#include <string.h>
void flip(char *b, int k, int l)
{
    int tmp;
	for ( ; k<l ; k++, l--) {
		tmp = b[k];
		b[k] = b[l];
		b[l] = tmp;
	}
}

int main(void)
{
	char a[100];
	int i, j;
	fgets(a, 99, stdin);
	a[strlen(a)-1] = ' ';

	//"this is a book \0"

	j = 0;
	for (i=0; a[i]!='\0'; i++) {
		if (a[i] == ' ') {
			flip(a, j, i-1);
			j = i+1;
		}

	}
	flip(a, 0, strlen(a)-2);
	printf("%s\n", a);

	return 0;
}
