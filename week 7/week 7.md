# week 7
### 內容
Character Input/Output and .Input Validation

## 前言
我們已經學會用 printf()，scanf()，putchar()，getchar() 來做輸入輸出。這些用來處理 I/O 的 functions 並不是 C 語言內建的，而是由 C library 提供，我們接著就來解釋輸入輸出的運作機制  

```c
#include<stdio.h>

int main(void)
{
    int c;
    while ( (c = getchar()) != EOF)
    {
        putchar(c);
    }
    return 0;
}
```
輸入  
```
Learning c language is fun.
```
輸出  
```
Learning c language is fun.
```
這裡會出現一個問題，照理來說，這個 code 理當出現  
```
LLeeaa......
```

每當輸入一個字就輸出一次，但程式執行起來卻是輸入一整行之後（按下 enter ） 才把字元輸出  

講解：首先得了解 c language 如何處理鍵盤輸入，以及所謂的 buffering 機制。由於 getchar() 會透過 buffer 來緩衝輸入的字元，所以要等到換行字元出現，才會把 buffer 裡的東西送到程式做處理。所以我們會看到鍵盤輸入的字元先被直接回應到螢幕上 (系統為了要讓我們知道究竟打了哪些字)，同時這些輸入的字元會被存在 buffer 中，等到使用者打了 Enter 才把 buffer 裡的字元送到程式，然後程式的迴圈就把字元一一處理  

## 輸入與輸出

在 C 裡面，輸入和輸出的裝置被當成檔案來看待，輸入就像在讀檔，輸出就像是在寫入檔案  

- 好處：可以讓程式可以有統一的介面來處理輸入和輸出，不會因為換到不同系統和不同裝置就變得不能執行  

- 在 stdio.h 裡：訂了兩個標準的檔案串流   
    - stdin ： 標準輸入裝置 (通常假設是鍵盤)  
    - stdout： 標準輸出裝置 (通常假設為螢幕)  

    

所以如果我們從 stdin 讀取資料，就等於是從鍵盤讀取使用者輸入的內容，如果把資料寫入 stdout 就等於把資料送到螢幕上  

- 我們學過的：  
    - scanf() 和 getchar() 都是預設從 stdin 讀資料  
    -  printf() 和 putchar() 都預設為寫入 stdout  
    -  在 stdio.h 裡面定了一個常數 EOF (End of File)，用來代表檔案結束  

既然輸入被當作檔案來處理，我們在讀取 stdin 的資料的時候，就可以判斷讀入的字元是否等於 EOF，用這個檔案結束符號來判斷使用者是否已經結束輸入的動作  


### EOF 在 stdio.h 裡面有定義：  
- 通常它的值被定成 -1，可能因作業系統而有不同  
- 另外，配合 EOF 我們把 c 的型別改成 int 。(其實 getchar() 和 putchar() 的回傳值和參數本來就都是 int 型別)  

```c
#include<stdio.h>

int main(void)
{
    int c; // 這就是為什麼 c 是 int 不是 char （雖然用 char 也ok）
    while ( (c = getchar()) != EOF)
    {
        putchar(c);
    }
    return 0;
}
```

### 如何用鍵盤輸入 EOF  
- 不是輸入 E O F 三個字母，也不是直接打 -1  
- windows ： Ctrl + z  
- mac and linux : Ctrl + d  

##  redirection 功能  
1. 我們先把下面的範例 code 存檔在 c 槽並命名為 code.c，然後 compile 並產生執行檔，假設執行檔的名稱叫做 code.exe  

```c
#include<stdio.h>

int main(void)
{
    int c; // 這就是為什麼 c 是 int 不是 char （雖然用 char 也會過）
    while ( (c = getchar()) != EOF)
    {
        putchar(c);
    }
    return 0;
}
```

2. 儲存下面的內容為 test.txt。  
```c
Learning c language is fun.
```

3. 在 command line 模式下試試看下面的指令  
```
C:\> code.exe < test.txt
```

你會發現 test.txt 的內容會被顯示到螢幕上  

- 不論是在 DOS, Unix, Linux 都提供了所謂 redirection 的功能  
- 這項功能可以把作業系統裡一般的檔案 (譬如純文字檔)，導向成為標準輸入裝置 (對我們的程式來說就是 stdin )  
  
- 以上面的例子來說：  

' < ' 看起來就像把 test.txt 餵給 code.exe，所以 code.exe 不再是從鍵盤讀取資料，而是從檔案 test.txt 讀取資料，對程式來說， test.txt 跟鍵盤輸入都被當作 stdin 所以結果一樣  

> 由於 test.txt 本身是純文字檔，它的檔案結束正是靠 EOF 來標記，所以 code.exe 讀了整個檔案直到碰到 EOF 就結束  


- 輸出也可以用相同的方法：  
```
C:\> code.exe > result.txt
```

- 用 > 符號接上我們要輸出的檔案名稱  
- 之後我們再把command line 裡面輸入字元，最後按 ctrl + z(or ctrl + d)  
- 你會發現在c槽裡多了一個 result.txt 的檔案  

- 把兩個指令合起來就變成：
```
C:\> code.exe < test.txt > result.txt
```

學會了 redirection 這項小技巧之後，處理檔案資料就變得很方便。我們可以先把資料編輯好，再用 < 導向送入程式作處理，不需要每次都在線上打字輸入。而程式輸出的資料也可以用 > 導向，送到檔案裡儲存起來。  

### 另一種方法
- 用 freopen 的方法，也會有相同的結果。  
- 詳情請看 week 6 講義  

## 處理 Buffered Input

還記得上星期所學的 getchar 不只會讀取字母與數字，連enter, tab, 空白都會讀取嗎？  

現在看看下面這個例子：  
```c
#include <stdio.h>
int main(void)
{
    int value = 1;
    
    printf("what day is today, please tell me whether i am true or not with 'y' and 'n'\n");
    
    printf("Uh...is your number %d?\n", value);
    
    while (getchar() != 'y')
        printf("Well, then, is it %d?\n", ++value);
    
    printf("I got it\n");
    
    return 0;
}
```

輸出
```
what day is today, please tell me whether i am true or not with 'y' and 'n'
Uh...is your number 1?
```
輸入
```
n
```
輸出
```
Well, then, is it 2?
Well, then, is it 3?
```
輸入
```
n
```
輸出
```
Well, then, is it 4?
Well, then, is it 5?
```
輸入
```
y
```
輸出
```
I got it
```
講解：這個程式有一個bug，他會一次跑兩行出來。原因就在於我們除了輸入 n 以外， 後面還按了一個 enter（\n ） ， 所以他才會多輸出一行  

修改：  
```c
#include <stdio.h>
int main(void)
{
    int value = 1;
    
    printf("what day is today, please tell me whether i am true or not with 'y' and 'n'\n");
    
    printf("Uh...is your number %d?\n", value);
    
    while (getchar() != 'y')
    {
        printf("Well, then, is it %d?\n", ++value);
        while (getchar() != '\n') continue;
    }
    printf("I got it\n");
    
    return 0;
}
```

同理，看下面這個例子：  
題目：Input a certain date, calculate the n^th^ day of the year.  
改編自：[11153 - Day Number of the Year Calculator](http://acm.cs.nthu.edu.tw/problem/11153/)  

```c
#include <stdio.h>
int a[12]={31,29,31,30,31,30,31,31,30,31,30,31};//閏年
int b[12]={31,28,31,30,31,30,31,31,30,31,30,31};//平常
int function (int year, int month, int date)
{
    int day = 0;
    int i;
    if (year % 400 == 0)//閏年
    {
        for (i = 0; i < month - 1; i++)
        {
            day += a[i];
        }
        day += date;
        
    }else if (year % 4 == 0 && year % 100 != 0)//閏年
    {
        for (i = 0; i < month - 1; i++)
        {
            day += a[i];
        }
        day += date;
        
    }else
    {
        for (i = 0; i < month - 1; i++)
        {
            day += b[i];
        }
        day += date;
        
    }
    
    return day;
    
}
int main(void)
{
    int year, month, date;
    printf("press 'a' to start the program or press enter to quit\n");
    while( getchar() != '\n')
    {
        printf("Input a certain date\n");
        
        scanf("%d %d %d",&year, &month, &date);
        
        printf("%d\n", function(year, month, date));
        
        printf("press 'a' to start the program or press enter to quit\n");
    }
    
    printf("Bye\n");
    return 0;
}
```
輸出
```
press 'a' to start the program or press enter to quit
```
輸入
```
a
```
輸出
```
Input a certain date
```
輸入
```
1997 10 8
```
輸出
```
281  
press 'a' to start the program or press enter to quit
Bye
```
講解：你會發現這個題目沒辦法重複輸入，原因就在於 scanf() 只會讀取他想要的東西，而其他東西就會一直留在 buffer 裡面，等到下一個讀取資料時，才會再從buffer 拿出來。  

所以當我們在輸入1997 10 8 時，後面所按的 enter ，會一直留在 buffer 裡面（因為 scanf 只會讀取 %d %d %d ），這時就會留一個 \n 在 buffer 裡面。  

修改：  
```c
#include <stdio.h>
int a[12]={31,29,31,30,31,30,31,31,30,31,30,31};//閏年
int b[12]={31,28,31,30,31,30,31,31,30,31,30,31};//平常
int function (int year, int month, int date)
{
    int day = 0;
    int i;
    if (year % 400 == 0)//閏年
    {
        for (i = 0; i < month - 1; i++)
        {
            day += a[i];
        }
        day += date;
        
    }else if (year % 4 == 0 && year % 100 != 0)//閏年
    {
        for (i = 0; i < month - 1; i++)
        {
            day += a[i];
        }
        day += date;
        
    }else
    {
        for (i = 0; i < month - 1; i++)
        {
            day += b[i];
        }
        day += date;
        
    }
    
    return day;
    
}
int main(void)
{
    int year, month, date;
    printf("press 'a' to start the program or press enter to quit\n");
    while( getchar() != '\n')
    {
        printf("Input a certain date\n");
        
        scanf("%d %d %d",&year, &month, &date);
        while (getchar() != '\n');
        printf("%d\n", function(year, month, date));
        
        printf("press 'a' to start the program or press enter to quit\n");
    }
    
    printf("Bye\n");
    return 0;
}
```


## scanf 的格式化輸入
在寫程式的時候，有時為了更人性化，會有一些特殊的輸入格式，例如在輸入日期時，常常會寫成 年 / 月 / 日，而不會只是 年 月 日，這時，就可以用 scanf 的格式化輸入來解決這個問題  

讓我們修改一下上面那個例子  

```c
#include <stdio.h>
int a[12]={31,29,31,30,31,30,31,31,30,31,30,31};//閏年
int b[12]={31,28,31,30,31,30,31,31,30,31,30,31};//平常
int function (int year, int month, int date)
{
    int day = 0;
    int i;
    if (year % 400 == 0)//閏年
    {
        for (i = 0; i < month - 1; i++)
        {
            day += a[i];
        }
        day += date;
        
    }else if (year % 4 == 0 && year % 100 != 0)//閏年
    {
        for (i = 0; i < month - 1; i++)
        {
            day += a[i];
        }
        day += date;
        
    }else
    {
        for (i = 0; i < month - 1; i++)
        {
            day += b[i];
        }
        day += date;
        
    }
    
    return day;
    
}
int main(void)
{
    int year, month, date;
    printf("press 'a' to start the program or press enter to quit\n");
    while( getchar() != '\n')
    {
        printf("Input a certain date\n");
        
        scanf("%d/%d/%d",&year, &month, &date);
        while (getchar() != '\n');
        printf("%d\n", function(year, month, date));
        
        printf("press 'a' to start the program or press enter to quit\n");
    }
    
    printf("Bye\n");
    return 0;
}
```


## 檢查輸入是否符合規定
在寫程式時，除了要人性化之外，還必須要求使用者遵照一些要求輸入資料，如果格式不符了話，可能會造成程式停擺或是資料外洩。舉個例子來說，在註冊email時，常常會要求人們只能輸入數字跟英文，如果今天有人故意亂輸入，這時，檢察機制就變成很重要了  

舉個例子來說：  
```c
#include <stdio.h>
#include <string.h>
int main(void)
{
    int valid = 0, i = 0;
    printf("enter your google mail (without @gmail.com)\n");
    while (!valid)
    {
        char a[1000];
        scanf("%s",a);
        
        for (i = 0; i < strlen(a); i++)
        {
            if ( (a[i] >= 65 && a[i] <= 90) ||  ( a[i] >= 97 && a[i] <= 122) || ( a[i] >= 48 && a[i] <= 57))
            {
                valid = 1;
            }else
            {
                valid = 0;
                break;
            }
        }
        
        if (valid == 1) break;
        else            printf("this is not a valid email, please enter it again.\n");
        
    }
    printf("correct format.= ')\n");
    return 0;
}
```
輸出
```
enter your google mail (without @gmail.com)
```
輸入
```
littleboy++_ssdfds
```
輸出
```
this is not a valid email, please enter it again.
```
輸入
```
))(_(
```
輸出
```
this is not a valid email, please enter it again.
```
輸入
```
littleboy1008
```
輸出
```
correct format.= ')  
```
---
講解：基本上，這類的檢查是否符合格式的方法可以用一個迴圈一個一個字元檢查。這題運用的技巧是去判斷他的ascii code 是否是 大寫字母、小寫字母、數字  
reference：[wiki - ASCII](https://zh.wikipedia.org/wiki/ASCII)  

## scanf() 做了什麼事

假設我們輸入了 42 然後按下 Enter  
1. 如果程式是用 scanf() 加上 %c 來讀取，它會讀到 '4' 這個字元，然後存入變數裡  

2. 如果我們用 %s，則會讀入兩個字元 '4' 和 '2'，然後把他們存成字串 (結尾多加 '\0')  

3. 如果用的是 %d，則 scanf()也是讀取兩個字元 '4' 和 '2'，並且計算他們所代表的十進位整數值 (計算 4*10 + 2 得到 42)，然後把這個數值用二進位表示法存入型別為 int 的變數中  

4. 如果用 %f，則一樣是把字元轉成數值 42，再用浮點數的儲存方式存入 float 變數中  

## 複習 scanf() 的輸入法  
### %[*][寬度][資料型態]    
- 資料型態是必填欄位 
- 其他欄位可有可無
- ex.scanf("%7s",a);

### 常見輸入形式  


| Column 1 | Column 2      |Column 3               |  
| -------- | --------      | --------              |
| %d       | int           |&n, store the input integer in n
| %ld      | long          |
| %lld     | long long     |
| %u       | unsigned int  |
| %f       | float         |read float
| %lf      | double        |read double
| %Lf      | long double   |read long double
| %c       | char          |read 3 characters %3c
| %s       | char(string)  |read a string until whitespace
| %n       | int           |with %s, to get string length

char a[100]; int len;   
scanf("%s%n", a, &len);   
len will have the string length  


