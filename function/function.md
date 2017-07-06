# function

## 前言

### 種類

- library 裡已經寫好的function, include 後進來呼叫。

- 自己寫function， 在別的function 裡面呼叫他

### 舉例

- 引用library的
    - scanf( ), printf( ), 

```c
#include <stdio.h>
nt main()
{
    printf("Y.O.L.O.\n");
    return 0;
}
```

- 自己寫的function （如下）

```c
#include <stdio.h>
int add ( int m, int n)
{
    int ans;
    ans = m + n;
    return ans;
}

int main()
{
    int a = 2;
    int b = 5;
    printf("ans : %d\n", add(a, b));
    return 0;
}
```

## 如何宣告 function

### 模板：

```c
[回傳值的型別] [function名稱] ( [傳進來的參數] )
{

    [function主體]
    
    return [要回傳的值];
}

舉例：

int add (int a, int b)
{
    int sum = a + b;
    
    return sum;
}
```
- [回傳值的型別] ：以範例來說，要回傳的值是 sum ， 而 sum 的 型別是 int ， 所以這裡就要放 int。假設今天沒有要回傳值，那麼就宣告為void ，即可不用回傳值。

:::info
基本上，[ 回傳值的型別 ] 要看
1. 原本呼叫的那個function 所定義的那個變數的型別
2. 宣告的這個function 裡要回傳的值的型別

兩者的型別必須相同。
:::

- [function名稱] ：可以自己取，但不得重複，也不行跟寫在library裡的 function 撞名。

- [傳進來的參數] ：把所有要傳進來的參數寫在這裡。


### function 的位置

::: info
注意：宣告的 function 一定要比呼叫他的function 還要上面。 

:::

原因在於，當compiler在編譯時，會一行一行的編譯下來，所以，你必須先跟 compiler 說 有這個 function， 這樣在編譯到 呼叫 function 那一行時， compiler 才不會不認得它。

#### 第一種宣告方式： 直接把整個 function 寫在呼叫的function前面。
```clike=
#include <stdio.h>
int add ( int m, int n)
{
    int ans;
    ans = m + n;
    return ans;
}

int main()
{
    int a = 2;
    int b = 5;
    printf("ans : %d\n", add(a, b));
    return 0;
}
```



#### 第二種宣告方式：只宣告 function prototype，之後再把function 主體補齊。

```clike=
#include <stdio.h>
int add ( int , int );

int main()
{
    int a = 2;
    int b = 5;
    printf("ans : %d\n", add(a, b));
    return 0;
}

int add ( int m, int n)
{
    return m+n;
}
```

這種方法的好處在於，只要把所有的 function 都宣告在整個程式的上方，就不用擔心哪天不小心哪個function 忘記宣告或是位置放錯。

::: danger
宣告 function prototype 時，要傳入的變數名稱並不重要，可以之後再決定，重要的是 [ 型別 ] 必須先訂出來。
:::

### return 的位置

先看看下面這個範例
```clike=
#include <stdio.h>

void go()
{
    printf("hi\n");
    return;
    printf("my name is kronos\n");
}

int main ()
{
    go();
    return 0;
}

```
結果：
hi
＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿
講解： 你會發現在 return 下面那一行的 printf 永遠不會執行到，這代表 function 的結束是在 return 而不是在 整個function 都執行完。
.
.
.


## 為什麼要寫function
舉個例子來說，當你在整理房間時，總不能把所有東西都塞在同一個櫃子了，這樣當你要找一個東西時，不僅很麻煩又很難找。

寫程式也是一樣，如果今天你把所有東西都寫在 main 裡面， 那麼，一個地方出了什麼bug話，該怎麼辦？ 從頭早起？還是打掉重練？ 這時有寫 function 了話，就能一個一個 function 檢查， 不僅有效率，畫面也比較整潔，這就是為什麼是寫 function 了。

而且，寫function的另一個好處就在於，我們可以把一個大任務拆成一個個小問題來解決，每一個小問題寫一個function，而那個function就能專心處理這些事情，那麼就不至於陷入不知從何下手的窘境。

### 舉例
假設今天給你一個班所有同學（100位學生 0 ~ 99)的成績，先找出全班最高分的分數，算一下有多少人及格，並求全班平均，以及標準差，並把整個班的成績用樹狀圖表示。

ps.
標準差 reference ： https://zh.wikipedia.org/wiki/標準差

先把題目簡化一下，變成：
1. 找出全班最高分的分數
2. 算一下有多少人及格
3. 全班平均
4. 標準差
5. 樹狀圖



### step 1 先把整個問題分成好幾個小問題。那麼 main 裡面應該會變成這樣

```clike=
int main(void)
{
    double ave;
    
    input();
    
    printf("highest : %d\n", first()); //求最高分
    printf("save : %d\n", save(60));   //幾個人及格
    
    ave = average();                    //求平均
        
    printf("average : %lf\n", ave);
    printf("sd : %Lf\n", standard_deviation(ave));//標準差
    
    graph(); //作圖
    
    return 0;
}
```
這樣問題就變得容易許多了。

### step 2 宣告 function

```clike=
#include <stdio.h>
#include <math.h>
#include <stdlib.h>
#define students 10

int a[100];

void input();
void graph();

int first();
int save(int );

double average();

long double standard_deviation(double );

int main(void)
{
    double ave;
    
    input();
    
    printf("highest : %d\n", first());
    printf("save : %d\n", save(60));
    
    ave = average();
    
    printf("average : %lf\n", ave);
    printf("sd : %Lf\n", standard_deviation(ave));
    
    graph();
    
    return 0;
}
```

### step 3 把各個 function 的功能補齊

1. input ( )：用來讀取資料。
```clike=
void input()
{
    int i;
    for (i = 0; i < students; i++)
    {
        scanf("%d", &a[i]);
    }
    // return;
}

```
講解：由於不用回傳任何值，所以 [ 回傳值的型別 ] 為 void， void function 可以不用寫 return。

2. first ( )：找誰是全班最高分的同學，並把他的分數傳回去。

```clike=
int first()
{
    int i = 0;
    int highest = a[0];
    for (i = 1; i < students; i++)
    {
        if ( highest < a[i]) highest = a[i];
    }
    return highest;
}
```
講解：先假設 a[0] 是最高的， 然後一個一個去比較，最後再把最高者的分數傳回去。

3. save ( )：判斷有幾個人及格。

```clike=
int save( int grade)
{
    int i = 0, n = 0;
    for (i = 0; i < students; i++)
    {
        if ( a[i] >= grade) n++;
    }
    return n;
}
```
講解： grade 是用來決定及格分數的，只要高於他就算及格。

4. average ( )：算全班平均

```clike=
double average()
{
    int sum = 0, i = 0;
    double ave = 0;
    for (i = 0; i < students; i++)
    {
        sum += a[i];
    }
    
    ave = sum/students;
    return ave;
}
```

5. standard_deviation( double ave)：算標準差。

```clike=
long double standard_deviation( double ave)
{
    long double sd = 0;
    int i = 0;
    for (i = 0; i < students; i++)
    {
        sd +=  pow(a[i]-ave, 2);
    }
    sd /= students;
    sd = sqrt(sd);
    
    return sd;
}
```

6. graph ( )：畫樹狀圖

```clike=
void graph()
{
    int i, j;
    int tree[101] = { 0 };
    for (i = 0; i< students; i++)
    {
        tree[ a[i] ] ++;
    }
    
    for (i = 0; i <= 100; i++)
    {
        printf("%d : ", i);
        for (j = 0; j < tree[i]; j++)
        {
            printf("*");
        }
        printf("\n");
    }
}
```

### 完成後應該會長這樣：

```clike=
#include <stdio.h>
#include <math.h>
#include <stdlib.h>
#define students 10
int a[100];
void input();
int first();
int save(int );
double average();
long double standard_deviation(double );
void graph();
int main(void)
{
    double ave;
    input();
    printf("highest : %d\n", first());
    printf("save : %d\n", save(60));
    ave = average();
    printf("average : %lf\n", ave);
    printf("sd : %Lf\n", standard_deviation(ave));
    graph();
    
    return 0;
}

void input()
{
    int i;
    for (i = 0; i < students; i++)
    {
        scanf("%d", &a[i]);
    }
}

int first()
{
    int i = 0;
    int highest = a[0];
    for (i = 1; i < students; i++)
    {
        if ( highest < a[i]) highest = a[i];
    }
    return highest;
}

int save( int grade)
{
    int i = 0, n = 0;
    for (i = 0; i < students; i++)
    {
        if ( a[i] >= grade) n++;
    }
    return n;
}

double average()
{
    int sum = 0, i = 0;
    double ave = 0;
    for (i = 0; i < students; i++)
    {
        sum += a[i];
    }
    
    ave = sum/students;
    return ave;
}

long double standard_deviation( double ave)
{
    long double sd = 0;
    int i = 0;
    for (i = 0; i < students; i++)
    {
        sd +=  pow(a[i]-ave, 2);
    }
    sd /= students;
    sd = sqrt(sd);
    
    return sd;
}

void graph()
{
    int i, j;
    int tree[101] = { 0 };
    for (i = 0; i< students; i++)
    {
        tree[ a[i] ] ++;
    }
    
    for (i = 0; i <= 100; i++)
    {
        printf("%d : ", i);
        for (j = 0; j < tree[i]; j++)
        {
            printf("*");
        }
        printf("\n");
    }
}
```
