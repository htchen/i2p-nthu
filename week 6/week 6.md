# week 6
### 內容： freopen, EOF,if else, getchar(), putchar(), switch case, #ifndef




## 讀取檔案的方法
```c
    freopen("testcase.txt", "r", stdin);
    freopen("output.txt", "w", stdout);
```

程式碼測試階段，可以在 main 一開始的地方加入這兩行程式碼，這樣每次執行時就不必手動重複輸入測試資料。

- testcase.txt 是你自己編輯的測試資料文字檔
- output.txt 則會由程式自動產生，檔案裡面會是程式執行的結果
- "r" 代表 read
- "w" 代表 write
- 基本上在windows系統裡，寫這樣就可以了，他會自動對應到相對位置（通常是跟你的.c檔放在一起，如果發現程式執行後沒有檔案輸出，這時就必須用絕對位置，例如：/Users/kronos/Desktop/testcase.txt，這樣才能讀到。

- 這個方法只能在自已電腦測試時使用，不能送入ＯＪ，因為ＯＪ會擋住這個library。





## ＥＯＦ
- 檔案的結尾一定會有 EOF 字元，因此可以用 EOF 判斷是否已經將檔案讀完
- 如果是用手動方式輸入，則要在結束時按下 ctrl + z (windows)或是ctrl + d (mac)
- 舉例
```clike=
#include <stdio.h>

int main(void)
{
    int a;
    while ( scanf("%d", &a) != EOF)
    {
        printf("%d\n", a);
    }
}
```
INPUT:
3
4
5
6
(ctrl + d or ctrl + z)
＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿
OUTPUT:
3
4
5
6



## if else
### 模板：
```clike=
    if (<#condition#>)
    {
        <#statements#>
        
    }else if (<#expression#>)
    {
        <#statements#>
        
    }else
    {
        <#statements#>
    }
```

- <#condition#> ： 條件，如果 <#condition#> 不為 0 （在c語言裡面，不為0 即代表true的意思）， 就會執行<#statements#>裡面的事。

- <#statements#>： 要執行的事

### 要注意的是如果已經符合第一個if（ ）的條件， 他就不會去判斷下一個else if 的條件了
- 舉個例子來說：
```clike=
#include<stdio.h>
int main(void)
{
    int a = 10;
    
    if (a > 5)
    {
        printf("a > 5\n");
        
    }else if ( a > 7)
    {
        printf("a > 7\n");
        
    }else
    {
        printf("a < 5\n");
    }
    return 0;
}
```
結果：
a > 5

你會發現a 明明大於7，但是由於他已經符合第一個條件了，所以即便a符合第二個條件，他也不會執行裡面的敘述。

改寫：
```clike=
#include<stdio.h>
int main(void)
{
    int a = 10;
    
    if (a > 5)
        printf("a > 5\n");
        
    if ( a > 7)
        printf("a > 7\n");
        
    return 0;
}
```
結果：
a > 5
a > 7

＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿

### 什麼時候需要加大括號？
- 注意範例一跟範例二，會發現一個if else 有大括號，一個沒有
- 大括號的使用條件：
    - 如果今天你的statement（要執行的事）只有一行，那麼就可以省略模板裡面的大括號

### 一定要加else嗎？

- 不一定，如果沒有其他條件了話，就可以不用加else

### 小括號裡面的condition 可以放什麼？
1. expression ： > , < , >= , <= , || , && , == , !=
```clike=
#include<stdio.h>
int main(void)
{
    int m = 10;
    int d = 0;
    
    if ( m == d) printf("m == d\n");
    else         printf("m is not equal to d\n");
    // 因為 m != d 所以結果不是 0
    
    if ( m || d) printf("m != 0 or d != 0\n");
    else         printf("m and d are 0\n");
    // 由於 m != 0 所以 m || d 的結果不是 0
    
    if ( m && d) printf("m and d are not 0\n");
    else         printf("one of them is 0\n");
    // 由於 d = 0 所以 m && d 的結果是 0
    
    return 0;
}
```
結果：
m is not equal to d
m != 0 or d != 0
one of them is 0

進階用法：
```clike=
#include<stdio.h>
int main(void)
{
    int m = 5;
    int n = 7;
    
    if ( m == 5 && n == 7)
    {
        printf("m is 5 and n is 7\n");
    }
    
    return 0;
}

```
結果：
m is 5 and n is 7

＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿

2. funciotn 的回傳值

問題： 從1 ~ 100 找出 7 的倍數

```clike=
#include<stdio.h>
int find( int n)
{
    if ( n < 7 ) return 0;      // return false
    if ( n % 7 == 0) return n;  // 只要回傳的值不是0 ，那就是 return true
    
    return 0;                   // return false
}
int main(void)
{
    
    int i;
    for (i = 0; i <= 100; i++)
    {
        if ( find(i) ) printf("%d\n", i);
        
    }
    return 0;
}
```
結果
7
14
21
28
35
42
49
56
63
70
77
84
91
98
＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿

3. ctype.h

ctype.h 是一個很好用的library， 有興趣的人可以去下面的網址看看。
reference: http://www.cplusplus.com/reference/cctype/

問題：
Given a string, output the number of uppercase letters,lowercase letters and others.（判斷一個字串裡面有幾個大寫字母，小寫字母，跟其他的字元。
For example, "AEbW%9Q" consist of 4 uppercase letters, 1 lowercase letter and 2 other letters.

原題目：http://acm.cs.nthu.edu.tw/problem/11162/

```clike=
#include <stdio.h>
#include <ctype.h>
#include <string.h>
int main(void)
{
    char a[1000];
    scanf("%s",a);
    int i,up=0,low=0;
    for (i = 0; i < strlen(a); i++)
    {
        if (isupper(a[i]) ) up++;
        if (islower(a[i]) ) low++;
    }
    printf("%d %d %lu\n",up,low, (strlen(a)-up-low) );
    return 0;
}
```
講解：
- isupper是用來判斷輸入進去的字母是不是大寫，如果是大寫就return true，不是就return false。
- islower用來判斷是不是小寫。

ctype.h裡還有 isspace,isalnum等等，大家可以去試試看。
- isalnum() 
- isalpha()
- isdigit()
- tolower()
- toupper()
＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿


4. 等等


### ? : 的語法
-先看範例code：
```clike=
a = ( b > 0) ? 1 : -1;
```
講解：這是if else 簡單寫法，翻成中文是： 如果 b > 0 了話， a = 1， 如果不是了話， a = -1。

所以換成我們常看的if else 就會變成：
```clike=
if ( b > 0) a =  1;
else        a = -1;
```

進階版：
```clike=
a = ( b > 0) ? 1 : ( c > 0) ? 2 : 0;
```
意義相當於：
```clike=
if ( b > 0)
{
    a = 1;
    
}else if ( c > 0)
{
    a = 2;
    
}else
{
    a = 0;
}
```
不過，通常不會寫得這麼文言，用if else 表示比較容易看懂，也比較好debug。




## if statement 搭配 loop

在寫迴圈時，常常會搭配if else 來使用，除了可以控制迴圈什麼時候停止，也能控制迴圈要不要執行，先看看下面這個例子

```clike=
#include<stdio.h>
int main(void)
{
    int n = 0;
    while (++n)
    {
        if      ( n == 3)   continue;
        else if ( n == 9)   break;
        
        printf("%d\n", n);

    }
    return 0;
}
```
結果：
1
2
4
5
6
7
8


仔細對照範例code跟結果，你會發現 當n = 3 的時候，不會執行print的動作，原因就在於，continue 會直接跳過這次迴圈要執行的事，直接去下一個cycle。然後當 n = 9 的時候， 迴圈就強制結束的原因在於break，break 這個指令會直接跳出迴圈，不再執行任何事。

- continue ： 直接跳過這次迴圈要執行的事，直接去下一個cycle。
- break： 強制停止迴圈



## getchar() and putchar()

寫程式時總會有需要一次只讀取或是輸出一個字元的時候，那這個時候就不能用scanf() 或是 printf()，而要用 getchar() 和 putchar()

先看看下面這個例子：
```clike=
#include<stdio.h>

int main(void)
{
    char c;
    
    while ( (c = getchar() ) != EOF )
    {
        printf("c = ");
        putchar(c);
        printf("\n");
    }
    return 0;
}
```
INPUT
hello world.
＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿
OUTPUT
c = h
c = e
c = l
c = l
c = o
c = 
c = w
c = o
c = r
c = l
c = d
c = .
c = 
＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿
講解： getchar( )不只會讀取字母和數字而已，連空白、tab鍵、enter等等都會讀取進來，所以在句點輸出完了後，會多輸出一個" c = "。

解決的辦法：
```clike=
#include<stdio.h>

int main(void)
{
    char c;
    
    while ( (c = getchar() ) != EOF )
    {
        if ( c == '\n') continue;
        printf("c = ");
        putchar(c);
        printf("\n");
    }
    return 0;
}
```


在寫OJ的題目時可以想想什麼時候可以用getchar() 跟 putchar()，再搭配ctype.h，這樣很多題目都可以變得很簡單。



### 舉例

舉例：輸入一字串，然後1. 把空白字元拿掉 2. 把大寫換成小寫，小寫化成大寫 3. 數字了話，把原數字加2後輸出。


ps.所謂的空白字元包含1. 空白鍵 2. tab鍵 3. 換行字元（enter鍵）
reference: http://www.cplusplus.com/reference/cctype/isspace/

```clike=
#include<stdio.h>
#include <ctype.h>

int main(void)
{
    char c;
    int n;
    while ( (c = getchar() ) != EOF )
    {
        if ( isspace(c)) continue;
        if ( isupper(c)) printf("%c\n", tolower(c) );
        if ( islower(c)) printf("%c\n", toupper(c) );
        if ( isdigit(c))
        {
            ungetc(c, stdin);
            scanf("%d", &n);
            printf("%d\n", n+2);
        }
    }
    return 0;
}
```
INPUT
abcd 5 33 ABw 98

＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿
OUTPUT
A
B
C
D
7
35
a
b
W
100
＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿
講解：這題不用scanf 讀取字串的方式讀進來的原因在於，用scanf("%s",a)， 了話， 當他遇到white space時，就會停止讀取了，所以這題必須用getchar()，一個字元一個字元慢慢讀進來。




## switch case
### 模板
```clike=
    switch (<#expression#>)
    {
        case <#constant#>:
            <#statements#>
            break;
            
        case <#constant#>:
            <#statements#>
            break;
            
        default:
            break;
    }
```
- <#expression#> ： 要判斷的東西
- <#constant#>    ： 值
- <#statements#>： 要做的事

### 範例code
```clike=
#include<stdio.h>

int main(void)
{
    int a = 99;
    switch (a)
    {
        case 1:
            printf("a is 1\n");
            break;
            
        case 99:
            printf("a is 99\n");
            break;
        .
        .
        .
        .
        .
        .// 可以有很多個case
        .
        .
        
            
        default:
            break;
    }
    return 0;
}
```
結果：
a is 99
＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿
講解：switch case 的用法跟 if else 很像， 概念也差不多，而 default 相當等於 if else 裡面的 else，不同的地方在於， case 裡的 statement 不需要用大括號把它匡著，但是必須在後面加上 break， 用意在於， 只要進入這個case 裡面， 他就不能再進入其他的case裡了。

- 如果沒加了話：
 他就會每一個case都跑進去檢查一次，這樣速度會以較慢，也容易出錯，所以養成好習慣在寫case時都加入一個break結尾。







## ifndef and endif
- 舉例：
```clike=
#ifndef ONLINE_JUDGE
freopen("jewels.txt", "r", stdin);
#endif
```
和 #include 和 #define 一樣
都屬於 preprocessor directives
在程式碼被編譯之前會先做的前處理
#ifndef ONLINE_JUDGE 的作用是根據 ONLINE_JUDGE 是否被定義
決定是否要呼叫 freopen("jewels.txt", "r", stdin);
如果沒有被定義就會呼叫
反之
如果已經定義則不呼叫
(#ifndef 的字面意思可以想成是 if not defined)

這個技巧對於做 online judge 題目很有用
我們可以先將標準鍵盤輸入 stdin 導向為文字檔
文字檔裡面先填好要測試的資料
這樣就不用每次都重複輸入
等到真的要上傳的時候
就把程式碼最開頭的  /* #define ONLINE_JUDGE */
註解去掉變成
#define ONLINE_JUDGE
這樣由於 ONLINE_JUDGE 被定義了
所以 ifndef 條件不成立
不會呼叫 freopen()
標準輸入 stdin 的來源就會交給 Online Judge 系統決定

