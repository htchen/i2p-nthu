# week 12 非指標
### 內容：
insertion sort
複習 permutation
指標+陣列+遞迴
FILE 指標
Sentence reversal

## insertion sort
先從檔案讀取資料，存入陣列 data 中。接著利用 insertion sort 的方法，將資料排序，由小到大存放在 data 陣列中。
把排序完之後的資料輸出到檔案 "data_50000_sorted.txt" ，底下是把資料輸出到檔案的方式:
      freopen("data_50000_sorted.txt", "w", stdout);

     for (i = 0; i < num_data; i++) {
        printf(" %d", data[i]);
     }

insertion sort 的概念很簡單，可自行參考 Wikipedia 的敘述。上課的時候也會再講解一次。。
Wikipedia 提供的 pseudo code 稍做修改
for i ← 1 to length(A)
    j ← i
    while j > 0 and A[j-1] > A[j]
        swap A[j] and A[j-1]
        j ← j - 1
將 swap 展開之後的版本是
 for i = 1 to length(A)
    x = A[i]
    j = i
    while j > 0 and A[j-1] > x
        A[j] = A[j-1]
        j = j - 1
    A[j] = x
請試著把 pseudo code 轉換成 C 程式碼。


## 複習 permutation 指標+陣列+遞迴

```clike=
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
練習如何使用陣列來儲存資料
練習如何開檔、讀檔、寫檔。

[前置作業]
下載附件檔案 data_50000.txt
檔案裏讀到的第一個整數代表後面總共有多少筆資料必須讀取(data_50000.txt 裏的第一個整數是50000，因此後面共五萬筆資料)，資料的格式是整數，有正有負。
讀取到的資料用一個 global 整數陣列來儲存。
global 陣列宣告在 function 之外，不屬於單一 function，每個 function 都可以存取 global 陣列。
需要這麼做的原因是因為 function 中宣告的陣列大小通常不能太大，
由於我們還沒學到動態記憶體管理，
因此目前先暫時用 global 的方式產生陣列，這樣才能夠處裡稍微大量一點的資料。

例如下面的程式碼，產生一個叫作 data 的整數陣列，用來儲存從檔案讀取到的資料:
#include <stdio.h>
int data[100000];
int main(void)
{
    FILE *fin, *fout;
    int i, j, tmp;
    int num_data;

    fin = fopen("data_50000.txt", "r");
    fscanf(fin, "%d", &num_data);
    for (i=0; i<num_data; i++) {
        fscanf(fin, "%d", &data[i]);
    }

    for (i=1; i<num_data; i++) {
        j = i;
        tmp = data[j];
        while (j>0 && data[j-1]>tmp) {
            data[j] = data[j-1];
            j--;
        }
        data[j] = tmp;
    }



    fout = fopen("data_50000_sorted.txt", "w");
    fprintf(fout, "%d\n", num_data);
    for (i=0; i<num_data; i++) {
        fprintf(fout, "%d ", data[i]);
    }

    return 0;
}

這段程式碼會產生一個 FILE 指標 (變數名稱是 fin)，
接著用 fopen() 把檔案開啟並取得資料的起始位置，然後用 fscanf() 讀取資料。
fopen() 的用法是 fopen("檔案路徑和檔名", "開檔方式"); 若對照上面那段程式碼，其中我們用到的 "r"， 代表 read， 也就是只做讀取， 如果開檔成功會傳回指向檔案內容起始位置的指標。假如沒有指定路徑而只有給檔名，則要確定data_50000.txt 和你的程式執行檔是在同一個資料夾下，才會順利找到檔案並開啟。

fscanf()的用法是 fscanf(檔案指標, "讀取格式", 變數的位址); 和 scanf() 的用法完全一樣，只是前面要多傳一個指標，指向要讀取的資料。檔案讀取完畢要用 fclose() 把檔案關閉。

關於開檔、讀檔、寫檔的方式，雖然上機考試用不到，但是一般寫程式經常會用到。

用迴圈讀取資料的寫法，可以參考講義 [範例 E10_03.c]，把其中的 scanf(...) 換成 fscanf(fin, ...)。
除錯階段可以先拿小一點的檔案來測試。

## 字串處理

Reference:
http://www.gnu.org/software/libc/manual/html_node/String-and-Array-Utilities.html#String-and-Array-Utilities

使用 strcmp 和 strncmp
intstrcmp(const char *s1, const char *s2);
int strncmp(const char *s1, const char *s2, size_t size);

#include <stdio.h>
#include <string.h>

int main(void)
{
char str1[30];
char str2[30];
int n;

scanf("%29s", str1);
scanf("%29s", str2);

printf("strcmp(%s, %s) = %d\n", str1, str2, strcmp(str1, str2));
n = 3;
printf("strncmp(%s, %s, n) = %d\n", str1, str2, strncmp(str1, str2, n));

return 0;
}

輸入:
 
abcd
abce

輸出：
 
strcmp(abcd, abce) = -1
strncmp(abcd, abce, n) = 0


使用 strcpy, strcat, strncpy
char * strcpy(char *restrict to, const char *restrict from);
char * strncpy(char *restrict to, const char *restrict from, size_t size);
char * strcat(char *restrict to, const char *restrict from);

 
#include <stdio.h>
#include <string.h>

int main(void)
{
char str1[30];
char str2[30];
char str3[60];


scanf("%29s", str1);
scanf("%29s", str2);

strcpy(str3, str1);
strcat(str3, str2);
printf("%s\n", str3);

return 0;
}


 
#include <stdio.h>
#include <string.h>

int main(void)
{
char str1[30];
char str2[30];
int n;

scanf("%s", str1);

n = 2;
strncpy(str2, str1, n);
str2[n] = '\0';
printf("%s\n", str2);

return 0;
}


使用 memset, memcpy
void *memset(void *block, int c, size_t size);
void * memcpy(void *restrict to, const void *restrict from, size_t size);

 
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
