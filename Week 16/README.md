# Week 16

## 關於 static 與 extern
[Stack Overflow 關於這部分的說明](https://stackoverflow.com/questions/95890/what-is-a-variables-linkage-and-storage-specifier)  

*   **static**  
    函數或是全域變數前面如果加`static`  
    表示這個函數不會被 linker 看到  
    因此不會和其他檔案連結  

    區域變數前面如果加`static`  
    表示這個變數從程式開始執行就會存在  
    直到程式結束才會消失  

*   **extern**  
    只在一個原始碼檔案中定義  
    
    ```C
    int x = 0;
    ```
    
    其他原始碼檔案如果要使用`x`，應該宣告成  
    
    ```C
    extern int x;
    ```
    
    > Note:  
    > 可以參考底下的資料，進一步了解對於變數來說甚麼時候需要`extern`  
    > [Stack Overflow 關於 Global Variable in Header File 的問答](https://stackoverflow.com/questions/8108634/global-variables-in-header-file)  

## 資料處理
*   [qsort](http://www.gnu.org/software/libc/manual/html_node/Array-Sort-Function.html#Array-Sort-Function)
*   [fgets](http://www.cplusplus.com/reference/cstdio/fgets/)

讀取`imdb_top250.txt`檔案，裡面包含了 250 筆電影資料  
每一筆資料包含四個項目，分別是平均評分、電影名稱、上映年份、參與評分的網友數目  

輸入中的每一筆資料之間用空行隔開  

```
9.2
The Shawshank Redemption
1994
885806

9.2
The Godfather
1972
641587
```

並利用底下的 structure 儲存  

```C
struct t_movie {
    double rating;
    char name[64];
    int year;
    int reviews;
};
typedef struct t_movie Movie;
```

且產生一個 250 個元素的陣列，儲存 250 筆電影資料  

```C
Movie top[250];
```

利用`qsort`將資料以底下的原則重新排列：  
1.  依照電影名稱的英文字母順序
2.  依照上映年份排序，從早期到近期，如果年份相同，則再依照電影名稱的英文字母順序排序
3.  依照平均評分的高低排序，由高到低，如果評分相同，則再依照參與評分的網友數量排序

將以上三點的的排序結果分別輸出為`sort1.txt`、`sort2.txt`、`sort3.txt`

範例程式碼  

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct t_movie {
    double rating;
    char name[64];
    int year;
    int reviews;
};
typedef struct t_movie Movie;
Movie movies[300];

int cmp1(const void *a, const void *b)
{
    Movie *s, *t;
    s = (Movie*) a;
    t = (Movie*) b;
    return strcmp(s->name, t->name);
}
int cmp2(const void *a, const void *b)
{
    Movie *s, *t;
    s = (Movie*) a;
    t = (Movie*) b;
    if (s->year > t->year) return 1;
    else if (s->year < t->year) return -1;
    else
         return strcmp(s->name, t->name);
}
int cmp3(const void *a, const void *b)
{
    Movie *s, *t;
    s = (Movie*) a;
    t = (Movie*) b;
    if (s->rating > t->rating) return -1;
    else if (s->rating < t->rating) return 1;
    else {
        if (s->reviews > t->reviews) return -1;
        else if (s->reviews < t->reviews) return 1;
        else return 0;
    }
}

void write(char * fname, Movie *mvs, int NM)
{
    int i;
    FILE *fout;
    fout = fopen(fname, "w");
    for (i=0; i<NM; i++) {
        fprintf(fout, "%3d: %f\t%s\t(%d)\t%d\n",
               i+1, mvs[i].rating, mvs[i].name, mvs[i].year, mvs[i].reviews);
    }
    fclose(fout);
}

int main(void)
{
    int NM;
    FILE *fin;

    char line[255];
    fin = fopen("imdb_top250.txt", "r");

    NM = 0;
    while (!feof(fin) ) {
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].rating = atof(line);
        if (fgets(line, 255, fin)==NULL) break;
        strcpy(movies[NM].name, line);
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].year = atoi(line);
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].reviews = atoi(line);
        NM++;
        if (fgets(line, 255, fin)==NULL) break;
    }
    fclose(fin);
/*
    for (i=0; i<NM; i++) {
        printf("%3d: %f\t%s\t(%d)\t%d\n",
               i+1, movies[i].rating, movies[i].name, movies[i].year, movies[i].reviews);
    }
*/
    qsort(movies, NM, sizeof(Movie), cmp1);
    write("sort1.txt", movies, NM);

    qsort(movies, NM, sizeof(Movie), cmp2);
    write("sort2.txt", movies, NM);

    qsort(movies, NM, sizeof(Movie), cmp3);
    write("sort3.txt", movies, NM);


    return 0;
}
```

另一種透過指標陣列的寫法  

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct t_movie {
    double rating;
    char name[64];
    int year;
    int reviews;
};
typedef struct t_movie Movie;
Movie movies[300];

int cmp1(const void *a, const void *b)
{
    Movie *s, *t;
    s = * (Movie**) a;
    t = * (Movie**) b;
    return strcmp(s->name, t->name);
}
int cmp2(const void *a, const void *b)
{
    Movie *s, *t;
    s = * (Movie**) a;
    t = * (Movie**) b;
    if (s->year > t->year) return 1;
    else if (s->year < t->year) return -1;
    else
         return strcmp(s->name, t->name);
}
int cmp3(const void *a, const void *b)
{
    Movie *s, *t;
    s = * (Movie**) a;
    t = * (Movie**) b;
    if (s->rating > t->rating) return -1;
    else if (s->rating < t->rating) return 1;
    else {
        if (s->reviews > t->reviews) return -1;
        else if (s->reviews < t->reviews) return 1;
        else return 0;
    }
}

void write(char * fname, Movie * pmvs[], int NM)
{
    int i;
    FILE *fout;
    fout = fopen(fname, "w");
    for (i=0; i<NM; i++) {
        fprintf(fout, "%3d: %f\t%s\t(%d)\t%d\n",
               i+1, pmvs[i]->rating, pmvs[i]->name, pmvs[i]->year, pmvs[i]->reviews);
    }
    fclose(fout);
}

int main(void)
{
    int NM;
    FILE *fin;

    char line[255];
    fin = fopen("imdb_top250.txt", "r");

    Movie *pmovies[300];
    int i;
    for (i=0; i<300; i++) pmovies[i] = &movies[i];

    NM = 0;
    while (!feof(fin) ) {
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].rating = atof(line);
        if (fgets(line, 255, fin)==NULL) break;
        line[strlen(line)-1] = '\0';
        strcpy(movies[NM].name, line);
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].year = atoi(line);
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].reviews = atoi(line);
        NM++;
        if (fgets(line, 255, fin)==NULL) break;
    }
    fclose(fin);


    qsort(pmovies, NM, sizeof(Movie*), cmp1);
    write("sort1.txt", pmovies, NM);

    qsort(pmovies, NM, sizeof(Movie*), cmp2);
    write("sort2.txt", pmovies, NM);

    qsort(pmovies, NM, sizeof(Movie*), cmp3);
    write("sort3.txt", pmovies, NM);


    return 0;
}
```

## 查字典與自動完成

利用附件`words.txt`  

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
enum {
    MAX_LEN = 60,
    NUM_WORDS = 120000
};

int lookup(char word[], char *dict[], int nwords);

int main(void)
{
    char **p, buf[MAX_LEN + 1];
    int i, j;
    FILE *fin;

    fin = fopen("words.txt", "r");
    p = (char **) malloc(sizeof(char *)* NUM_WORDS);
    i = 0;
    while(i< NUM_WORDS && (fgets(buf, MAX_LEN + 1, fin) != NULL)) {
        buf[strlen(buf)-1] =  '\0';
        p[i] = malloc(strlen(buf)+1);
        if (p[i] != NULL) {
            strcpy(p[i], buf);
            i++;
        }
    }
    fclose(fin);

    while(fgets(buf, MAX_LEN + 1, stdin) != NULL) {
        buf[strlen(buf)-1] = '\0';
        j = lookup(buf, p, i);
        while (j>=0) {
            if (strncmp(p[j], buf, strlen(buf))!=0) {
                j++;
                break;
            }
            j--;
        }
        if (j<0) j = 0;
        while (j<i) {
            if (strncmp(p[j], buf, strlen(buf))!=0)
                break;
            printf("%s\n", p[j]);
            j++;
        }
    }

    for (j=0; j<i; j++) {
        free(p[j]);
    }
    free(p);

    return 0;
}

int lookup(char *word, char *dict[], int nwords)
{
    int  low, high, mid, cmp;
    low = mid = 0;
    high = nwords - 1;
    while (low <= high) {
        mid = low + (high-low)/2;
        cmp = strcmp(word, dict[mid]);
        if (cmp < 0)
            high = mid - 1;
        else if (cmp > 0)
            low = mid + 1;
        else
            break;
    }
    return mid;
}
```

## 實作 Doubly Linked List
[維基百科對於 doubly linked list 的說明](http://en.wikipedia.org/wiki/Doubly_linked_list)

底下是框架，可以試著實作那些尚未被完成的功能  

```C
##include <stdio.h>
#include <stdlib.h>

struct dl_node {
    int data;
    struct dl_node *prev;
    struct dl_node *next;
};
typedef struct dl_node DL_Node;

struct dl_list {
    DL_Node *firstNode;
    DL_Node *lastNode;
};
typedef struct dl_list DL_List;

void insertAfter(DL_List *list, DL_Node *node, DL_Node *newNode)
{

}

void insertBefore(DL_List *list, DL_Node *node, DL_Node *newNode)
{

}

void insertBeginning(DL_List *list, DL_Node *newNode)
{

}

void insertEnd(DL_List *list, DL_Node *newNode)
{

}

DL_Node *createNewNode(int data)
{
    DL_Node *p;
    p = (DL_Node*) malloc(sizeof(DL_Node));
    p->data = data;
    p->prev = NULL;
    p->next = NULL;
    return p;
}

void showList(DL_List *list)
{
    DL_Node *p = list->firstNode;
    while (p != NULL) {
        printf("%d->", p->data);
        p = p->next;
    }
    printf("NULL\n");
}
void showListReverse(DL_List *list)
{

}
void removeNode(DL_List *list, DL_Node *node)
{

}
void freeList(DL_List *list)
{
    DL_Node *p = list->firstNode;
    while (p != NULL) {
        p = p->next;
        if (p!=NULL)
            free(p->prev);
        else
            free(list->lastNode);
    }
    free(list);
}
int main(void)
{
    DL_List *dll = NULL;

    dll = (DL_List*) malloc(sizeof(DL_List));

    dll->firstNode = NULL;
    dll->lastNode = NULL;

    insertBeginning(dll, createNewNode(14));
    insertBeginning(dll, createNewNode(13));
    insertBeginning(dll, createNewNode(12));
    insertBeginning(dll, createNewNode(11));
    insertBeginning(dll, createNewNode(10));

    insertEnd(dll, createNewNode(20));
    insertEnd(dll, createNewNode(21));
    insertEnd(dll, createNewNode(22));
    insertEnd(dll, createNewNode(23));
    insertEnd(dll, createNewNode(24));

    showList(dll);
    showListReverse(dll);

    removeNode(dll, dll->firstNode);
    removeNode(dll, dll->lastNode);
    showList(dll);

    freeList(dll);

    return 0;
}
```

執行之後應該要得到  

```
10->11->12->13->14->20->21->22->23->24->NULL
24->23->22->21->20->14->13->12->11->10->NULL
11->12->13->14->20->21->22->23->NULL
```

圖示搭配程式碼可以參考`dll.pptx`

底下是完整的程式碼  

```C
#include <stdio.h>
#include <stdlib.h>

struct dl_node {
    int data;
    struct dl_node *prev;
    struct dl_node *next;
};
typedef struct dl_node DL_Node;

struct dl_list {
    DL_Node *firstNode;
    DL_Node *lastNode;
};
typedef struct dl_list DL_List;

void insertAfter(DL_List *list, DL_Node *node, DL_Node *newNode)
{
    newNode->prev = node;
    newNode->next = node->next;
    if (node->next == NULL) {
        list->lastNode = newNode;
    } else {
        node->next->prev = newNode;
    }
    node->next = newNode;
}

void insertBefore(DL_List *list, DL_Node *node, DL_Node *newNode)
{
    newNode->prev = node->prev;
    newNode->next = node;
    if (node->prev == NULL) {
        list->firstNode = newNode;
    } else {
        node->prev->next = newNode;
    }
    node->prev = newNode;
}

void insertBeginning(DL_List *list, DL_Node *newNode)
{
    if (list->firstNode == NULL) {
        list->firstNode = newNode;
        list->lastNode = newNode;
        newNode->prev = NULL;
        newNode->next = NULL;
    } else {
        insertBefore(list, list->firstNode, newNode);
    }
}

void insertEnd(DL_List *list, DL_Node *newNode)
{
    if (list->lastNode == NULL) {
        insertBeginning(list, newNode);
    } else {
        insertAfter(list, list->lastNode, newNode);
    }
}

DL_Node *createNewNode(int data)
{
    DL_Node *p;
    p = (DL_Node*) malloc(sizeof(DL_Node));
    p->data = data;
    p->prev = NULL;
    p->next = NULL;
    return p;
}

void showList(DL_List *list)
{
    DL_Node *p = list->firstNode;
    while (p != NULL) {
        printf("%d->", p->data);
        p = p->next;
    }
    printf("NULL\n");
}

void showListReverse(DL_List *list)
{
    DL_Node *p = list->lastNode;
    while (p != NULL) {
        printf("%d->", p->data);
        p = p->prev;
    }
    printf("NULL\n");
}

void removeNode(DL_List *list, DL_Node *node)
{
    if (node->prev == NULL) {
        list->firstNode = node->next;
    } else {
        node->prev->next = node->next;
    }
    if (node->next == NULL) {
        list->lastNode = node->prev;
    } else {
        node->next->prev = node->prev;
    }
    free(node);
}

void freeList(DL_List *list)
{
    DL_Node *p = list->firstNode;
    while (p != NULL) {
        p = p->next;
        if (p!=NULL)
            free(p->prev);
        else
            free(list->lastNode);
    }
    free(list);
}

int main(void)
{
    DL_List *dll = NULL;

    dll = (DL_List*) malloc(sizeof(DL_List));

    dll->firstNode = NULL;
    dll->lastNode = NULL;


    insertBeginning(dll, createNewNode(14));
    insertBeginning(dll, createNewNode(13));
    insertBeginning(dll, createNewNode(12));
    insertBeginning(dll, createNewNode(11));
    insertBeginning(dll, createNewNode(10));

    insertEnd(dll, createNewNode(20));
    insertEnd(dll, createNewNode(21));
    insertEnd(dll, createNewNode(22));
    insertEnd(dll, createNewNode(23));
    insertEnd(dll, createNewNode(24));

    showList(dll);
    showListReverse(dll);

    removeNode(dll, dll->firstNode);
    removeNode(dll, dll->lastNode);
    showList(dll);

    freeList(dll);

    return 0;
}

```