# Pointer
## 用一句來形容 pointer 
指標就是一個專門用來儲存位址的變數，但他的功用卻超乎想像，以下會用幾個例子來說明 pointer 的用處  


## 基本介紹
### 宣告

跟之前宣告變數的方法差不多，只是在宣告指標變數時要加個 * 在裡面  

```c
#include<stdio.h>
int main(void)
{
    int* ptr_int;
    char* ptr_char;
    double* ptr_double;
    
    return 0;
}
```


### 符號介紹
還記得當初在學 scanf()的時候，在填寫變數時前面要加一個 &，其實就是用位址來當作參數。而任何 C 的 function 如果不想靠 return 方式來傳回值，就只能透過位址來取得 function 執行的結果。我們先來探討一下 & 符號，以及如何取得位址的資訊  

- ＆a  ： 取地址
- ＊ptr： 取值
```c
#include<stdio.h>
int main(void)
{
    int a = 7;
    int* ptr;
    ptr = &a;
    
    printf("%d\n", *ptr);
    
    return 0;
}
```
輸出
```
7
```
### %p

專門用來顯示指標變數所儲存的的值(代表某個位址)  
```c
#include<stdio.h>
int main(void)
{
    int a = 88;
    printf("addr = %p\n", &a);
    return 0;
}
```

輸出
```
addr = 0x7fff5fbff6c8
```
 
輸出的 &a 是位址的十六進位值，能夠取得變數的位址是一個很強大的功能，因為這樣一來我們就可以透過位址來存取和修改變數值，而不再只是透過變數名稱來存取變數值。除此之外，我們也可以把位址當參數傳遞，這一點可以幫我們做到很多原本做不到的事情  

### 常見的錯誤

1. 宣告時，要注意

```c
#include<stdio.h>

int main(void)
{
    int* a, b; 
    
    // 錯：這樣宣告代表 
    // int* a; 
    // int b; 
    // b 不是指標變數
    //正確宣告方式：
    
    int* a, * b;
    
    return 0;
}
```

2. 在寫等號時要注意：

```c
#include<stdio.h>

int main(void)
{
    int* a;
    int  b = 6;
    
    a = b;
    
    //要改成： a = &b;
    
    return 0;
}
```

b 為一般變數， a 為指標變數，不能用等號連接  

## 變數的意義

我們之前提過每個 function 會有自己的 local variables，不會和外部衝突，而且參數傳入 function 的時候，是把值 “ 複製 ” 過去，所以在 function 裡任意修改參數並不會影響到外部的變數。下面的範例就是在說明這個特性  

```c
#include<stdio.h>
void function (int a)
{
    a = 99;
    printf("in function:\n");
    printf("addr = %p\n", &a);
    printf("a    = %d\n", a);
    
}

int main(void)
{
    int a = 10;
    
    function(a);
    printf("//---------------------//\n");
    printf("in main:\n");
    printf("addr = %p\n", &a);
    printf("a    = %d\n", a);
    
    return 0;
}
```
輸出
```
in function:

addr = 0x7fff5fbff71c

a    = 99

//---------------------//

in main:

addr = 0x7fff5fbff748

a    = 10
```
這個例子把 a 在 main 跟 function 裡的地址都印出來，你會發現，兩個的位置不一樣，表示，我們把值傳進去 function 時，他並不是把原本的那個變數傳進去，而是把值 **“ 複製 ”** 過去而已。 所以即便我們在 function 裡面更改了 a 的值， 也不會影響到在 main 裡面的 a （畢竟他們兩個根本是不同的 a ，只是剛好名字相同而已）  
  
  
## 位置的重要

當我們在寫程式的時候，每個變數對我們來說具有兩項主要性質：

1. **變數名稱**

2. **變數值**

但當程式經過 compile 後 load 到記憶體裡準備執行時，電腦認知的變數所具有的性質變成：

1. **位址**

2. **變數值**

也就是說電腦是以 **位址** 來區別變數。在許多語言中，位址完全交給電腦負責就好了，但是在 C 語言裡，程式設計者可以用 & 符號來取得位址的資訊。我們就來試試看透過記憶體位址，達到真正交換兩個變數值的效果  

看下面這個例子：  

```c
#include<stdio.h>
void swap_with_number(int a, int b)
{
    int temp = a;
    a = b;
    b = temp;
}
void swap_with_ptr( int* ptr_a, int* ptr_b)
{
    int temp = *ptr_a;
    *ptr_a = *ptr_b;
    *ptr_b = temp;
}
int main( void)
{
    int a = 40;
    int b = 12;
    swap_with_number( a,  b);
    printf("swap with num\n");
    printf("a : %d\n", a);
    printf("b : %d\n", b);
    
    //--------------------------------//
    
    swap_with_ptr(&a, &b);
    printf("swap with ptr\n");
    printf("a : %d\n", a);
    printf("b : %d\n", b);
}
```
輸出
```
swap with num
a : 40
b : 12
swap with ptr
a : 12
b : 40
```
講解：就如同剛剛所說的，對電腦而言，變數的意義在於位置跟變數值，所以，如果我們要交換兩個值了話，只能把他們的位置傳進去 function 裡面，然後進來交換  


> Note : 
> 1. 由於我們要傳進去的參數不是值，而是地址，所以在宣告 function 的 prototype 時，也必須用 int *  
> 
> 2. 在swap_with_ptr 的 function 裡面，我們要交換的不是兩個位置，而是位置裡面的值， 所以不能寫成 ptr_a = ptr_b ， 而是 *ptr_a = *ptr_b  


```c
void swap_with_ptr( int* ptr_a, int* ptr_b)
{
    int temp = *ptr_a;
    *ptr_a = *ptr_b;
    *ptr_b = temp;
}
```

## 指標 與 陣列

### %s

還記得剛剛在講 scanf 的參數是地址這件事嗎，那回想一下，當初在學的時候，有一個東西用 scanf 輸入的時候，不用加 &  

```c
#include<stdio.h>
int main(void)
{
    char a[100];
    scanf("%s", a);
    printf("%s\n", a);
    return 0;
}
```
輸入
```
happy
```
輸出
```
happy
```
沒錯就是讀字串的時候，我們沒有加 &，正確來說，是 array 在宣告時，本身就是指標變數，簡單來說 a 就代表整個 array 得開頭指標

```c
#include<stdio.h>
int main(void)
{
    int a[10] = {999, 1544, 985, 11};
    
    printf("*a = %d\n", *a);
    return 0;
}
```
輸出
```
999
```
同時他也代表 array 中第一個變數( a[ 0 ] )的地址  

### 等價寫法

首先各位要有一個認知， array 在跟記憶體要位置時，是一起要的，所以他們的地址也會連再一起。意即 a[ 0 ] 的地址 +1 就會得到 a[ 1 ] 地址  

如果把它想像成門牌號碼了話會更好懂，a[ 0 ] 的隔壁住 a[ 1 ]，所以我把 a[ 0 ] 的門牌號碼（地址）往後數一個就會得到 a[ 1 ] 的門牌號碼了  

範例：
```c
#include<stdio.h>
int main(void)
{
    int a[10] = {0,1,2,3,4,5,6,7,8,9};
    int i;
    
    for (i = 0; i < 10; i++)
    {
        printf("a[%d] : %p\n", i, &a[i]);
    }
    
    return 0;
}
```
輸出
```
a[0] : 0x7fff5fbff6a0
a[1] : 0x7fff5fbff6a4
a[2] : 0x7fff5fbff6a8
a[3] : 0x7fff5fbff6ac
a[4] : 0x7fff5fbff6b0
a[5] : 0x7fff5fbff6b4
a[6] : 0x7fff5fbff6b8
a[7] : 0x7fff5fbff6bc
a[8] : 0x7fff5fbff6c0
a[9] : 0x7fff5fbff6c4
```

講解：以我的 compiler 而言，每個地址都差 4 bytes


```c
#include<stdio.h>
int main(void)
{
    int a[10] = {0,1,2,3,4,5,6,7,8,9};
    int* ptr = a;
    
    printf("%p : %d\n" ,ptr, *ptr);
    
    ptr++;
    
    printf("%p : %d\n",ptr, *ptr);
    
    return 0;
}
```
輸出
```
0x7fff5fbff6a0 : 0
0x7fff5fbff6a4 : 1
```

講解：指標變數 ptr++ 的意思就是直接跳到下一個人的地址 

接下來，我們來看下面這兩個式子  

```c
a[i]  <--->  *(a+i)
&a[i] <--->  (a+i)
```
在講解時，我們先把變數 i 換成 5

```c
a[5]  <--->  *(a+5)
&a[5] <--->  (a+5)
```

這樣應該會比較好懂。

### 指標 與 二維陣列

雖然我們在宣告二維陣列時是把他當作一個平面的概念去宣告的，但是對電腦而言，他仍然是把他當作一維陣列在儲存的
```c
#include<stdio.h>
int main(void)
{
    int a[3][3];
    int i, j;
    int* ptr = a;
    
    for (i = 0; i < 3; i++)
    {
        for (j = 0; j < 3; j++)
        {
            a[i][j] = i * 3 + j;
        }
    }
    
    for ( ; *ptr < 9; ptr++)
    {
        printf ( "%d : %p\n", *ptr, ptr);
    }
}
```
輸出
```
0 : 0x7fff5fbff720
1 : 0x7fff5fbff724
2 : 0x7fff5fbff728
3 : 0x7fff5fbff72c
4 : 0x7fff5fbff730
5 : 0x7fff5fbff734
6 : 0x7fff5fbff738
7 : 0x7fff5fbff73c
8 : 0x7fff5fbff740
```
在這個例子裡，雖然我們在宣告 array 時，是用二維的方式宣告，但他們的記憶體位置仍然是連再一起的，跟一維陣列一樣


### 指標 與 宣告 function
```c
int function(int a[], int n);
int function(int* a, int n);
```
在寫 function 的 prototype 時， 可以用上面兩種方法來宣告指標變數，兩個方法的意思是一樣的