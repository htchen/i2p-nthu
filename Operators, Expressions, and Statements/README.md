# Operators, Expressions, and Statements

## 前言

我們已經大致知道如何輸入資料給程式，以及如何透過程式輸出資料到螢幕上。具備了與使用者互動的基本能力之後，再來就可以開始寫更複雜的程式。我們接下來要介紹一些 **運算符號(operator)**，並介紹如何在程式裡加入 **迴圈(loop)**，讓程式可以反覆做類似的運算，畢竟這才是電腦的強項。我們就來看看如何利用電腦重複地做大量的計算。

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


第一行我們將`celsius`代入換算公式，算出華氏溫度`fahr`。第二行再用`printf()`把對照的華氏溫度印出來。最後一行則是將`celsius`的值累加上`STEP`，然後再將得到的值存回`celsius`。所以跑過第一次迴圈之後，`celsius`的值將從 0 變成 10 。

在程式做完大括號`{}`裡面定義的事情之後，整個程式會回到一開始小括號`()` 的式子，再次判斷`celsius <= UPPER`是否成立。如果式子依然成立，`while`迴圈就會再次執行大括號`{}`裡面定義的事情。如此一直反覆進行下去，直到小括號`()`裡面的式子不再被滿足，`while`迴圈就此結束。在範例 code 中，`celsius`的值每次都會加 10 ，一直不停地累加上去。當`celsius`的值大於我們設定的上界`UPPER`，程式就會跳出大括號`{}`之外，繼續執行之後的程式碼。

## Operator "="
基本的運算符號如`+`、`-`、`*`、`/`相信大家都已不陌生，它們分別代表的是加、減、乘、除的四則運算符號。接下來我們會介紹一些在 C 語言中常用的運算符號。

首先我們來介紹`=`這個運算符號。`=`是一個把**值設定給變數**的運算符號 (Assignment Operator)，下面介紹第一種簡單的用法：
```
count = 1000;
```
這個式子代表直接將`count`這個變數的值設為 1000 。而另外一種常見的用法如下：
```
count = count + 1;
```
這個用法則代表：我們先將右邊算式的值給算出來，最後再存到左邊的變數裡面。範例中，如果`count`的初始值是 1000 的話，我們計算右邊算式的值會得到 1001 ，最後我們再將得到的值存`count`這個變數裡面。

![](https://i.imgur.com/t7Pr0l5.png)

## Operator "%"
除了常見的四則運算符號之外，C 語言裡面還內建了一個好用的運算符號`%`，它的功用是拿來求餘數。例如 24%5 得到的數字就是 4 。下面我們利用`while`迴圈以及`%`求餘數，讓使用者輸入一個正整數之後，輸出該正整數的十進位分解。

**範例 code：**
```C
#include <stdio.h>
int main(void)
{
    int num, p;
    scanf("%d", &num);
    printf("The number %d can be written as\n", num);
    p = 1;
    
    while(num >= 10) 
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

在這個程式中，我們利用`num % 10`來求出個位數字，之後便將`num`除以 10 。由於正整數的除法是無條件捨去，所以我們便可以在下一次迴圈，再次利用`%`求得十位數字。依此類推，便可把所有位數都求出來。變數`p`紀錄的是指數的部分 (10 的 n 次方) ，所以在每一次迴圈內都要累乘 10 。

由於我們的最高位數字後面不用印出加號，所以當`num`小於 10 ，程式便跳出迴圈，由最後一行`printf()`印出最高位數字。

## Operator "++" and operator "\-\-" 
接下來我們要介紹遞增`++`與遞減`--`的運算符號。假設現在我們有一個變數`count`：

- `count++`，就是把`count`的值增加一，相當於`count = count + 1`。
- `count--`，就是把`count`的值減去一，相當於`count = count - 1`。

讓我們來看個實際的例子：

**範例 code(1)：**
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

**範例 code(2)：**
```C
#include <stdio.h>
int main(void)
{
    int i = 'A';

    while (i <= 'Z') 
    {
        printf("%c: %3d  ", i, i);
        i++;
    }
    return 0;
}
```
**執行結果：**
```
A:  65  B:  66  C:  67  D:  68  E:  69  F:  70  G:  71  H:  72  I:  73  J:  74  K:  75  L:  76  M:  77  N:  78  O:  79  P:  80  Q:  81  R:  82  S:  83  T:  84  U:  85  V:  86  W:  87  X:  88  Y:  89  Z:  90  
```

這個程式非常簡短，作用是把 'A' 到 'Z' 的 ASCII code 列出來。所以我們可以用 'A' 或 'Z' 這種字元表示方式來設定整數的值，例如：
```
i = 'A';
```
上面的程式碼，代表我們把變數`i`的值設定成 'A' 在 ASCII code 上所對應的數字，也就是 65 。除此之外，我們也能拿這種表示方式來做比較，譬如：
```
i <= 'Z'
```
在上述的範例 code 中，整個迴圈會不斷增加`i`的值，直到`i`超過 'Z' 的 ASCII code 值。

##  \+\+a 與 a\+\+ 的差別？

遞增與遞減的運算符號，除了可以放在變數後面 (例如：`count++`) ，也可以放在變數的前面 (例如：`++count`) 。不過放在變數前面與後面的意義稍有不同，請看以下範例：

**範例 code：**
```C
#include <stdio.h>
int main(void)
{
    int x = 1, y = 1;
    int pre, post;
    pre = ++x * 2;
    post = y++ * 2;
    printf("x    : %d\n", x);
    printf("pre  : %d\n", pre);
    printf("---------\n");
    printf("y    : %d\n", y);
    printf("post : %d\n", post);
    return 0;
}

```
**執行結果：**
```
x    : 2
pre  : 4
---------
y    : 2
post : 2
```

以上兩種寫法都達到了把`x`和`y`的值增加一的效果。但是從`pre`和`post`的值可以看出來，`x++`和`++y`兩種寫法，在遞增的時間點稍有不同：

- 如果將`++`寫在前面，相當於先讓`x`遞增 1 ，再去做乘以 2 的動作。
- 如果將`++`寫在後面，相當於先做乘以 2 的動作，然後才讓`y`遞增 1 。

## Operator "sizeof"

使用`sizeof`這個運算符號，可以查看某個型別或某個資料所占用的 byte 數，而`sizeof`所回傳的值 (byte 數) 的型別為`size_t` (其實相當於`unsigned int`) 。

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
在 C 語言中， Statements 是建構起整個程式的基本要素；而一個 statement 是由好幾個 expressions 所組成的。

### Expressions
一個合法的 expression ，由一群**運算子**(運算符號， operator)與**運算元**(operand)組成。運算元泛指的就是可以放進運算子裡面，讓運算子進行運算的東西。例如：
```
x + 3
```
在上述的這個 expression 裡面，`+`是運算子，而變數`x`與常數`3`則是運算元。下面我們列舉一些合法的 expression：

* 6
* -4 + 7
* x*(y + z)/10
* a = ++b * 666
* n < 10

而每一個 expression ，都可以算出一個特定的值出來。值得注意的是，就算是比較大小 expression ，也能算出特定的值：得到 1 代表判斷式為真(true)，得到 0 則代表判斷式為假(false)。

expression | value
:--------: | :---:
8 + 9 | 17
z = -2 + 6 | -4
10 < 5 | 0 (false)

### Statements

在 C 語言中，所有以**分號**結尾的句子，都可以看做是一個 statements 。例如：
```
x = 5 + 4
```
是一個 expression ，但是
```
x = 5 + 4;
``` 
則變成了一個 statement 。

## 型別轉換

當 expression 或 statement 中所出現的變數或常數之間的型別不同時，C 語言會做型別轉換，將不同變數或常數轉成相同型別。底下是型別轉換的基本規則：

1.  在 expression 中的`char`會轉換成`int`，譬如我們前面看到的`i <= 'Z'`。
2.  當兩種型別混用時，位階較低的型別會轉成位階較高的；位階高低順序如下：`double`、`float`、`unsigned long`、`long`、`unsigned int`、`int`。
3. 在等式的 statement 中，型別會被轉換成等號左邊的變數的型別。

通常，第三個規則所造成的型別轉換有可能會讓我們不小心寫出 bug。

**範例 code ：**
```C
#include <stdio.h>
int main(void)
{
    char c;
    int n;
    double fl;

    c = 'K';                    // (1)
    n = c;                      // (2)
    fl = n;                     // (3)
    printf("c = %c\n", c);
    printf("n = %d\n", n);
    printf("fl = %2.2f\n", fl);

    printf("-----------\n");

    c = c + 2;                  // (4)
    n = fl + c / 2;             // (5)
    fl = n * 1.5 + c;           // (6)
    printf("c = %c\n", c);
    printf("n = %d\n", n);
    printf("fl = %2.2f\n", fl);
    return 0;
}

```

**執行結果：**
```
c = K
n = 75
fl = 75.00
-----------
c = M
n = 113
fl = 416.00
```

1. 在`(1)`當中，我們把字元 'K' 存在變數`c`中(只佔 1 byte)。
2. 在`(2)`當中，我們把`char`型態的`c`，轉成`int`型態存在變數`n`中。
3. 在`(3)`當中，我們把`int`型態的`n`，轉成`double`型態存在變數`fl`中。
4. 在`(4)`當中，`c`被加上了 2 (都是`int`型態)，此時`c`的值變成 77 ，所對應的字母是 'M'。
5. 在`(5)`當中，我們必須計算`fl + c / 2;`。當程式算完`c/2`，會把得到的值(38)轉成`double`型態，再去跟前面的`fl`做計算。最後的結果會被降成`int`型態，存進`n`當中。
6. 在`(6)`當中，`n`會被轉成`double`型態先進行乘法，隨後`c`也會被轉成`double`型態進行加法，最後的結果再存進`fl`裡面。

[C - Type Casting](https://www.tutorialspoint.com/cprogramming/c_type_casting.htm)

## 強制型別轉換

除了讓 C 語言自動做型別轉換之外，我們也可以強制常數或變數做型別轉換，避免程式發生無預警的錯誤。只要在我們的目標常數或變數前面加上小括號`()`，並且在小括號裡面置入我們想要強制轉換的型別。舉例來說：
```C
int x; 
x = (int) 2.3 * (int) 3.8;
```
變數`x`得到的結果會是 6 。因為 2.3 被轉成`int`型態而變成 2，同時 3.8 也被轉成`int`型態而變成 3 ，所以 2*3 就得到 6。

如果我們將上述寫法寫成 `x = 2.3 + 2.8;`，則 x 的值會是 8。因為程式會先計算出 8.74 ，然後再將 8.74 無條件捨去轉換成`int`型態。


---
# 練習題

## while loop-01
在螢幕上顯示一個簡單的對照表，列出數字 1 到 30 以及它們對應的平方值。

## while loop-02
1. 小明在銀行存入 10000 元，年利率 5%，總共存了十年，利息以複利計算。請依照年度列出小明每年能夠賺到的利息。
2. 承上題，假設小明每過一年又會再存入 10000 元，請把新的獲利表列出來。

## Operator “%”-01
讓使用者輸入一個十進位的數字，程式必須印出該數字的二進位表示法。

## Operator “++” and operator “\-\-”-01
讓使用者輸入兩個++大寫++英文字母，然後程式必須輸出字母順序介於兩者之間的其他字母。

## Operator “++” and operator “\-\-”-02
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
