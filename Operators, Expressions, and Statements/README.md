# Week 4 & Week 5

### 內容：Operators, Expressions, and Statements; Looping

---

我們已經大致知道如何輸入資料給程式，以及如何透過程式輸出資料到螢幕上。具備了與使用者互動的基本能力之後，再來就可以開始寫更複雜的程式。我們接下來要介紹一些運算符號，並介紹如何在程式裡加入迴圈，讓程式反覆做類似的運算，畢竟這才是電腦的強項。我們就來看看如何利用電腦重複地做大量的計算。

---

## Expressions 與 Statements

稍微介紹一下 C 語言語法裡兩個常用的名詞：expression 與 statement。底下的運算式都是 expression：
```
a + 5
x = 3 * 4
k > 3
```
最需要記得的觀念是每個 expression 都代表某個值，譬如`b = 1 + 2`的值是 3；甚至條件判斷式也有值，譬如`5 > 3`的值就是 1 (在 C 裡面 1 代表 true，而 0 代表 false)。至於 statement 簡單的說就是以分號結尾的句子，譬如： 
```
x = 3 * 4;
```
稍微知道這兩個名詞的意思，往後我們提到的時候就會有概念，解釋起來也會比較方便。

## Operators
### Relational Operators

Operator | 意思
:--------: | :--:
< | 小於
<= | 小於等於
\> | 大於
\>= | 大於等於
== | 等於
!= | 不等於

比較新奇的應該只有`==`和`!=`，其他跟我們在算數學的時候用的寫法一樣。在比較兩個數值的大小關係時，如果比較的是浮點數，要特別注意，只能使用`>`和`<`，因為經過一些數學運算，兩個浮點數是否還能完全相等往往會出乎我們意料之外。


## while loop
### 先來看一個例子：
```C
#include <stdio.h>
int main(void)
{
    int x, n;
    int i, sum;
    int a[50];

    printf("How many numbers? (n<50) ");

    scanf("%d", &n);
    i = 0;
    while (i<n) {

        printf("Input a[%d]: ", i);
        scanf("%d", &x);

        printf("The number is %d\n", x);

        a[i] = x;

        i = i + 1;
    }
    i = 0;

    sum = 0;

    while (i<n) {

        printf("%d + ", a[i]);
        sum = sum + a[i];
        i++;
    }
    printf("\b\b= %d\n", sum);
    return 0;
}
```

### 執行結果：
```
How many numbers? (n<50) 5
Input a[0]: 10
The number is 10
Input a[1]: 20
The number is 20
Input a[2]: 30
The number is 30
Input a[3]: 40
The number is 40
Input a[4]: 50
The number is 50
10 + 20 + 30 + 40 + 50 = 150
```

### 範例code：

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
   while (celsius <= UPPER) {
      fahr = 1.8*celsius + 32;
      printf("%4d %12d\n", celsius, fahr);
      celsius = celsius + STEP;
   }
   printf("---------------------\n");
   return 0;
}
```

### 執行結果：
```
Celsius    Fahrenheit
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
```

這個程式會列出攝氏和華氏溫度的對照表，範圍從攝氏零度到一百度。程式用到了 while 迴圈來達到反覆呼叫 printf() 的效果。當程式執行到 while 時，會先檢查括號裡的式子：
```
(celsius <= UPPER)
```
這個式子是在判斷 celsius 是否小於 UPPER，由於在執行到 while 之前的一行，celsius 的值被設為 LOWER (值為 0)，所以目前 celsius 的確小於 UPPER。由於 celsius 與 UPPER 的實際大小關係符合這個式子所描述，所以這個式子會得到 true，表示這個式子是成立的，因此程式就可繼續執行 while 迴圈接下來的敘述。整個 while 迴圈的內容被包含在 {} 所定義的有效範圍內。接下來要執行的程式碼就是
```C
fahr = 1.8*celsius + 32;
printf("%4d %12d\n", celsius, fahr);
```


代入換算公式，再把對照值有固定格式印出來。接下來 celsius 的值會被累加上 STEP
```
celsius = celsius + STEP;
```
這一行的意思就是把 celsius 加上 STEP 之後，得到的值再存回 celsius。所以 celsius 的值從 0 變成 10，然後程式的進行會回到 while 的判斷式，再次判斷 celsius <= UPPER 是否成立。這樣一直反覆進行下去，不斷地在螢幕上印出攝氏華氏對照值，而且 celsius 的值也會不斷增加。一直到 celsius 的值變成 100，然後再經過 celsius = celsius + STEP; 之後變成 110，這時候回到判斷式 celsius <= UPPER 就會發現條件不成立，因為 110 已經比 100 大，所以 while 迴圈就此結束，程式接下來直接跳到 while { ... } 之外，執行下一行程式碼 
printf("---------------------\n"); 。


### 練習題(1)：
1. 在螢幕上顯示一個簡單的對照表，列出數字 1 到 30 以及它們對應的平方值。
2. 小明在銀行存入 10000 元，年利率 5%，總共存了十年，利息以複利計算。請依照年度列出小明每年能夠賺到的利息。
3. 承上題，假設小明每過一年又會再存入 10000 元，請把新的獲利表列出來。

### 範例
```C
#include <stdio.h>
#include <string.h>
#define STAR "**********"
int main(void)
{
   int i = 1, j = 0;
   while (i <= strlen(STAR)) {
      printf("%.*s\n", i++, STAR);
      j = 0;
      while (j < 50000000) j++;
   }
   return 0;
}
```

### 輸出結果：
```
*
**
***
****
*****
******
*******
********
*********
**********
```

### 練習題(2)：
1. 補足下面範例 code "???" 的部分，讓使用者輸入一串字串，譬如 "ABCDE"，然後輸出像下面這樣的結果：
```
    A
   AB
  ABC
 ABCD
ABCDE
```

### 參考程式碼：
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
2. 補足下面範例 code "???" 的部分，讓使用者輸入一個正整數，譬如 13579，利用 while 迴圈以及 % 求餘數，輸出如下的結果：
```
The number 13579 can be written as 
9 + 7*10 + 5*100 + 3*1000 + 1*10000.
```
### 參考程式碼：
```C
#include <stdio.h>
int main(void)
{
    int i, k;
    scanf("%d", &i);
    printf("The number %d can be expressed as\n", i);
    k = 1;
    
    while (???) {
        printf("%d*%d + ", ???, ???);
        i = ???;
        k = ???;
    }
    // printf("\b\b\b.  \n");
    
    return 0;
}
```

## ++ and \-\- operators
在上面的例子的第31行中，我們看到了一個新的 operator "++"。這邊我們再用一個範例來說明。
### 範例code：
```C
#include <stdio.h>
int main(void)
{
   int lower = 0, upper = 10;

   while (lower < upper) {
      lower++;
      upper--;
      printf("lower = %d, upper = %d \n", lower, upper);
   }
   return 0;
}
```
### 執行結果：
```
lower = 1, upper = 9
lower = 2, upper = 8
lower = 3, upper = 7
lower = 4, upper = 6
lower = 5, upper = 5
```

程式用到 ++ 和 \-\- 來改變變數的值。
- lower++，就是把 lower 的值增加一，相當於 lower = lower + 1。
- 同理，upper \-\-，相當於 upper = upper - 1。
當 lower 的值比 upper 還小，迴圈就會繼續進行；而當 lower 的值增加到等於或超過 upper 時，迴圈就結束。

##  \+\+a 與 a\+\+ 的差別？
### 範例code：
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
### 執行結果：
```
a   aplus   b   plusb
2     1     2     2
```
以上兩種寫法都達到了把 a 和 b 的值增加一的效果。但是從 aplus 和 plusb 的值可以看出來，a++ 和 ++b 的時間點稍有不同。
- 如果 ++ 寫在後面，相當於先做 aplus = a；然後再做 a = a + 1。
- 如果 ++ 寫在後面，相當於先做 b = b + 1；再做 plusb = b 的動作。

## 利用while迴圈做連續輸入
```C
#include <stdio.h>
int main(void)
{
   long num;
   long sum = 0L;
   int status;

   printf("Please enter an integer to be summed ");
   printf("(q to quit): ");
   status = scanf("%ld", &num); /* %ld for long, status is the return value */
   while (status == 1) {        /* == means "is equal to" */
      sum = sum + num;
      printf("Please enter next integer (q to quit): ");
      status = scanf("%ld", &num);
   }
   printf("Those integers sum to %ld.\n", sum);
   return 0;
}
```
這個程式會不斷要求使用者輸入整數，然後當使用者輸入的資料不是數字時，迴圈就會結束，並且把所有數目的總和算出來，譬如：
```C
Please enter an integer to be summed (q to quit): 12
Please enter next integer (q to quit): 34
Please enter next integer (q to quit): -56
Please enter next integer (q to quit): 789
Please enter next integer (q to quit): q
Those integers sum to 779.
```
型別 long 我們之前比較少用，宣告為 long 的變數可以儲存更大範圍的整數值。程式一開始顯示一些資訊請使用者輸入整數，然後就用 scanf() 讀取輸入的數值並存到 num 中。因為要讀的是 long，所以用 %ld。另外有個新東西是我們用到了 scanf() 的回傳值。其實每次呼叫 scanf() ，它除了把輸入的資料存入參數之外，還會回傳一個整數值給呼叫者。不過因為我們之前用不到，所以都忽略掉而沒有拿變數去接收這個回傳值。由於 scanf() 回傳的是它成功讀取的資料數目，以這個例子來說，如果 scanf() 能成功讀到 1 個長整數 (對應到 %ld)，它就會回傳 1，如果使用者輸入的不是整數 (譬如輸入英文字母 'q')，則 scanf() 會回傳 0 表示沒有讀到任何整數，所以 status 的值就會是 0。當進入 while 迴圈的時候，會先判斷 status 是否等於 1，等於 1 才表示 scanf() 有讀到整數資料。在 C 語言裡判斷兩個數是否相等要用 == 符號，也就是兩個等號連在一起。 注意，千萬不要錯用成一個等號，因為 = 的意義是 assignment，也就是設定變數值，**剛開始學 C 語言最常犯的錯誤之一就是把 == 寫成 =**，大多數時候如果有這樣的誤用，程式 compile 還是會通過，而且也可以執行，但是意義完全不同，所以得到的結果會是錯的。


## C Style Reading Loop
前面範例的迴圈，在寫法上通常會被簡化。譬如，原來是：
```C
   status = scanf("%ld", &num); 
   while (status == 1) {     
      sum = sum + num;
      printf("Please enter next integer (q to quit): ");
      status = scanf("%ld", &num);
   }
```
會習慣寫成：
```C
   while (scanf("%ld", &num) == 1) {     
      sum = sum + num;
      printf("Please enter next integer (q to quit): ");
   }
```
這樣的寫法可以省去變數 status。這樣的用法等於一次做兩個動作，先利用 scanf() 讀入資料，同時判斷回傳值是否等於 1。熟練之後，這樣的寫法會蠻簡潔方便。如果還不熟練，寫的時候就要稍微小心不要造成迴圈停不下來。

## 無窮迴圈
使用 while 迴圈要注意停止條件，看看底下這個錯誤示範
```C
int i = 1;
while (i < 5) {
   printf("Hello!\n");
}
```
執行這個程式會發生什麼事? 迴圈裡面完全沒有更改 i 的值，所以 i < 5 的條件會一直成立，迴圈無法停下來，只能用暴力手段把程式停掉。而如果程式改成
```C
int i = 1;
while (--i < 5) {
   printf("Hello!\n");
}
```
程式還是錯的，但是有可能會自己停止。當 i 不斷遞減變成絕對值越來越大的負數，到了極限之後反而變成絕對值最大的正數，等於繞了一圈循環回來，所以 i 的值就比 5 大，迴圈就停止了。底下的程式碼可以看出循環的現象。
```C
#include <stdio.h>
int main(void)
{
   char i = 1;
   while (--i < 1) {
      printf("%d\n", i);
   }
   printf("%d\n", i);
   return 0;
}
```
```C
#include <stdio.h>
int main(void)
{
   char i = 1;
   while (++i < 1) {
      printf("%d\n", i);
   }
   printf("%d\n", i);
   return 0;
}
```
由於 char 型別只佔一個 byte 所以可以比較快停下來。如果換成 int 就要跑比較久。
