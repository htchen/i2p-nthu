# week 12 非指標
### 內容：
insertion sort  
複習 permutation  
指標+陣列+遞迴  
FILE 指標  
Sentence reversal  

## insertion sort
insertion sort reference : [wiki - insertion sort](https://zh.wikipedia.org/wiki/%E6%8F%92%E5%85%A5%E6%8E%92%E5%BA%8F)  

insertion sort 圖解 :  
![](https://i.imgur.com/jzjChVp.gif)  
insertion sort 原理 :  

首先將資料分成 **已排序**、**未排序** 兩部份
依序由 **未排序** 中的第一筆資料，插入到 **已排序** 中的適當位置
插入時由右而左比較，直到遇到第一個比當下的值還小的值
再插入比較時，若遇到的值比當下的值大或相等，則將值往右移

```c
#include <stdio.h>

int main ()
{
    int a[10] = {2,23,9,57,19,0,5};
    int len = 7;
    int i = 0, j = 0;
    int temp;
    for ( i = 1; i < len; i++)
    {
        temp = a[i];
        j = i - 1;
        for (; j >= 0 && a[j] > temp; j--) 
        {
            a[j + 1] = a[j];
        }
        a[j + 1] = temp;
    }
    for ( i = 0; i < len; i++)
    {
        printf("%d", a[i]);
        if( i < len-1)
            printf(" ");
    }
    printf("\n");
    return 0;
}
```
輸出  
```
0 2 5 9 19 23 57
```

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
void show(char* p)
{
    if (*p!='\0')
    {
        show(p-1);
        printf("%c", *p);
    }
}
void swap(char* p, char* q)
{
    char tmp;
    tmp = *p;
    *p = *q;
    *q = tmp;
}
void permutation(char* p)
{
    int i;
    if (p[0] == '\0') 
    {
        show(p-1);
        printf("\n");
    } else 
    {
        for (i = 0; i < strlen(p); i++)
        {
            swap(p, p+i);
            permutation(p+1);
            swap(p, p+i);
        }
    }
}
```

輸入  
```
1234
```
輸出  
```
1234
1243
1324
1342
1432
1423
2134
2143
2314
2341
2431
2413
3214
3241
3124
3142
3412
3421
4231
4213
4321
4312
4132
4123
```

## FILE 指標
FILE reference : [wiki - stdio.h](https://zh.wikipedia.org/wiki/Stdio.h)  
(進去裡面後 ctrl + F 搜尋 file )  
  
請下載檔案夾中的 test.txt，並跟 c 檔放在一起，接著看下面這個範例  
  
題目：test.txt 裡面第一個數字代表總共有幾筆測資，接著請將這些測資排序後，輸出至 result.txt 裡面  
```c 
#include <stdio.h>

int a[10000];

int main(void)
{
    FILE *fin, *fout;
    int i, j, tmp;
    int len;
    
    fin = fopen("test.txt", "r");
    
    fscanf(fin, "%d", &len);    //讀取總共有幾筆測資
    
    for (i = 0; i < len; i++)    //讀取資料
    {
        fscanf(fin, "%d", &a[i]);
    }
    
    for ( i = 1; i < len; i++)    //排序
    {
        tmp = a[i];
        j = i - 1;
        for (; j >= 0 && a[j] > tmp; j--)
        {
            a[j + 1] = a[j];
        }
        a[j + 1] = tmp;
    }
    
    fout = fopen("result.txt", "w");    //設定輸出的目標檔案
    
    for (i = 0; i < len; i++)
    {
        fprintf(fout, "%d ", a[i]);
    }
    
    fclose (fin);
    fclose (fout);
    //用 fopen 打開的檔案必須在結束後，用 fclose 將檔案關閉
    return 0;
}
```

- fopen 的語法   
```c
fopen("檔案路徑和檔名", "開檔方式")
```
"r" : read ，對檔案進行讀取  
"w" : write，對檔案進行寫入  
> NOTE :   
> 使用相對位置時需注意程式執行檔和 test.txt 是在同一個資料夾下  
> 不然就要用絕對路徑  

- fscanf 的用法 :   
```c
fscanf(檔案指標, "讀取格式", 變數的位址)
```
fscanf 的用法跟 scanf 一樣，只是前面要多傳一個指標指向要讀取的資料。讀取完後在用 fclose 把檔案關閉  

## 字串處理
### strcmp 和 strncmp  
```c
int strcmp(const char * s1, const char * s2);
int strncmp(const char  * s1, const char * s2, size_t size);
```  
strcmp 和 strncmp 都能拿來將字串做比較，差別在於 strcmp 是拿來比較整個字串，而 strncmp 則是能自行決定要比較幾個字元，最後根據結果回傳值 

| 回傳的值 | 意義
| -------- | --------  
| < 0  | 表示前面那個字串的 ASCII code 比較小   
| = 0  | 表示兩字串相同   
| > 0  | 表示後面那個字串的 ASCII code 比較小   

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
> 放在前面的是 **目標指標**，後面才是原本的字串，在寫程式的時候，記得不要寫錯了  
  
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
由範例二和範例三可以發現，如果 目標指標的字串原本有東西，那麼再複製時，並不會把後面的東西給刪掉  

### strcat
```c
char * strcat ( char * destination, char * source );
```
strcat 能將一個字串的東西複製並貼在另一個字串的後面，但在使用時要特別注意字串的大小是否夠大，否則程式會出錯
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
- memset 和 memcpy 的語法  
```c
void * memset ( void * ptr, int value, size_t num )
void * memcpy ( void * destination, void * source, size_t num );
```
- 英文定義 :
    - memeset : Sets the first num bytes of the block of memory pointed by ptr to the specified value (interpreted as an unsigned char)
    - memcpy : Copies the values of num bytes from the location pointed to by source directly to the memory block pointed to by destination  

reference : [cplusplus - memeset](http://www.cplusplus.com/reference/cstring/memset/) , [cplusplus - memcpy](http://www.cplusplus.com/reference/cstring/memcpy/)   
- memset 範例  
```c
#include <stdio.h>
#include <string.h>
#define SIZE 100
int main(void)
{
   int a[SIZE];
   int i;
   
   memset(a, 0, SIZE * sizeof(int));
   
   for (i = 0; i < SIZE; i++)
   {
       printf("%d ", a[i]);
       if ((i + 1) % 10 == 0) printf("\n");
   }
}
```
輸出  
```
0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 
```
memset 通常拿來將一個陣列初始化，只要搭配上 SIZE * sizeof( 型別 ) 就能輕鬆將整個陣列，以這個範例來說他就將整個 a 陣列初始化為 0 了，省去當初要寫一個 for loop 的時間  

- memcpy 範例

 ```c
#include <stdio.h>
#include <string.h>
#define SIZE 100
int main(void)
{
    int b[SIZE];
    int i;

    for (i = 0; i < SIZE; i++)
    {
        a[i] = i * i;
    }
    
    memcpy(b, a, SIZE * sizeof(int));
    
    for (i = 0; i < SIZE; i++)
    {
        printf("%5d", b[i]);
        if ((i + 1) % 10 == 0) printf("\n");
    }
    
    return 0;
}

```
輸出
```
    0    1    4    9   16   25   36   49   64   81
  100  121  144  169  196  225  256  289  324  361
  400  441  484  529  576  625  676  729  784  841
  900  961 1024 1089 1156 1225 1296 1369 1444 1521
 1600 1681 1764 1849 1936 2025 2116 2209 2304 2401
 2500 2601 2704 2809 2916 3025 3136 3249 3364 3481
 3600 3721 3844 3969 4096 4225 4356 4489 4624 4761
 4900 5041 5184 5329 5476 5625 5776 5929 6084 6241
 6400 6561 6724 6889 7056 7225 7396 7569 7744 7921
 8100 8281 8464 8649 8836 9025 9216 9409 9604 9801
 ```
 memcpy 用來做內存複製，你可以拿它複製任何數據類型的對象，可以指定複製的數據長度，範例中 memcpy 將平方處理後的 a 字串複製到 b 字串中  
 
> Note : 
> memcpy 與 strcpy 的差別在於 strcpy 就只能複製字符串了，它遇到 '\0' 就結束複製  
## Sentence reversal  
題目：輸入一個英文句子字串然後將它反轉。（其字串包含多個英文單字，每個英文單字用單個空白字元隔開，標點符號只有句點）  
例如：
輸入
```
This is a book.
```
輸出
```
book. a is This
```

### step 1 - 輸入
這裡不採用 scanf "%s" 讀取字串的原因是因為 scanf 讀到 **空白字元** 就會停止  
而 fgets 則不受到空白字元影響   
  
- fgets 的語法
```c
char * fgets ( char * str, int num, FILE * stream );
```
- str : 目標字串
- num : 決定讀取幾個字元
- stream : 來源，如果是用鍵盤輸入了話就用 stdin 即可 

> Note 1 :
> fgets 會讀取資料直到讀到下面三種字元才會停止  
> 1. '\0'  
> 2. newline character ( 換行 )  
> 3. EOF ( end of file )  
  
> Note 2 : 
> fgets 雖然讀到 **換行** 就換停止，但他也會把換行讀進來，所以在用鍵盤輸入時，記得在程式碼裡面要把 **換行** 拿掉

> Note 3：
> fgets 會自動在結尾補 '\0'，所以不用自己補

fgets reference : [reference - fgets](http://www.cplusplus.com/reference/cstdio/fgets/)  

所以輸入的程式碼應該長成這樣：
```c 
#include <stdio.h>
#include <string.h>
int main()
{
    char a[100];
    int i, j;
    fgets(a, 99, stdin);
}
```  

### Point 2 - 反轉字串
在這裡我們先不討論怎麼定義一個單字，單純只討論怎麼反轉單字
```c
void flip(char* b, int k, int l)
{
    int tmp;
    for ( ; k < l ; k++, l--)
    {
        tmp = b[k];
        b[k] = b[l];
        b[l] = tmp;
    }
    // k++ ， l-- 的用意：每換一組字母，
    // 就讓開頭位置 + 1 ，而結尾位置 - 1
    // 這樣當 k = l (開頭 = 結尾) 時就表示整個單字都反轉完了
}
```
- 傳進來的參數
```c
1. char* b : 字串的開頭指標（ 記憶體位置 ）
2. int k : 單字的開頭
3. int l : 單字的結尾
```
> Note : 
> 這裡不將整個字串傳入的原因在於，傳 array 所需要的資源過大，而傳指標了話，所需要的資源較少，且能達到相同的效果  
### step 3 - 判斷什麼時候要反轉
在這裡我們採用的方式是
1. 先將每個單字都反轉一次  
2. 最後再將整個字串反轉  

這樣就能達到題目所要求的效果了  
  
首先，我們要做的是定義什麼樣叫做一個單字，這裡採用的定義為 ==兩個空格間所夾的字母為一個單字==，因此為了讓最後一串字母被定義為單字，我們在整個字串的後面加一個空白字元
>Note : 
>剛剛有提到，我們今天輸入是用 fgets ，所以在用鍵盤輸入時， enter (newline character ) 也會被讀進資料中，所以這時，我們只要把字串中的換行字元改成空白字元就可以了

所以程式應該長成
```c
#include <stdio.h>
#include <string.h>
int main()
{
    char a[100];
    int i, j;
    fgets(a, 99, stdin);
    a[strlen(a)-1] = ' ';
}
```

接著將所整個字串用 for loop 掃過一遍，把所有的單字都反轉一次
```c
int main(void)
{
    char a[100];
    int i, j;
    fgets(a, 99, stdin);
    a[strlen(a)-1] = ' ';
    
    j = 0;
    for (i = 0; a[i] != '\0'; i++)
    {
        if (a[i] == ' ')
        {
            flip(a, j, i-1);
            j = i+1;
        }
        
    }
}
```
最後再將整格字串 ( 最後面我們多加的空格除外 )反轉就完成了，最後附上完整程式
```c
#include <stdio.h>
#include <string.h>
void flip(char* b, int k, int l)
{
    int tmp;
    for ( ; k < l ; k++, l--)
    {
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
    
    j = 0;
    for (i = 0; a[i] != '\0'; i++)
    {
        if (a[i] == ' ')
        {
            flip(a, j, i-1);
            j = i+1;
        }
        
    }
    flip(a, 0, strlen(a)-2);
    printf("%s\n", a);
    
    return 0;
}
```
輸入
```
This is a book.
```
輸出
```
book. a is This
```
