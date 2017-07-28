# Looping

## 前言
我們已經初步學會了如何使用`while`迴圈，本章將介紹其他 2 個常用的迴圈，分別是`for`迴圈和`do while`迴圈。另外，我們也將介紹更多好用的運算符號，並且引進多重迴圈的概念，來幫助我們寫出更複雜的程式。

首先，先讓我們對迴圈有更進一步的認識。

## 使用迴圈進行練續輸入
以往我們的程式都只能讓使用者進行一次輸入，這樣子的程式使用起來效率非常低落。搭配上迴圈之後，我們就可以讓使用者進行連續的輸入，並且印出相對應的結果。

**範例 code ：**
```C
#include <stdio.h>
#define BASE 1.6
int main(void)
{
    double kms;
    int status;
	
    printf("This is a km to mile converter.\n");
    printf("Please enter a number (q to quit): ");
    status = scanf("%lf", &kms);
    while(status == 1)
    {
        printf("%.2f kms equals to %.2f miles.\n", kms, kms/BASE);
        printf("Please enter another number (q to quit): ");
        status = scanf("%lf", &kms);
    }
    printf("This program is ended.\n");
    return 0;
}
```
**輸入：**
```
8
3.2
6.66
q

```
**執行結果(被*夾起來的為使用者輸入)：**
```
This is a km to mile converter.
Please enter a number (q to quit): *8*
8.00 kms equals to 5.00 miles.
Please enter another number (q to quit): *3.2*
3.20 kms equals to 2.00 miles.
Please enter another number (q to quit): *6.66*
6.66 kms equals to 4.16 miles.
Please enter another number (q to quit): *q*
This program is ended.

```
這個程式會不斷要求使用者輸入一個數字 (公里數) ，然後印出換算出來的英里數。當使用者輸入的資料不是數字時，迴圈就會結束。

在這個程式中，我們用到了`scanf()`的回傳值，來判斷我們是否輸入成功。其實每次呼叫`scanf()`，它除了把輸入的資料存入變數之外，還會回傳一個**整數值**給呼叫者，而`scanf()`回傳的是它**成功讀取的資料數目**。以這個例子來說，如果`scanf()`能成功讀到 1 個浮點數，它就會回傳 1；如果使用者輸入的不是浮點數 (譬如輸入英文字母 'q') ，則`scanf()`會回傳 0 ，表示沒有讀到任何浮點數。

程式進入`while`迴圈之前，會先判斷`status`的值是否等於 1。如果`status`等於 1 ，表示`scanf()`有正確地讀到資料，程式才會繼續執行迴圈內的指令。在 C 語言裡，判斷兩個數是否相等要用`==`符號，也就是兩個等號連在一起。

> 注意，**千萬不要錯用成一個等號**，因為`=`的意義是 assignment，也就是設定變數值。剛開始學 C 語言最常犯的錯誤之一，就是把`==`寫成`=`。大多數時候如果有這樣的誤用，程式 compile 還是會通過，而且也可以執行，但是意義完全不同，所以得到的結果會是錯的。

## C style Reading Loop
當同學漸漸開始熟悉迴圈，我們可以將上述範例 code 中的迴圈，改寫成比較簡潔的寫法：
```C
while(scanf("%lf", &kms) == 1)
{
    printf("%.2f kms equals to %.2f miles.\n", kms, kms/BASE);
    printf("Please enter another number (q to quit): ");
}
```
上面的程式每次進入迴圈內部之前，都會先讓使用者輸入資料，並檢查`scanf()`的回傳值是否等於 1 。如果`scanf()`的回傳值是 1 ，代表`scanf()`成功讀到一個浮點數。這樣的寫法不僅幫我們省去了一個變數`status`，程式碼的易讀性也提高了。

## Relational operators 
在 C 語言中，除了上面提到判斷是否等於的`==`符號，常用於撰寫判斷式的**比較符號**有以下六種：

operator | 意思
:------: | :--:
< | 小於
<= | 小於等於
\> | 大於
\>= | 大於等於
\== | 等於
!= | 不等於

其中特別要注意「等於」`==`與「不等於」`!=`的符號，與我們在數學上的習慣不一樣，大家在初學 C 語言時，很容易不小心犯下這種語法的錯誤。


## 無窮迴圈
在初學迴圈時，同學可能偶爾會遇到程式跑不出來，或是程式不預期地反覆印出我們不要的東西。這時我們都應該檢視自己，是不是不小心寫出了**無窮迴圈**。所謂的無窮迴圈是指：迴圈無法達到小括號`()`內 expression 的終止條件，而不斷反覆執行大括號`{}`內的指令。

* 第一種常見的情況是：**忘記在迴圈裡加上更新條件**，所以迴圈永遠達不到終止條件而停不下來。看看底下的錯誤示範：
```C
int n = 0;
while(n < 5) 
{
    printf("ascending  : %d \n", n);    
}
```
上面這個迴圈裡忘記更新變數`n`，所以變數`n`的值永遠保持是 0 ，判斷式`n < 5`永遠為 true 。所以程式會不斷印出`ascending  : 0`而停不下來。

* 另外一個常見的錯誤則發生在**小括號裡的判斷式**：
```C
status = scanf("%lf", &kms);
while(status = 1)
{
    printf("%.2f kms equals to %.2f miles.\n", kms, kms/BASE);
    printf("Please enter another number (q to quit): ");
    status = scanf("%lf", &kms);
}
```
當我們輸入浮點數時，程式看起來還相當正常。但是當我們輸入 'q' 之後，程式就掉進了無止境的迴圈，自己不停的印出`printf()`裡的資訊。這一切都是因為`status = 1`這個 expression 寫錯。本來應該用`status == 1`判斷`scanf()`是否有讀到一個浮點數。但是寫成了`status = 1`，變成在做 assignment 的動作。我們之前提過，所有的 expression 都有一個**值 (value)** ，而`status = 1`這個 expression ，它的值永遠都是 1。所以在這個錯誤範例中，迴圈的判斷式永遠為 true，迴圈永遠停不下來。

至於為什麼在我們輸入 'q' 之後，程式會開始不斷顯示`printf()`裡的資訊，而不再等待使用者進行下次輸入？這就和`scanf()`的運作機制有關。當`scanf()`讀不到想讀的東西時，它會把讀到但**格式不符**的資料又丟回 buffer，暫時保留在那裡，留待下次再處理。而下次再度呼叫`scanf()`時，'q' 依舊在那裡，而`scanf()`還是讀不到浮點數。所以`scanf()`又再次把 'q' 留在那裡，就這樣沒完沒了。

> 總之，在寫終止條件時要特別小心，千萬不要讓 expression 變成出乎我們意料的數值，**尤其是`==`和`=`不要錯用**。

## True and False
另外值得一提的是，在 C 語言中任何 expression 的值，除了整數 0 代表 false ，其他的整數值都代表 true 。請看以下的範例：

**範例 code ：**
```C
#include <stdio.h>
int main(void)
{
    int n = 3;
    while(n)
    {
    	printf("%d\n", n);
    	n--;
    }
    n = -3;
    while(n)
    {
    	printf("%d\n", n);
    	n++;
    }
    return 0;
}
```
**執行結果：**
```
3
2
1
-3
-2
-1
```
從上述例子可以看出：
* 當`n`的值不為 0 時，此時小括號內的 expression 的值不為 0 ，迴圈的判斷式為 true，程式會繼續執行迴圈內的指令
* 當`n`的值變為 0 時，此時小括號內的 expression 的值變成 0 。迴圈的判斷式變成 false，程式便會跳出迴圈

## for loop
在我們撰寫迴圈時，往往可以事先知道我們的迴圈將會執行的次數。為了達成這個目的，我們需要一個變數來幫助我們紀錄迴圈進行的次數。

**範例 code (1)：**
```C
#include <stdio.h>
#define TIMES 30
int main(void)
{
    int counter = 1;         //(1)
    while(counter <= TIMES)  //(2)
    {
    	printf("$");
    	counter++;           //(3)
    }
    return 0;
}

```
**執行結果(1)：**
```
$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$
```
在上面的範例 code 中，我們做了三件事情：

1. 在`(1)`當中，我們初始化計數器`counter`的值。
2. 在`(2)`當中，我們判斷迴圈是否已經做滿我們想要的次數。
3. 在`(3)`當中，我們在每一次做完迴圈更新`counter`的值。

如果今天我們想要使用上述的方式來描述迴圈，利用`for`**迴圈**將是一個更好的撰寫方式。請看以下另外一個範例：

**範例 code (2)：**
```C
#include <stdio.h>
#define TIMES 30
int main(void)
{
    int i;
    for(i = 0; i < TIMES; i++)
    {
    	printf("$");
    }
    return 0;
}
```
**執行結果(2)：**
```
$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$
```
`for`迴圈的標準語法如下：

```
for( 初始化 ; 判斷迴圈是否繼續執行 ; 更新變數值 ) 
{ 
    /* 迴圈主體 */
} 
```

小括號內的語法被**分號**分成三個部分。第一個部分是**初始化**。第一部分會在進入迴圈前先被執行，用來設定計數器的初始值，此部分只會執行一次。第二個部分會**判斷迴圈是否該繼續執行**。這部分就跟`while`迴圈小括號內的判斷式作用相同：如果此部分判斷式的值為 true ，迴圈才會去做大括號內部的指令，否則就直接跳出迴圈。第三個部分則是**更新變數 (計數器) 的值**。第三部分的指令，會等到迴圈跑完大括號內的指令才會被執行。

由於`for`迴圈算是最常使用的一個迴圈，底下我們將列出許多範例跟練習題，讓大家熟悉語法。

* 列出從 1 到 100 內所有 7 的倍數：

**範例 code：**
```C
#include <stdio.h>
#define LIMIT 100
int main(void)
{
    int n;
    for(n = 7; n <= LIMIT; n = n + 7)
    {
    	printf("%d \n", n);
    }
    return 0;
}
```

* 除了`int`型態的變數，`char`型態，甚至是`double`型態的變數也能寫在`for`迴圈當中。以下的範例從 5 到 6 ，每隔 0.1 列出該數字的平方：

**範例 code ：**
```C
#include <stdio.h>
#define GAP 0.1
int main(void)
{
    double i;
    printf("number  square\n");
    for(i = 5; i <= 6 ; i = i + GAP)
    {
    	printf(" %2.2f   %2.2f\n", i, i*i);
    }
    return 0;
}
```

* 括號內的各個部分不一定要有指令，但是一定要用**分號**將三個區域分開。例如我們什麼都不做，就會變成一個無窮迴圈：

```C
for( ; ; )
{
    printf("I'm a infinite loop!\n");
}
```

* 如果我們想在初始化與更新變數值的地方做兩個以上的指令，只要把每個 statement 用**逗號**來分開就可以了：

**範例 code ：**
```C
#include <stdio.h>
#define LIMIT 10
int main(void)
{
    int i, sum;
    for(i = 1, sum = 1; i <= LIMIT ; i++, sum = sum + i)
    {
    	printf("sum from 1 to %2d : %2d\n", i, sum);
    }
    return 0;
}
```
在上面的範例中，變數`i`是我們的計數器，而`sum`則是記錄累加的總和。這個程式列出了從 1 到 10 中的每個數字，從 1 開始累加到自己的數值。

## More assignment operators
在 C 語言當中，除了`=`這樣的 assignment 符號之外，還有`+=`、`-=`、`*=`、`/=`、`%=`等好用的 assingnment符號。如果我們寫了以下這些 statement ：
```
x += 5;
y %= 7;
```
第一個 statement 其實跟`x = x + 5;`有一樣的功用；而第二個 statement 則跟`y = y % 7;`相同。這樣的寫法看起來比較簡潔，但是同學不一定要使用這樣的寫法，只要順著自己的習慣就可以了。知道有這種寫法，看到別人的程式才不會搞不懂程式在寫什麼。
## do ... while loop
某些情況下，迴圈會保證至少被執行一次，這時候就適合用`do ... while`迴圈。它的效果是先做迴圈內容，最後在判斷條件是否要繼續，譬如下面的例子，要求使用者猜年齡，直到輸入正確為止。由於這個動作至少要做一次，所以用`do ... while`程式會比較簡潔，它的語法如下：

**範例 code(1) ：**
```C
#include <stdio.h>
#define AGE 87
int main(void)
{
    int age;

    do{
        printf("Please guess my granny's age : ");
        scanf("%d", &age);
    } while(age != AGE);
    printf("Your right!\n");
    return 0;
}
```
要注意在`do ... while`的用法裡面， while 的條件判斷之後有個**分號**，不能漏掉，這是`do ... while`和標準`while`的語法不同之處。以下我們將上面的範例改寫成`while`迴圈的型式，同學可以比較看看，如果用`while`而不是 `do ... while`，寫起來會有點累贅：

**範例 code(2) ：**
```C
#include <stdio.h>
#define AGE 87
int main(void)
{
    int age;
    printf("Please guess my granny's age : ");
    scanf("%d", &age);
    while(age != AGE)
    {
        printf("Please guess my granny's age : ");
        scanf("%d", &age);
    }
    printf("Your right!\n");
    return 0;
}
```

## Nested loop (多重迴圈)
更複雜的迴圈使用方法，是用一個迴圈來包住另一層迴圈，此種迴圈稱作**多重迴圈 (nested loop)** 。學會使用多重迴圈，可以讓我們用很簡單的程式碼，做到許多複雜的功能。**雙重迴圈**是一個最常用的 nested loop ，常用來處理以二維表格方式呈現的資料 (直行橫列的資料) 。

譬如我們想要輸出一個用`*`號填滿的長方形，寬和高分別是 25 和 7。我們已經學過用一層迴圈反覆輸出，所以要印出一排 25 個星號可以用下面的寫法：

```C
for(j = 0; j < 25; j++)
{
   printf("*");
}
printf("\n");
```
顯示了一串 25 個 * 之後，再用`printf("\n");`換行。接下來如果我們再用一個迴圈把上面的程式碼包起來，變成：
```C
for(i = 0; i < 7; i++)
{ 
   for(j = 0; j < 25; j++)
   { 
      printf("*");
   }
   printf("\n");
}
```
原本的程式碼就會重複做 7 次，每次都會顯示一排 25 個`*`號。如此一來就可以印出一個 25 * 7 的長方形。

完整的程式碼如下：

**範例 code ：**
```C
#include <stdio.h>
#define ROW 7
#define COL 25
int main(void)
{
    int i, j;
    for(i = 0; i < ROW; i++)
    {
    	for(j = 0; j < COL; j++)
        {
            printf("*");
        }
        printf("\n");
    }
    return 0;
}
```
**執行結果：**
```
*************************
*************************
*************************
*************************
*************************
*************************
*************************

```

> 當我們開始練習撰寫雙重迴圈 (甚至是以後會出現的三重迴圈)，我們應該養成良好的變數命名習慣。習慣上，會將控制迴圈進行的變數 (計數器) 依序命名為`i`、`j`、`k`。
 
如果我們稍微修改上述的範例 code ，更改內部迴圈初始化的地方，新的結果就會跟原本的結果不太一樣：

**範例 code ：**
```C
#include <stdio.h>
#define ROW 7
#define COL 25
int main(void)
{
    int i, j;
    for(i = 0; i < ROW; i++)
    {
    	for(j = i; j < COL; j++)
        {
            printf("*");
        }
        printf("\n");
    }
    return 0;
}
```
**執行結果：**
```
*************************
************************
***********************
**********************
*********************
********************
*******************

```

與原本的輸出結果相比，新的結果缺了右下角的一小部分。這是因為我們更改了內部迴圈`j`的初始值，將`j`的初始值與外部迴圈`i`做互動，所以內部迴圈會印出的`*`符號越來越少。

除了更改內部迴圈的初始值，內部迴圈的終止條件，也很常與外部的迴圈做連結。以下我們再給一個範例給大家參考，同學也可以多加嘗試不同的寫法會印出怎樣的結果。

**範例 code ：**
```C
#include <stdio.h>
int main(void)
{
    char i, j;
    for(i = 'A'; i <= 'H'; i++)
    {
    	for(j = 'A'; j <= i; j++)
        {
            printf("%c", j);
        }
        printf("\n");
    }
    return 0;
}
```
**執行結果：**
```
A
AB
ABC
ABCD
ABCDE
ABCDEF
ABCDEFG
ABCDEFGH

```
---

# 練習題

## 使用迴圈進行練續輸入-01
請撰寫一個時間轉換的程式，將使用者輸入的秒數換算成幾分幾秒，並使用`while`迴圈，讓使用者可以不斷輸入資料。當使用者輸入的資料小於或等於零，迴圈就停止。

## for loop-01
用 for 迴圈計算 0 到 100 之間，奇數數字的平方和。

## for loop-02
用 for 迴圈一一列出 1 到 100 每個數字的立方。

## nested loop-01
利用雙重迴圈，輸出九九乘法表的內容。

## nested loop-02
利用雙重迴圈，輸出九九乘法表左上半部的內容。

**範例輸出 (格式為%3d) ：**
```  
  1  2  3  4  5  6  7  8  9
  4  6  8 10 12 14 16 18
  9 12 15 18 21 24 27
 16 20 24 28 32 36
 25 30 35 40 45
 36 42 48 54
 49 56 63
 64 72
 81

```
## nested loop-03
利用雙重迴圈，輸出下列範例輸出的內容。

**範例輸出 (第一列有7個\*) ：**
```  
*******
 ******
  *****
   ****
    ***
     **
      *

```

## nested loop-04
讓使用者輸入一個大寫英文字母，譬如 'E'，然後輸出像範例輸出一樣排列的形狀。

**範例輸出：**
```  
    A
   ABA
  ABCBA
 ABCDCBA
ABCDEDCBA

```
