# Operators, Expressions, and Statements

## 前言

我們已經大致知道如何輸入資料給程式，以及如何透過程式輸出資料到螢幕上。具備了與使用者互動的基本能力之後，再來就可以開始寫更複雜的程式。我們接下來要介紹一些**運算符號(operator)**，並介紹如何在程式裡加入**迴圈(loop)**，讓程式可以反覆做類似的運算，畢竟這才是電腦的強項。我們就來看看如何利用電腦重複地做大量的計算。

我們第一個要學的迴圈叫做`while`迴圈。

## while loop
**範例 code：**

```C
#include <stdio.h>
#define STEP 10
#define LOWER 0
#define UPPER 100
int main(void)
{
    int fahr, celsius;

    printf("Celsius    Fahrenheit\n");
    printf("---------------------\n");
    celsius = LOWER;
    while (celsius <= UPPER) 
    {
       fahr = 1.8*celsius + 32;
       printf("%4d %12d\n", celsius, fahr);
       celsius = celsius + STEP;
    }
    printf("---------------------\n");
    return 0;
}
```

**執行結果：**
```
Celsius    Fahrenheit
---------------------
   0           32
  10           50
  20           68
  30           86
  40          104
  50          122
  60          140
  70          158
  80          176
  90          194
 100          212
---------------------
```

這個程式會列出攝氏和華氏溫度的對照表。首先我們用了`#define`來訂出我們溫度範圍的上界`UPPER`與下界`LOWER`，除此之外我們還訂出了這個對照表的溫度間隔`STEP`。使用`#define`之後，程式裡所有的變數`UPPER`、`LOWER`、`STEP`都會被設定成我們想要的數值。

接下來我們將開始介紹所謂的`while`迴圈。當程式執行到`while`那一行時，會先檢查小括號`()`裡的式子：
```
(celsius <= UPPER)
```
這個式子是在判斷`celsius`是否小於或等於`UPPER`。如果`celsius`與`UPPER`的大小關係符合這個式子，這個式子就會得到 true，表示這個式子是成立的，因此程式就可繼續執行`while`迴圈裡面的敘述。而整個`while`迴圈需要執行的內容，是被定義在大括號`{}`所包起來的範圍內。所以接下來要執行的程式碼就是：
```C
{
    fahr = 1.8*celsius + 32;
    printf("%4d %12d\n", celsius, fahr);
    celsius = celsius + STEP;
}
```


第一行我們將`celsius`代入換算公式，算出華氏溫度`fahr`。第二行再用`printf()`把對照的華氏溫度印出來。最後一行則是將`celsius`的值累加上`STEP`，然後再將得到的值存回`celsius`。所以跑過第一次迴圈之後，`celsius`的值將從 0 變成 10。

在程式做完大括號`{}`裡面定義的事情之後，整個程式會回到一開始小括號`()` 的式子，再次判斷`celsius <= UPPER`是否成立。如果式子依然成立，`while`迴圈就會再次執行大括號`{}`裡面定義的事情。如此一直反覆進行下去，直到小括號`()`裡面的式子不再被滿足，`while`迴圈就此結束。在範例 code 中，`celsius`的值每次都會加 10 ，一直不停地累加上去。當`celsius`的值大於我們設定的上界`UPPER`，程式就會跳出大括號`{}`之外，繼續執行之後的程式碼。

## Operator "="
基本的運算符號如`+`、`-`、`*`、`/`相信大家都已不陌生，它們分別代表的是加、減、乘、除的四則運算符號。

在這部分，首先我們來介紹`=`這個運算符號。`=`是一個把**值設定給變數**的運算符號 (Assignment Operator)，下面介紹第一種簡單的用法：
```
count = 1000;
```
這個式子代表直接將`count`這個變數的值設為 1000。而另外一種常見的用法如下：
```
count = count + 1;
```
這個用法則代表：我們先將右邊算式的值給算出來，最後再存到左邊的變數裡面。範例中，如果`count`的初始值是 1000 的話，我們計算右邊算式的值會得到 1001 ，最後我們再將得到的值存`count`這個變數裡面。

示意圖如下：

![](https://i.imgur.com/t7Pr0l5.png)

## Operator "%"
除了常見的四則運算符號之外，C 語言裡面還內建了一個好用的運算符號`%`。它的功用是拿來求餘數。例如 24%5 得到的數字就是 4 。下面我們利用`while`迴圈以及`%`求餘數，讓使用者輸入一個正整數之後，輸出該正整數的十進位分解。

**範例 code：**
```C
#include <stdio.h>
int main(void)
{
    int num, p;
    scanf("%d", &num);
    printf("The number %d can be written as\n", num);
    p = 1;
    
    while(num>=10) 
    {
        printf("%d*%d + ", num%10, p);
        num = num / 10;
        p = p * 10;
    }
    printf("%d*%d", num%10, p);
    return 0;
}
```
**輸入：**
```
13579
```
**執行結果：**
```
The number 13579 can be written as 
9*1 + 7*10 + 5*100 + 3*1000 + 1*10000
```

## Operator "++" and operator "\-\-" 
接下來我們要介紹遞增`++`與遞減`--`的運算符號。假設現在我們有一個變數`count`：

- `count++`，就是把`count`的值增加一，相當於`count = count + 1`。
- `count--`，就是把`count`的值減去一，相當於`count = count - 1`。

讓我們來看個實際的例子：

**範例 code：**
```C
#include <stdio.h>
int main(void)
{
    int n = 0;

    while(n < 5) 
    {
        n++;
        printf("ascending  : %d \n", n);    
    }
    printf("---------------\n");
    while(n > 0) 
    {
    	printf("descending : %d \n", n); 
        n--;
    }
    return 0;
}
```
**執行結果：**
```
ascending  : 1 
ascending  : 2 
ascending  : 3 
ascending  : 4 
ascending  : 5 
---------------
descending : 5 
descending : 4 
descending : 3 
descending : 2 
descending : 1 
```

##  \+\+a 與 a\+\+ 的差別？

遞增與遞減的運算符號，除了可以放在變數後面(例如：`count++`)，也可以放在變數的前面(例如：`++count`)。不過放在變數前面與後面的意義稍有不同，請看以下範例：

**範例 code：**
```C
#include <stdio.h>
int main(void)
{
   int a = 1, b = 1;
   int aplus, plusb;
   aplus = a++;
   plusb = ++b;
   printf("a   aplus   b   plusb \n");
   printf("%1d%6d%6d%6d\n", a, aplus, b, plusb);
   return 0;
}
```
**執行結果：**
```
a   aplus   b   plusb
2     1     2     2
```

以上兩種寫法都達到了把`a`和`b`的值增加一的效果。但是從`aplus`和`plusb`的值可以看出來，`a++`和`++b`的時間點稍有不同。

- 如果`++`寫在後面，相當於先做`aplus = a`；然後再做`a = a + 1`。
- 如果`++`寫在前面，相當於先做`b = b + 1`；然後再做`plusb = b`。

## Operator "sizeof"

使用`sizeof`這個運算符號，可以查看某個型別或某個資料所占用的 byte 數，而`sizeof`所回傳的值( byte 數)的型別為`size_t`(其實相當於`unsigned int`)。

下面我們來看個簡單的例子：

**範例 code：**
```C
#include <stdio.h>
int main(void)
{
    char c = 'a';
    size_t char_size;
	
    char_size = sizeof (char);
    printf("This character '%c' has %u byte;\n", c, sizeof(c));
    printf("Each 'char' takes up %u byte of space.\n", char_size);
    
    return 0;
}
```
**執行結果：**
```
This character 'a' has 1 byte;
Each 'char' takes up 1 byte of space.
```
在上述例子中，`char_size`是一個宣告成`size_t`型別的變數，用來儲存`sizeof (char)`所計算出來的值。此外，`%u`代表顯示的格式是`unsigned decimal integer`。其實`size_t`這個型別只是透過`typedef`的方式，來將它變成`unsigned int`的同義辭。當 compiler 遇到`size_t`的時候，它就會想起`size_t`其實就是`unsigned int`。假如我們今天打了這行程式碼：
```
typedef double real;
```
就可以替`double`定一個新的同義辭叫做 **real**。接下來程式裡面可以用
```
real x;
```
宣告變數`x`。乍看之下`x`屬於一個新的型別 **real**，但是其實 compiler 知道`x`的型別就是標準的`double`。



## 運算符號的先後順序

當我們在做數學上的運算時，會遵守「先乘除，後加減」的運算規則。這代表不同種類的運算符號，在計算上會有不同的先後次序。如果在撰寫程式碼時忽略了這些規則，可能會導致程式跑不出我們預期的成果。

在 C 語言中，運算符號的先後順序，大致上與我們的數學直覺相同。例如取餘數的`%`符號，與乘法`*`、除法`/`兩個運算符號，是同一層級的次序。如果對特殊符號的優先順序有疑慮的同學，可以參考以下的網站。

[C Operator Precedence](http://en.cppreference.com/w/c/language/operator_precedence)

## Expressions & Statements

## 型別轉換

## 強制型別轉換

---
# 練習題

## while loop-01
在螢幕上顯示一個簡單的對照表，列出數字 1 到 30 以及它們對應的平方值。

## while loop-02
1. 小明在銀行存入 10000 元，年利率 5%，總共存了十年，利息以複利計算。請依照年度列出小明每年能夠賺到的利息。
2. 承上題，假設小明每過一年又會再存入 10000 元，請把新的獲利表列出來。

## Operator “++” and operator “--”-01
補足下面參考程式碼中 "???" 的部分，讓使用者輸入一串字串，譬如 "ABCDE"，然後輸出像下面這樣的結果：
```
    A
   AB
  ABC
 ABCD
ABCDE
```

**參考程式碼：**
```C
#include <stdio.h>
#include <???.h>
int main(void)
{
   int i = 1, word_len;
   char word[40];
   scanf("???", ???);

   word_len = strlen(word);

   while (i <= word_len) {
      printf("%???", ???, ???, word);
      i++;
   }

   return 0;
}
```
