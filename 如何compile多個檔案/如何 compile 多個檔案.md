# 如何 compile 多個檔案
到目前為止，我們寫的程式都是把所有的 functions 放在同一個檔案裡，這樣只要 compile 那個檔案就可產生最後我們要的執行檔。但是其實 compiler 允許我們把程式碼 (以 function 為基本單位) 分散在不同檔案裡，然後再個別 compile 並且 link 產生一個執行檔。如果我們把程式碼分散在多個檔案中。

在往後 online judge 的題目中，也常常用到這個技巧。
舉例：http://acm.cs.nthu.edu.tw/problem/10947/
你會發現上面那題裡，下面有附一個 .c 檔和一個 .h 檔，然後要你上傳另一個 .c 檔。

## header file

由於 function 使用前要先宣告 prototype，所以必須在每個分散的檔案裡面都把要用到的 function prototype 包含進來。常見的作法是把這些宣告都放在一個 header file 裡面 (副檔名 .h)，然後用 #include 的方式引入原始檔中。除了 function prototype 之外，用 #define 方式定義的常數也可以放在 header file 裡。




## 舉例

題目：Given a integer number N, print out all the primes less than N

In this problem, your ONLY need to implement a function called isPrime(int n).

It returns 1 if the number is prime, otherwise returns 0.

reference ：http://acm.cs.nthu.edu.tw/problem/11182/

1. 先儲存下面 code 為 code.c。

```clike=
#include <stdio.h>
#include "function.h"
int main()
{
	int N, i;
	scanf("%d", &N);
	for(i = 0; i < N; i++)
		if(1 == isPrime(i) ) printf("%d\n", i);
}
```

2. 儲存下面 code 為 function.h。

```clike=
int isPrime(int n);
```

3. 儲存下面 code 為 function.c。

```clike=
#include "function.h"
int isPrime(int n)
{
    if (n == 2 || n == 3 || n == 5 || n == 7) return 1;
    if (n == 1) return 0;
    if (n % 6 != 1 && n % 6 != 5) return 0;
    
    int i, flag = 1;
    
    for (i = 3; i * i <= n && flag == 1; i = i+2) 
        flag = n % i; 
        // 如果flag = 0 了話，表示整除，即不是質數
        
    if (flag == 1) return 1;
    return 0;
}
```
質數 reference ：http://www.csie.ntnu.edu.tw/~u91029/Prime.html

先講解一下這個程式碼：
1. 先把小於 10 的質數列舉出來。
2. 由於 1 不是質數，所以回傳 0。
3. 接下來把剩下的數分成 6n, 6n+1,6n+2, 6n+3, 6n+4, 6n+5 這六種，你會發現 6n, 6n+2, 6n+3, 6n+4 這些都不是質數，所以，只要 n 對 6 取餘數的結果不是 1 or 5，那肯定不是質數。
4. 剩下的數字用基本檢查法求出。

- i 從 3 開始的原因在於 1 跟 2 都不用考慮。
- i = i + 2 是因為每兩個數就有一個是偶數（可被 2 整除)
- i * i <= n ：reference ：https://zh.wikipedia.org/wiki/素性测试

## compile
### 方法一
直接在 command line 打

```clike=
gcc code.c function.c function.h
```
然後目錄下會產生一個我們要的執行檔，檔名是 a.exe，在 mac了話，則會產生一個 a.out 檔。
![](https://i.imgur.com/RYp1bOW.png)

### 方法二
在程式開發軟體中，你可以將其他的 .c or .h 檔加入你的 project 中，然後compile 即可。

以 xcode 為例：

將檔案 add 進來：
![](https://i.imgur.com/qWnGlUs.png)

![](https://i.imgur.com/n8SOL8u.png)

更快的方法：你可以直接拖曳進去test裡面：

![](https://i.imgur.com/Hg7hs47.png)

![](https://i.imgur.com/c0EcqHX.png)

接著直接 compile 即可。

