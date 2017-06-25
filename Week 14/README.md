# Week 14

## qsort
*   [C referecne of qsort](http://en.cppreference.com/w/c/algorithm/qsort)

使用 qsort
`void qsort( void *ptr, size_t count, size_t size, int (*comp)(const void *, const void *) )`
*   *ptr*
    指向要排序的陣列的起始位置的指標
*   *count*
    在陣列中有幾個元素要排序
*   *size*
    在陣列中每個元素的大小，以 byte 為單位
*   *comp*
    比較用的函數，回傳正數代表第一個引數比第二個引數小，回傳負數代表第一個引數比第二個引數大，回傳0代表兩個引數相等

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int compare_int (const void *a, const void *b)
{
    const int *va = (const int *) a;
    const int *vb = (const int *) b;
    return *va-*vb;
}

int compare_double (const void *a, const void *b)
{
    const double *da = (const double *) a;
    const double *db = (const double *) b;
    return (*da > *db) - (*da < *db);
}

int main(void)
{
    int data1[SIZE];
    double data2[SIZE];
    int i;

    for (i=0; i<SIZE; i++) {
        data1[i] = rand()%SIZE;
        data2[i] = (double)rand()/RAND_MAX;
    }

    printf("original: ");
    for (i=0; i<SIZE; i++) {
        printf("%d ", data1[i]);
    }
    printf("\n");

    printf("  sorted: ");
    qsort(data1, SIZE, sizeof(int), compare_int);
    for (i=0; i<SIZE; i++) {
        printf("%d ", data1[i]);
    }
    printf("\n");

    printf("original: ");
    for (i=0; i<SIZE; i++) {
        printf("%.2f ", data2[i]);
    }
    printf("\n");

    printf("  sorted: ");
    qsort(data2, SIZE, sizeof(double), compare_double);
    for (i=0; i<SIZE; i++) {
        printf("%.2f ", data2[i]);
    }
    printf("\n");

    return 0;
}
```

> Note:
> `rand()` 會回傳一個偽亂數，型別是int，其值介於 0 到`RAND_MAX`之間。
> [維基百科對於偽亂數的解釋](https://zh.wikipedia.org/wiki/%E4%BC%AA%E9%9A%8F%E6%9C%BA%E6%80%A7)

## 對固定長度的字元陣列排序
```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int main(void)
{
    char strs[SIZE][4] ={
        "aab", "abc", "aaa", "abb", "acb",
        "aab", "abc", "aaa", "abb", "acb"
    };
    int i;

    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }
    printf("\n");
    qsort(strs, SIZE, 4*sizeof(char), (int (*) (const void *, const void *))strcmp);
    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }

    return 0;
}
```

上面的方法可以正確運作的原因是字串長度固定而且連續地被放置在記憶體中  
`strs[SIZE][4]`這個二維陣列的內容如下表，`strs[0]`對應到`"aab"`，`strs[1]`對應到`"abc"`  
但是其實這樣的二維陣列，在記憶體中仍然是用一維方式循序放置  
table           |   ptr[0]  |   ptr[1]  |   ptr[2]  |   ptr[3]  
----------------|-----------|-----------|-----------|-----------
ptr = strs[0]   |   `'a'`   |   `'a'`   |   `'b'`   |   `'0'`   
ptr = strs[1]   |   `'a'`   |   `'b'`   |   `'c'`   |   `'0'`   
ptr = strs[2]   |   `'a'`   |   `'a'`   |   `'a'`   |   `'0'`   
ptr = strs[3]   |   `'a'`   |   `'b'`   |   `'b'`   |   `'0'`   
......          |           |           |           |           

陣列總共有10個元素，每個陣列的元素包含三個英文字元外加後面跟著一個 '\0' 字元 總共 4 bytes
因此我們可以用 qsort(strs, SIZE, 4*sizeof(char), (int (*) (const void *, const void *))strcmp);
讓 qsort 以陣列元素為基本單位替我們排序 也就是以 4 bytes 為單位進行個別元素比對搬動
最後 strs 陣列的內容會直接被修改 並搬動成由小排到大
qsort 第四個參數要強制型別轉換
讓 strcmp 變成符合型別
原本 strcmp 的型別 int strcmp (const char *s1, const char *s2);
經過型別轉換之後變成 int strcmp (const void *s1, const void *s2);

另一種方式
透過指標陣列
可以用在不同長度的字串排序

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int compare_str_ptr(const void *a, const void *b)
{
    char **pa;
    char **pb;
    pa = (char **) a;
    pb = (char **) b;
    return strcmp(*pa, *pb);
}

int main(void)
{
    char strs[SIZE][4] ={
        "aab", "abc", "aaa", "abb", "acb",
        "aab", "abc", "aaa", "abb", "acb"
    };
    char *ptrs[SIZE];
    int i;

    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }
    printf("\n");


    for (i=0; i<SIZE; i++) {
        ptrs[i] = strs[i];
    }
    qsort(ptrs, SIZE, sizeof(char*), compare_str_ptr);
    for (i=0; i<SIZE; i++) {
        printf("%s\n", ptrs[i]);
    }
    printf("\n");
    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }


    return 0;
}
```

ptrs 是一個指標陣列
因此 ptrs 的每個元素都是一個指標  都可以用來記錄某個記憶體位置
我們先用 ptrs 的每個元素 ptrs[i] 分別記住每個字串的開始位址
for (i=0; i<SIZE; i++) {
    ptrs[i] = strs[i];
}

接下來對指標陣列做 qsort
依照 ptrs 的每個元素所指到的字串大小
將 ptrs 的元素搬動 
所以只是調換指標的順序
實際的資料 strs 不會被更改
請注意這時候 compare 函數的寫法
int compare_str_ptr(const void *a, const void *b)
{
    char * *pa;
    char * *pb;
    pa = (char **) a;
    pb = (char **) b;
    return strcmp(*pa, *pb);
}

請記住下面兩個重點:
1. 被搬動的東西是指標
2. 用來比較則是指標所指到的字串
我們可以換個格式顯示陣列內容
執行底下附的程式碼會輸出
strs: aab0|abc0|aaa0|abb0|acb0|aab0|abc0|aaa0|abb0|acb0|
ptrs: 0028FEE4|0028FEE8|0028FEEC|0028FEF0|0028FEF4|0028FEF8|0028FEFC|0028FF00|0028FF04|0028FF08|
after sorting
ptrs: 0028FF00|0028FEEC|0028FEE4|0028FEF8|0028FEF0|0028FF04|0028FEE8|0028FEFC|0028FF08|0028FEF4|
ptrs: aaa0|aaa0|aab0|aab0|abb0|abb0|abc0|abc0|acb0|acb0|
strs: aab0|abc0|aaa0|abb0|acb0|aab0|abc0|aaa0|abb0|acb0|

對照記憶體位址以及 strs 的內容就會看出端倪

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int compare_str_ptr(const void *a, const void *b)
{
    char **pa;
    char **pb;
    pa = (char **) a;
    pb = (char **) b;
    return strcmp(*pa, *pb);
}

int main(void)
{
    char strs[SIZE][4] ={
        "aab", "abc", "aaa", "abb", "acb",
        "aab", "abc", "aaa", "abb", "acb"
    };
    char *ptrs[SIZE];
    int i;

    printf("strs: ");
    for (i=0; i<SIZE; i++) {
        printf("%s0|", strs[i]);
    }
    printf("\n");

    for (i=0; i<SIZE; i++) {
        ptrs[i] = strs[i];
    }

    printf("ptrs: ");
    for (i=0; i<SIZE; i++) {
        printf("%p|", ptrs[i]);
    }
    printf("\nafter sorting\n");

    qsort(ptrs, SIZE, sizeof(char*), compare_str_ptr);

    printf("ptrs: ");
    for (i=0; i<SIZE; i++) {
        printf("%p|", ptrs[i]);
    }
    printf("\n");

    printf("ptrs: ");
    for (i=0; i<SIZE; i++) {
        printf("%s0|", ptrs[i]);
    }
    printf("\n");

    printf("strs: ");
    for (i=0; i<SIZE; i++) {
        printf("%s0|", strs[i]);
    }

    return 0;
}
```

### Pointers to Functions

```C
#include <stdio.h>
#include <stdlib.h>
int sum(int a[], int n)
{
    int i, ans = 0;
    for (i=0; i<n; i++) {
        ans += a[i];
    }
    return ans;
}
int sum_squared(int a[], int n)
{
    int i, ans = 0;
    for (i=0; i<n; i++) {
        ans += a[i]*a[i];
    }
    return ans;
}

int middle(int a[], int n)
{
    return a[n/2];
}

int run (  int  (*fp) (int *, int )  , int a[], int n)
{
    return fp(a, n);
}

int main(void)
{
    int a[] = {1, 2, 3, 4};
    printf("%d\n", run(sum, a, 4));
    printf("%d\n", run(sum_squared, a, 4));
    printf("%d\n", run(middle, a, 4));
    return 0;
}
```

### 在程式執行期間取得記憶體

```C
/* E10_15.c */
#include <stdio.h>
#include <stdlib.h>
int main(void)
{
   double *ptd;
   int array_size, i;

   printf("How many doubles do you want? ");
   scanf("%d", &array_size);
   ptd = (double *) malloc(array_size * sizeof (double));
   if (ptd == NULL) {
      printf("Memory allocation failed.\n");
      exit(EXIT_FAILURE);
   }

   for (i = 0; i < array_size; i++) {
      ptd[i] = (double) rand() / RAND_MAX;
   }
   for (i = 0; i < array_size; i++) {
      if (*(ptd+i) > 0.5)
         printf("%d: %f\n", i, ptd[i]);
   }

   free(ptd);

   return 0;
}
```

```C
/* W10_09.c */
#include <stdio.h>
int main(void)
{
   int *a, **b, i, j, rows, cols;
   
   scanf("%d %d", &rows, &cols);
   a = (int *) malloc(rows * cols * sizeof(int));
   for (i = 0; i < rows*cols; i++) a[i] = i;
   b = (int* *) malloc(rows * sizeof(int *));

   for (i = 0 ; i < rows; i++) b[i] = &a[i*cols];


   for (i = 0; i < rows; i++) {
      for (j = 0; j < cols; j++) {
         printf("%3d ", b[i][j]); 
      }
      printf("\n");
   }
   
   free(b);
   free(a);

   return 0;
}
```

memory leak

dangling pointer

字元陣列和字串

Reference:
https://stackoverflow.com/questions/2245664/what-is-the-type-of-string-literals-in-c-and-c
http://en.cppreference.com/w/c/language/string_literal
http://en.cppreference.com/w/cpp/language/string_literal

簡要來說，對於C語言：
1.  string literal的型別是char[]
2.  如果是用指標去指向某個string literal，那麼其指向的內容是不可寫的
    `char* str = "Test"; str[0] = 't'; // Undefined behavior`
3.  為了避免類似錯誤發生，通常會建議用：
    *  `const char* str = "string"; // 如果寫出 str[0]='t'，在編譯時就會出錯`
    *  `char str[] = "string"; // str[0]='S' 是可以的`

```
#include <stdio.h>

int main(void)
{

    char *str1[] = {"piece", "of", "cake"};

    char str2[][8] = {"piece", "of", "cake"};

    int i, j;

    for (i=0; i<3; i++) {

        for (j=0; j<8; j++)

            printf("%c", str1[i][j]);

        printf("\n");

    }

    for (i=0; i<3; i++) {

        for (j=0; j<8; j++)

            printf("%c", str2[i][j]);

        printf("\n");

    }

    return 0; 

}
```

### 兩個星號

```C
#include <stdio.h>
#include <stdlib.h>

void malloc_float2( float * * p , unsigned int sz)
{
    *p = (float *) malloc(sz*sizeof(float));
}

int main(void)
{
    float * ptr = NULL;
    int i;
    int n = 100;

    malloc_float2(&ptr, n);

    for (i=0; i<n; i++)
        ptr[i] = (float) rand()/RAND_MAX;

    free(ptr);

    return 0;
}
```

### C Structures

```C
struct t_point {
    int x;
    int y;
};
typedef struct t_point Point;
```

上面的寫法
也可以合併成

```C
typedef struct {
    int x;
    int y;
} Point;
```

有了 Point 這個 struct 型別之後
就能用它產生變數
例如
Point pt = {5, 7};
Point *pp;

pp = &pt;
(*pp).x = 10;
pp->x = 10;

底下是一個較完整的範例

```C
#include <stdio.h>
#include <stdlib.h>
/* 定義一個新的型別 */
/* 取名叫做 Point */
/* 裡面包含 x 和 y 兩個 members */ 
/* 定義過之後   Point 可以被拿來當作一般的型別來使用 */
/* 包括宣告新的變數   或是宣告 function */ 
typedef struct {
    int x;
    int y;
} Point;

/* ones_vec_1() 會傳回某個 Point 結構的位址 */
/* 這個位址是由 Point 結構組成的陣列的開頭位址 */ 
Point * ones_vec_1(int length);

void ones_vec_2(int length, Point **bp);

int main(void)
{
   Point *a, *b;
   int i, length;
   
   printf("The size of a Point is %u bytes.\n", sizeof(Point));
    
   printf("vector length: ");
   scanf("%d", &length);

   /* 利用 ones_vec_1 取得一個陣列 */
   /* 陣列的每個元素是一個 Point */
   /* 陣列的開頭位址記錄在指標變數 a 裡面 */    
   a = ones_vec_1(length);
   ones_vec_2(length, &b);

   /* a 是個指標變數   它記錄的是某個陣列的開頭位址 */
   /* 陣列的每個元素是 a[i] (是個 Point) */
   /* 所以有兩個 members  分別是 a[i].x 和 a[i].y */   
   for (i=0; i<length; i++) 
      printf("(%d, %d) ", a[i].x, a[i].y);
   printf("\n");
   
   for (i=0; i<length; i++)
      printf("(%d, %d) ", b[i].x, b[i].y);
   printf("\n");
   
   
   return 0;
}

Point * ones_vec_1(int length)
{
   Point *a;
   int i;
   a = (Point *) malloc(length * sizeof(Point));
   for (i = 0 ; i < length; i++) {
      a[i].x = 1;
      a[i].y = 1;
   } 
   return a;
}

void ones_vec_2(int length, Point **bp)
{
   int i;
   
   *bp = (Point *) malloc(length * sizeof(Point));
   for (i = 0 ; i < length; i++) {
      (*bp)[i].x = 1;
      (*bp)[i].y = 1;      
   } 
}
```

```C
#include <stdio.h>
#include <stdlib.h>
typedef struct t_complex {
    double r;
    double i;
} Complex;

void add(Complex a, Complex b, Complex *t)
{
    t->r = a.r+b.r;
    t->i = a.i+b.i;
}
void set_complex(Complex *p, double r, double i)
{
    p->r = r;
    p->i = i;
}
void show_complex(Complex t)
{
    printf("%.2f%+.2fi\n", t.r, t.i);
}

int main(void)
{
    Complex x, y, z;
    set_complex(&x, 1, 2);
    set_complex(&y, 2, -3);
    add(x, y, &z);
    show_complex(z);

    return 0;
}
```