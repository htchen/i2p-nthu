# Week 15

## 位元運算
[C reference 對於算數運算子的說明](http://en.cppreference.com/w/c/language/operator_arithmetic)  

C 語言提供了一些 bitwise operators，讓我們能用更低階的方式去存取和修改資料  
*   `~` (bitwise NOT)  
    將每個 bit 的值反轉  
    ```C
    unsigned int a = ~0;
    // 將 a 用二進位表示出來，會得到所有 bit 都是 1
    ```  
*   `&` (bitwise AND)  
    將兩個運算元對應的 bit 進行 AND 運算  
    ```C
    // 假設 char 是 8 bit
    unsigned char a = 190; // 1011 1110
    unsigned char b = 162; // 1010 0010
    unsigned char c = a&b; // 1010 0010 = 162
    ```  
*   `|` (bitwise OR)  
    將兩個運算元對應的 bit 進行 OR 運算  
    ```C
    // 假設 char 是 8 bit
    unsigned char a = 190; // 1011 1110
    unsigned char b = 162; // 1010 0010
    unsigned char c = a|b; // 1011 1110 = 190
    ```  
*   `^` (bitwise XOR)  
    將兩個運算元對應的 bit 進行 XOR 運算  
    ```C
    // 假設 char 是 8 bit
    unsigned char a = 190; // 1011 1110
    unsigned char b = 162; // 1010 0010
    unsigned char c = a^b; // 0001 1100 = 28
    ```  
*   `<<` (left shift)  
    ```C
    // 假設 char 是 8 bit
    unsigned char x = 5; // 0000 0101 = 5
    unsigned char y = x << 3; // 0010 1000 = 40 = 5 * (2^3)
    ```  
*   `>>` (right shift)  
    ```C
    // 假設 char 是 8 bit
    unsigned char x = 59; // 0011 1011 = 59
    unsigned char y = x >> 1; // 0001 1101 = 29 = 59 / (2^1)
    ```  

> Note:  
> 對於 shift operators 其實要注意左右邊運算元的正負號  
> 可以參考開頭的網址了解更多細節，以及整數提升的規則  

有時候我們需要儲存的資訊可能只有 0 和 1 兩種值，譬如記錄某種狀態的有或無  
當我們需要記錄大量這一類的資料時，若每個狀態都使用`int`來記錄會太浪費空間  
這種情況就適合使用位元運算，就可以大幅地節省空間  

譬如一個集合中包含 100、200、300 三種數字  
可以把不同數字的有無當成是一個狀態，藉此總共有 2^3 = 8 種子集合  
底下的程式碼可以產生所有可能的組合(不包含空集合)  

輸入
```
3
100 200 300
```
輸出
```
100
200
100 200
300
100 300
200 300
100 200 300
```

```C
#include <stdio.h>

int mask[16] = {
    0x00001, 0x00002, 0x00004, 0x00008,
    0x00010, 0x00020, 0x00040, 0x00080,
    0x00100, 0x00200, 0x00400, 0x00800,
    0x01000, 0x02000, 0x04000, 0x08000
};

int main(void)
{
    int N, i, j, lim;
    int num[16];

    scanf("%d", &N);
    for (i=0; i<N; i++) {
        scanf("%d", &num[i]);
    }
    lim = 1<<N;
    for (i=0; i<lim; i++) {
        for (j=0; j<N; j++)  {
            if ((i&mask[j])!=0) {
                printf("%d ", num[j]);
            }
        }
        printf("\n");
    }

    return 0;
}
```

可以進一步將`mask`使用`1 << N`的方式動態產生

```C
#include <stdio.h>
int main(void)
{
    int N, i, j, lim;
    int num[16];

    scanf("%d", &N);
    for (i=0; i<N; i++) {
        scanf("%d", &num[i]);
    }

    lim = 1<<N;

    for (i=0; i<lim; i++) {
        for (j=0; j<N; j++)  {
            if ( (i & (1<<j))!= 0 ) {
                printf("%d ", num[j]);
            }
        }
        printf("\n");
    }
    return 0;
}
```

以下是實務中可能會用到的例子，藉由一個夠大的型別來儲存多種屬性

```
Student 0: NTHU CS Freshman 
Student 1: NTHU Freshman 
Student 2: NHCUE Freshman 
Student 3: CS 

After one year

Student 0: NTHU CS 
Student 1: NTHU 
Student 2: NHCUE 
Student 3: CS 

After merging

Student 0: NTHU CS 
Student 1: NTHU 
Student 2: NTHU 
Student 3: CS 
```

```C
#include <stddef.h>
#include <stdio.h>
#include <stdlib.h>

#define BIT(n) (1<<(n))

#define NTHU_STUDENT BIT(0)
#define NHCUE_STUDENT BIT(1)
#define CS_MAJOR BIT(2)
#define FRESHMAN BIT(3)

void Print(unsigned int* students, size_t length)
{
	size_t i;
	for (i = 0; i < length; i++)
	{
		printf("Student %d: ", i);

		if (students[i] & NTHU_STUDENT)
			printf("NTHU ");

		if (students[i] & NHCUE_STUDENT)
			printf("NHCUE ");

		if (students[i] & CS_MAJOR)
			printf("CS ");

		if (students[i] & FRESHMAN)
			printf("Freshman ");

		printf("\n");
	}
}

int main()
{
	unsigned int a = NTHU_STUDENT | CS_MAJOR | FRESHMAN;
	unsigned int b = NTHU_STUDENT | FRESHMAN;
	unsigned int c = NHCUE_STUDENT | FRESHMAN;;
	unsigned int d = CS_MAJOR;

	unsigned int students[] = { a,b,c,d };
	size_t length = sizeof(students) / sizeof(students[0]);
	size_t i;
	
	Print(students, length);

	printf("\nAfter one year\n\n");

	for (i = 0; i < length; i++) {
		students[i] &= ~FRESHMAN;
	}

	Print(students, length);

	printf("\nAfter merging\n\n");
	
	for (i = 0; i < length; i++) {
		if (students[i] & NHCUE_STUDENT) {
			students[i] &= ~NHCUE_STUDENT;
			students[i] |= NTHU_STUDENT;
		}
	}

	Print(students, length);

	return 0;
}
```

## 更多的位元運算

下例的程式碼會回傳從右邊數來第 p 個 bit 開始，取出 n 個 bit  
```C
unsigned getBits(unsigned x, int p, int n)
{
   return ( x >> (p-n) ) & ~( ~0 << n );  /* 取出 x 的第 p 位置起 n 個 bits */
}
```

以`getBits(100, 6, 4)`為例，會從右邊數來第 6 個 bit 開始，取出 4 個 bit  
十進位 100 用二進位表示為，粗體是要取出的 bits  
0000 0000 0000 0000 0000 0000 01**10** **01**00  

`~( ~0 << n )`相當於：  
`~0` : 1111 1111 1111 1111 1111 1111 1111 1111  
`<< n` : 1111 1111 1111 1111 1111 1111 1111 0000  
`~`: 0000 0000 0000 0000 0000 0000 0000 1111  
這個結果會用來當作mask  

` x >> (p-n)`相當於：  
`>> (p-n)` : 0000 0000 0000 0000 0000 0000 0001 **1001**  
先把右邊不要的部分透過位移運算移除  

最後將兩個結果進行 bitwise AND 運算：  
0000 0000 0000 0000 0000 0000 0000 1111 &  
0000 0000 0000 0000 0000 0000 0001 **1001**  

0000 0000 0000 0000 0000 0000 0000 **1001**  

下例的程式碼是用來把 x 第 p 位置起 n 個 bits 由 0 變 1，1 則變為 0  
```C
unsigned invert( unsigned x, int p, int n )
{
   return  x ^ (~(~0 << n) << (p-n));
}
```

想辦法產生一個 mask，能從 p 位置後接 n 個 1，其餘位置都是零  
這樣的 mask 可以用`~(~0 << n) << (p-n)`產生  
接著將 mask 和 x 做 bitwise XOR 就可得到`invert`要求的效果  

XOR 的運算性質：  
a ^ 0 = a  
a ^ 1 = ~a  

下例的程式碼是用來傳回 x 向右 rotate n bits 之後的結果  
```C
unsigned rightRotate(unsigned x, int n)
{
   return ((x & ~(~0 << n)) << (sizeof(x)*8 - n)) | (x >> n) ;
}
```

首先用`(x & ~(~0 << n))`取出最右邊 n 個 bits，然後向左位移`(sizeof(x)*8 - n)`bits  
接著`(x >> n)`把 x 向右位移 n bits，把兩個結果利用 OR 運算就可以做出向右 rotate 的效果  

> Note:  
> 以上範例均是假設`int`為 32 位元  

## C Structures

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

```C
Point pt = {5, 7};
Point *pp;
pp = &pt;
(*pp).x = 10;
pp->x = 10;
```

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

```C
#include <stdio.h>
#include <stdlib.h>
typedef struct t_data {
    int size;
    int * ptr;
} DATA;
void create_data(DATA *z, int sz)
{
    int * t;
    int i;
    z->size = sz;
    z->ptr = (int *) malloc(sz*sizeof(int));
    t = z->ptr;
    for (i=0; i< sz; i++)  {
        t[i] = i;
    }
}
void show_data(DATA d)
{
    int i;
    printf("%u\n", sizeof(d));
    for (i=0; i<d.size; i++) {
        printf("%d ", (d.ptr)[i]);
        if ((i+1)%10 == 0) printf("\n");
    }
}
void delete_data(DATA *z)
{
    free(z->ptr);
    z->ptr = NULL;
    z->size = 0;
}
DATA clone_data(DATA x)
{
    int i;
    DATA y;
    y.size = x.size;
    y.ptr = (int *) malloc(y.size*sizeof(int));
    for (i=0; i<y.size; i++) {
        (y.ptr)[i] = (x.ptr)[i];
    }
    return y;
}

int main(void)
{
    DATA x , x_clone;
    create_data(&x, 100);
    show_data(x);
    x_clone = clone_data(x);
    delete_data(&x);
    show_data(x_clone);

    return 0;
}
```

```C
#include <stdio.h>
#include <stdlib.h>
typedef struct _node {
    int x;
    struct _node *next;
} Node;

int main(void)
{
    Node head;

    head.x = 0;
    head.next = NULL;

    head.next = (Node *) malloc (sizeof(Node));

    (head.next)->x = 1;
    (head.next)->next = NULL;

    printf("%d %d\n", head.x, (head.next)->x);

    free(head.next);

    return 0;
}
```

```C
#include <stdio.h>
#include <stdlib.h>
typedef struct _node {
    int x;
    struct _node *next;
} Node;
/*

head|x|next|
        |
        V
        |x|next|
             |
             V
        np-> |x|next|

              */
int main(void)
{
    Node head;
    Node *np, *nq;
    int i;

    head.x = 0;
    head.next = (Node *) malloc(sizeof(Node));
    np = head.next;
    i = 1;
    while (i < 10) {
        np->x = i;
        np->next = (Node *) malloc(sizeof(Node));
        np = np->next;
        i++;
    }
    np->x = i;
    np->next = NULL;
    np = head.next;
    while (np!=NULL) {
        printf("%d\n", np->x);
        np = np->next;
    }

    np = head.next;
    while (np!=NULL) {
        nq = np;
        np = np->next;
        free(nq);
    }

    return 0;
}
```
