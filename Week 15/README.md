# Week 15

## 位元運算

請看講義附錄

位元運算
& AND
| OR
^ XOR
~ NOT
>> RIGHT SHIFT
<< LEFT SHIFT

應用
產生子集合
譬如集合中包含 100 200 300 三個數字
底下的程式碼可以產生所有可能的組合

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

精簡版
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
