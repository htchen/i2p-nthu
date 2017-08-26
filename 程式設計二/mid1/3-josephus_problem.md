# Josephus problem

這是一個不知道從哪裡來的故事 : 

Flavius Josephus is a Jewish historian living in the 1st century.  According to his account, he and his 40 comrade soldiers were trapped in a cave, surrounded by Romans. They chose suicide over capture and decided that they would form a circle and start killing themselves using a step of three. As Josephus did not want to die, he was able to find the safe place, and stayed alive with his comrade, later joining the Romans who captured them. 

### Problem description

+ n people form a circle, numbered from 1 to n.  Starting from the number 1-st person, killing every m-th person, who will be the last one?

+ This problem has two parameters : n and m, and the output is an integer between 1 and n.  

+ Problem : write a program to calculate the output. 

  有 n 個人排成一圈，從 第一個位置開始數，往前每數到第 m 個人，就將那個人從圈子中移除。將被移除的順位列出，就構成所謂的 Josephus Permutation。以 n=8, m=4 為例，假設從編號 1 的人開始數，則最早被移除的會是編號 4 那個人。請看底下的過程，編號加底線表示從那個人開始數，粗體的編號代表要被移除的人 : 

  + 第一次 : 1 2 3 **4** 5 6 7 8    ->    1 2 3 5 6 7 8
  + 第二次 : 1 2 3 5 6 7 **8**    ->    1 2 3 5 6 7
  + 第三次 : 1 2 3 **5** 6 7    ->    1 2 3 6 7
  + 第四次 : 1 **2** 3 6 7    ->    1 3 6 7 
  + 第五次 : **1** 3 6 7    ->    3 6 7
  + 第六次 : **3** 6 7    ->    6 7
  + 第七次 : 6 **7**    ->    6
  + 第八次 : **6**   ->   NULL

  所以編號 1 到 8 的人被移除的順位是 { 5 4 6 1 3 8 7 2 }，這就是 n=8, m=4 的 Josephus Permutation，我們可以寫成 JP(8, 4) = { 5 4 6 1 3 8 7 2 }，它的意思是編號 1 的人會是第五順位被移除，編號 2 的人會在第四順位被移除，而編號 6 的人會是第八順位，也就是最後一個存留下的人。

  另外我們也可以列出 Inverse Josephus Permutation，以上面 n=8, m=4 的例子會是 IJP(8, 4) = { 4 8 5 2 1 3 7 6 } 也就是依照被移除的順序將編號列出。

我們在下面會給出一卡車的做法，之後我們會仔細的分析每個方法的時間複雜度，讓大家對於演算法和資料結構有初步的認識。

這個問題的詳細描述與數學解 : https://en.wikipedia.org/wiki/Josephus_problem

## Algorithm

An **effective** method for **solving a problem** using a **finite** sequence of **instructions**.

1. 輸入：一個演算法必須有零個或以上輸入量。

2. 輸出：一個演算法應有一個或以上輸出量，輸出量是演算法計算的結果。

3. 明確性：演算法的描述必須無歧義，以保證演算法的實際執行結果是精確地符合要求或期望，通常要求實際執行結果是確定的。

4. 有限性：依據圖靈的定義，一個演算法是能夠被任何圖靈完備系統模擬的一串運算，而圖靈機只有有限個狀態、有限個輸入符號和有限個轉移函式（指令）。而一些定義更規定演算法必須在有限個步驟內完成任務。

5. 有效性：又稱可行性。能夠實現，演算法中描述的操作都是可以通過已經實現的基本運算執行有限次來實現。

   ( From Wiki )

   對於同一個問題，我們可以設計出很多不一樣的方法，而他們都是正確的。這時候我們會嘗試去比較這些演算法的效率，而我們最常用的方法是時間複雜度。

## Time Complexity

+ The efficiency of an algorithm is usually measured by the **number of operations**
  + assignment, comparison, arithmetic operation
+ Computer scientists are interested in the **worst case** running time
+ Operation count need be expressed as a function of problem size n.
  + For example, $2^n+n^3+n^2+nlogn$
+ Computer scientists are interested in the operation count for large n, determined by the order of the **dominant term**
  + $2^n$ is the order of the dominant term


+ Big-O Notation
  + f(x) = O(g(x)) if and only if there is a positive constant M such that for all sufficiently large x, the absolute value of f(x) is at most M multiplied by the absolute value of g(x). 
  + $2n^2+3n+4 = O(n^2)$
  + $10^9 = O(1)$
  + $\sqrt n+logn = O(\sqrt n)$
  + $n^{1.0001}+nlogn=O(n^{1.0001})$
  + $2^n+n^{100} = O(2^n)$
+ 常見演算法的時間複雜度
  + binary search $O(logn)$
  + factor $O(\sqrt n)$
  + find_MAX $O(n)$
  + quick sort $O(nlogn)$
  + bubble sort $O(n^2)$
  + matrix multiply $O(n^3)$

### 模擬過程 方法一

演算法採用模擬的方式，資料結構則採用 Array。

做法是先產生一個陣列，陣列的元素是每個人的編號。接下來就逐步模擬移除的過程，移除了某個人之後，就將後面的陣列元素往前挪。

```C
// Method 1 for Inverse Josephus Permutation
#include <stdio.h>

int a[100];
int main(){
    int n = 41, m = 3, i;
    int p = n; // people who alive
    int k = 1; // people who will be killed next
    for( i=1; i<=n; i++) a[i] = i; // number
    while( p > 0 ){
        k += m-1;
        while( k > p ) k -= p;
        printf("%d ", a[k]);
        for( i=k; i<p; i++){
            a[i] = a[i+1];
        }
        p--;
    }
    return 0;
}
// output : 3 6 9 12 15 18 21 24 27 30 33 36 39 1 5 10 14 19 23 28 32 37 41 7 13 20 26 34 40 8 17 29 38 11 25 2 22 4 35 16 31 
```

### 模擬過程 方法二

和 Method 1 一樣，演算法也是採用模擬的方式，資料結構採用 Array。

和 Method 1 不同的地方在於，移除了某個人之後，只將原本的陣列元素標記為 -1，不移動其他陣列元素。

```C
// Method 2 for Inverse Josephus Permutation
#include <stdio.h>

int a[100];
int main(){
    int n = 41, m = 3, i;
    int p = n; // people who alive
    int k = 0; // people who will be killed next
    for( i=1; i<=n; i++) a[i] = i; // number
    while( p > 0 ){
        int cnt = 0;
        while( cnt < m ){
            if( a[k%n+1] != -1 ) cnt++;
            k++;
            if( k > n ) k -= n;
        }
        printf("%d ", a[k]);
        a[k] = -1;
        p--;
    }
    return 0;
}
```

### Circular Linked List 1

演算法也是採用模擬的方式，資料結構採用 Circular Linked List。

```C
#include <stdio.h>
#include <stdlib.h>

typedef struct _node {
    int idx;
    struct _node *next;
} node;

node* build(int n){
    int i;
    node *np = (node*) malloc( sizeof(node) ); // idx = 1
    node *nq = np;
    np->idx = 1;
    for( i=2; i<=n; i++){
        np->next = (node*) malloc( sizeof(node) );
        np = np->next;
        np->idx = i;
    }
    np->next = nq; // circle
    return nq;
}

node* del_next(node* np){
    printf("%d ", np->next->idx);
    node *nq = np->next;
    np->next = np->next->next;
    free(nq);
    return np->next;
}

int main(){
    int n = 41, m = 3;
    node *k = build(n);
    int p = n;
    while( p-- ){
        int go = m-2;
        while( go-- ) k = k->next;
        k = del_next(k);
    }
    return 0;
}
```

這個方法的時間複雜度就很好分析了，因為我們不會經過那些已經死掉的人了，所以我們每次都剛好數 m 次就可以殺一個人，我們總共有 n 個人要殺，所以總共的時間複雜度是 $O(mn)$。

### Circular Linked List 2

演算法也是採用模擬的方式，資料結構採用 Array 來模擬 Linked List。

主要的想法是利用 Array 模擬 Circular Linked List，自己維持`next`的連接順序。從這個例子也可以看出 Array 與 指標之間相通之處。此外，使用 Array 模仿 Linked List 與真正的 Linked List 還有一個不同的地方：Linked List 可以動態產生和移除`Node`，如果是用 Array 模仿則會占用固定大小的空間，沒用到的`Node`其實還在原位，沒有真的被移除掉，只是靠`next`跳過而已。

```C
#include <stdio.h>

typedef struct {
    int idx;
    int next;
} node;
node a[100];

int main(){
    int n = 41, m = 3, i;
    for( i=1; i<=n; i++){
        a[i].idx = i;
        a[i].next = i % n + 1;
    }
    int p = n, k = n;
    while( p-- ){
        int go = m-1;
        while( go-- ) k = a[k].next;
        printf("%d ", a[ a[k].next ].idx);
        a[k].next = a[ a[k].next ].next;
    }
    return 0;
}
```

時間複雜度一樣是 $O(mn)$

### Recursion

來看遞迴的做法。底下的寫法只能列出最後一個留下來的人，不能列出 Inverse Josephus Permutation。

```C
#include <stdio.h>

int f(int n, int m){ // index from 1 to n
    if( n == 1 ) return 1;
    else return ( f(n-1, m) + m-1 ) % n + 1;
}

int main(){
    int n = 41, m = 3, i;
    printf("%d\n", f(n,m));
    return 0;
}
```

上面的函數`F(n, m)`傳回的值是最後能夠存留下來的人的編號，也就是如果總共 n個人，每m個移除掉一人，最後能夠剩下的人他的編號會是`F(n,m)`。同樣地，`F(n-1,m)`則是總共 n-1 個人、每 m 個移除掉一人，最後留下的人的編號。

遞迴的關鍵在於找出`F(n,m)`和`F(n-1,m)`之間的關聯。原本有 n 個人，移除掉一個之後，剩下 n-1 個人。假如我們有辦法知道`F(n-1,m)`是多少，那麼我們該如何推算出`F(n,m)`?

如果我們知道`F(n-1, m)`，也就是最後留下的那個人，我們就能換算出他在原本的 n個人的相對位置。以`F(41,3)`為例，如果我們知道`F(40,3)`的值是 28，對應到原本 41 人的情況中，`F(40,3)`其實是從原本的編號 4 的人開始數，因為原本 41人如果是從編號 1 開始數，一開始第一個被移除的人編號應該是 3 ，所以接下來剩下的 40 個人中的第一個人 (新的編號 1)，在原本 41 人中的編號是 4。這樣我們就可以看得出新舊編號的對應關係 ，所以如果`F(40,3)`是 28，則`F(41,3)`應該是 31。

時間複雜度 $O(n)$

### Magic (補充)

上面的方法不是效率不好，最快的 Linked List 需要 $O(mn)$ ，就是知道的資訊太少，像是遞迴只能算出最後一個，沒辦法算出被殺的順序。所以我們將題目延伸一下，我們要能知道誰是第幾個死的，第幾個死的又是誰 ? 

輸出`JP(n,m)`

```C++
#include <stdio.h>

int main(){
    int n = 8, m = 4, p, i;
    for( i=1; i<=n; i++){
        p = i;
        while( p % m != 0 ) p += n - p/m;
        printf("%d ", p/m);
    }
    return 0;
}
```

輸出`IJP(n,m)`

```C++
#include <stdio.h>

int main(){
    // n = people, i = the i-th be killed(1~n)
    // m = every m people kill one
    // p = the index(1~n) who be killed
    int n = 8, m = 4, p, i;
    for( i=1; i<=n; i++){
        p = i * m;
        while( p > n ) p = p-n + (p-n-1) / (m-1);
        printf("%d ", p);
    }
    return 0;
}
```

至於上面的方法為什麼會對，是經過數學簡化過的，主要的想法是將報數的編號一直延續下去，也就是報數的數字會是 1~n*m 每個報數到 m 的倍數的人被殺，剩下的人繼續報數。

這個算法的優勢是，每次的計算是獨立的，我們不需要知道上次被殺的是誰，就可以算出任一個順序是誰被殺，或是誰多久後會被殺。

至於時間複雜度的表現呢 ? 如果要列出整個過程的話，那麼兩個的複雜度都是 $O(nm)$也就是和 Linked List 一樣。但它更有優勢的地方在於它可以很快的找出任一個人的答案，而 Linked List 必須從頭模擬到那個人才會有答案。找任一個的時間複雜度和他在哪時候被殺有關連，但平均上是 $O(m)$, 最慘的一個大約是 $O(mlogn)$

手寫證明 : 

![image](images/josephus_proof.jpg)
