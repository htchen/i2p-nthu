# Recursion #

之前看的 function 都是透過別人來呼叫，那麼如果一個 function 自己呼叫自己會發生什麼事呢？在 C 程式裡面確實允許 function 呼叫自己，這樣的動作稱作遞迴 (recursion)。對於新手來說，遞迴的運作方式沒有迴圈來的容易思考，大部分的人也是學到這裡開始放棄QQ。下面的內容，我希望能用簡單的例題來讓大家慢慢上手，體會遞迴和迴圈的不同，大家不要放棄啊。

程式中使用到迴圈的地方通常都可以改成用 recursion 方式來做。有時候某些工作用迴圈方式來思考會比較容易，但有時候用 recursion 則會比較簡潔。通常使用 recursion 的解法會比較漂亮，但是可能會比較沒有效率，使用的資源也會比較多，我們之後再比較兩者的效率、資源使用、編譯優化，還有當你的程式有bug會發生哪些悲劇。

## Example 1 (遞迴前) ##

先來寫個最簡單的程式，印出1~10，迴圈的版本大概會長這樣

```C
// A piece of C code
#include <stdio.h> 
int main(void){
    int i, n = 10;
    for(i=1; i<=n; i++){
        printf("%d ", i);
    }
    return 0;
}
```



```
Sample Output:
1 2 3 4 5 6 7 8 9 10 
```

那遞迴該怎麼寫呢?

```C
// A piece of C code
#include <stdio.h>
void F(int i){
 	if( i > 10 ) return;
    printf("%d ", i);
    F(i+1);
}
int main(void){
    F(1);
    return 0;
}
```

```
Sample Output
1 2 3 4 5 6 7 8 9 10 
```

先看看上面會發生什麼事情，首先 main 呼叫了 F(1) ，我們也很順利的印出了 1，然後 F(1) 還沒結束，他呼叫了 F(2)，F(2)印出了 2，然後呼叫了 F(3).... 一直到呼叫了 F(11) 條件判斷讓程式不在繼續呼叫下去。

所以使用 recursion 時最需要注意的是停止條件，因為如果停止條件不正確，有可能不斷呼叫而讓程式停不下來，最後甚至會因為使用過多資源而讓程式當掉。

## Example 2 (遞迴後) ##

```C
// A piece of C code
#include <stdio.h>
void F(int i){
    if( i > 10 ) return;
    F(i+1);
    printf("%d ", i);
}
int main(void){
    F(1);
    return 0;
}
```



```
Sample Output
10 9 8 7 6 5 4 3 2 1 
```

我們利用了在遞迴之後才輸出的方法，也就是當遞迴到了你限制的大小，它才一層一層的印回來。比較後可以發現，在遞迴前跟遞迴後的不同時機做事，剛好有相反的效果。

## Binary representation ##

來看看什麼情況 recursion 比迴圈方式更好寫。我們要來寫一個程式能把十進位整數值的二進位表示法顯示出來，譬如二進位的表示法 $101_2$ 其實就是 $1*2^2 + 0*2^1 + 1*2^0$，相當於十進位的 $5$。從十進位數字轉成二進位表示法的運算要怎麼做呢，我們可以先用十進位來思考。譬如平常寫 $9487$ 其實相當於 $9*10^3+4*10^2+8*10^1+7$。所以如果要得到個位數是多少，只要用 `9487%10`，就可以知道個位數是7。那麼接下來如果要知道十位數是多少，其實只要先把十位數降成個位數，也就是 `9487/10`，然後無條件捨去得到 948，接著再用同樣伎倆 `948%10` 就可以取得十位數。就這樣一直做下去就可以把每個位數都求出來。二進位運算其實完全一樣，只要把 `%10`改成 `%2`，然後把 `/10`改成` /2`就可以。

我們現在發現了一個問題，我們最早算出來的是個位數，但是個位數應該是要最後輸出的。如果我們用傳統的迴圈寫法，很顯然的需要儲存之前的結果，最後倒過來輸出。現在改成利用遞迴運算跟輸出可以在不同時機運作的特性，來完成二進位的轉換。

現在就來看看程式碼怎麼寫：

```C
// A piece of C code
#include <stdio.h>
void bin(int i){
    if( i != 1 ) bin(i/2);
  	printf("%d", i%2 );
}
int main(void){
    int n;
    scanf("%d", &n);
    bin(n);
    return 0;
}
```

## GCD **最大公因數** (Greatest Common Divisor) ##

```C
// A piece of C code
#include <stdio.h> // GCD 遞迴版
int GCD(int a, int b){
    if( b == 0 )
        return a;
    else
        return GCD( b, a%b );
}
int main(void){
    int a, b; // a,b > 0 
    scanf("%d%d", &a, &b);
    printf("%d\n", GCD(a,b));
    return 0;
}
```

```C
// A piece of C code
#include <stdio.h> // GCD 迴圈版
int main(void){
    int a, b; // a,b > 0 
    scanf("%d%d", &a, &b);
    while( a!=0 && b!=0 ){
        if( a > b )
        	a = a % b;
        else
        	b = b % a;
    }
    if( a != 0 )
        printf("%d\n", a);
    else
        printf("%d\n", b);
    return 0;
}
```

## Fibonacci number ##

```C
// A piece of C code
#include <stdio.h> // 遞迴版
int fib(int i){
    if( i == 1 || i == 2 )
        return 1;
    else
        return fib(i-1) + fib(i-2);
}
int main(void){
    int n;
    scanf("%d", &n);
    printf("%d\n", fib(n));
    return 0;
}
```

```C
// A piece of C code
#include <stdio.h> // 迴圈版
int main(void){
    int n, fib[100], i;
    scanf("%d", &n);
    fib[1] = 1;
    fib[2] = 1;
    for(i=3; i<=n; i++){
        fib[i] = fib[i-1] + fib[i-2];
    }
    printf("%d\n", fib[n]);
    return 0;
}
```

## 遞迴與迴圈 ##

到這裡相信大家對遞迴有基本的認識，或是還有一些模糊的感覺，接下來的經典題目需要對遞迴有一定的熟悉才能真正理解，所以我在這裡把觀念全部整理一次，看完之後回去在看看上面的例題，希望大家可以真正的了解上面在幹嘛，不要只是背起來(蠻短的其實很好背)。

+ 特色

  + 遞迴可以想像成一個塔，只要有人呼叫了function ，那就再堆一層積木上去，接著我們就暫時放下下面的東西，優先處理上面的，當一個 function 到了 return，也就是結束了，那這層的積木就會消失，我們回到下面繼續處理剛剛位完成的工作。
  + 而迴圈就是按照你規劃 index 的變化來決定執行的方向，通常是一路向前不回頭。

+ 記憶體

  + 如同遞迴的特色，每一次呼叫就多了一個積木，但是我們等下結束了新的積木還是要去找舊的積木，所以很顯然的舊的一些資料我們必須保留，因此每次呼叫，程式會找一快空的記憶體，等到這層 function return 之後，程式會釋放這塊記憶體，之後才能被再次利用。
    + 這種先放進去後處理、後放先處理的資料結構叫做 Stack 。
  + 至於一個程式能要到多少記憶體，這部分是由作業系統 (OS) 決定的，當你遞回過程要求的記憶體超過了限制，意思就是你的塔疊太高了，那會產生 stack overflow，通常會這樣是因為停止條件不足或錯誤，導致你的遞回停不下來產生的。而這種錯誤在編譯其間是無法偵測到的，因此這種錯誤發生在執行期間，被歸類為 Runtime Error 。
    + 通常 Runtime Error 的兩大原因是遞迴停不下來，或是讀到不該讀的記憶體位置 ( 像是a[-1] )

+ 效率

  + 我們拿 Fibonacci number 來仔細分析一下，在遞迴版本如果我要算 F(5) :

    ```mathematica
    F(5) = F(4) + F(3)
    	 = [ F(3) + F(2) ] + [ F(2) + F(1) ]
    	 = [ F(2) + F(1) + F(2) ] + [ F(2) + F(1) ]
    ```

    很顯然的我們算了好多重複的東西 比如說 F(3) 算了兩次，這樣的算法隨著 N 成長，計算量是成指數翻倍的，然而迴圈的版本在這裡卻不會有重複的問題。

+ Tail Recursion

  + 有一種遞迴因為比較特殊，因此我們把它歸類為 tail recursion 。這類最大的特徵就是在呼叫自己之後就結束了，也就是在呼叫之後就 return (像是Example 1)。那為什麼這樣特殊呢? 因為這類的遞迴我們呼叫時堆了一個新的積木上去，但是我們會發現，舊的積木沒有用了啊，該做的事情都做完了!!
  + 所以我們呼叫的時候，就可以把舊的丟掉，這樣記憶體就不會隨著呼叫的次數跟著增加了。等等，那這樣跟迴圈不是一樣的意思，在同一個地方一直做差不多的事情。沒錯，所以這類遞迴大部分都可以寫成迴圈的版本，就像上面的 Example 1。
  + 但是我就是想寫遞迴R? 那事情就更簡單了，因為這件事情其實不用我們煩惱。C語言在編譯的時候有很多選項，其中有一個是優化，有分四個等級，我們在編譯的時候如果加上 ` -O2`  (等級二)，那如果編譯器發現了你的遞迴可以優化，那它就自動變成跟迴圈一樣的了。當然些微的差異是迴圈是用同一塊記憶體，優化過的遞迴還是會去拿新的，但是舊的會立刻釋放，因此兩著需要的記憶體是差不多的。




## Fibonacci number & $C(m,n)$ (進階) ##


之前有提到重複計算的問題，那又該如何解決呢?

+ 既然不想一樣的東西算第二次，那我們就乾脆把答案存起來，下一次遇到就拿出來。

+ 其實這個方法有個名字叫做動態規劃 dynamic programming (DP)，大部分有遞迴性質的問題，可以藉由 DP 壓縮大量重複的計算，增加效率。而這當中也有把大問題切割成多個小問題的特性，這種手法叫做分治法 (divide and conquer)，我們在之後的 merge sort 會看到這樣經典的例子。

+ 雖然說這兩個問題用 DP 顯得大材小用，所以我們只要體會一下感覺就好了，大三的演算法會有更多神奇的DP。DP主要有兩個 coding 的方法，一個是用遞迴，一個用迴圈，沒有說哪個比較好，純粹是看哪個比較好思考或是比較好寫，當然如果都差不多的話，推薦選擇迴圈，一來效率比較好 (不用一直呼叫function)，也可以避免遞迴過深導致 stack overflow。

  + 迴圈版 : 英文叫做 bottom up， 我們用一個陣列紀錄之前的答案，接著一個一個算到我們要的地方。

    ```C
    // A piece of C code
    #include <stdio.h> // 迴圈版
    int main(void){
        int n, fib[100], i;
        scanf("%d", &n);
        fib[1] = 1;
        fib[2] = 1;
        for(i=3; i<=n; i++){
            fib[i] = fib[i-1] + fib[i-2];
        }
        printf("%d\n", fib[n]);
        return 0;
    }
    ```

  + 遞迴版 : 英文叫做 top down ，我們用一個 table 紀錄了答案是否算過。

    ```C
    // A piece of C code
    #include <stdio.h> // 遞迴版(有紀錄)
    int table[100];
    int fib(int i){
      	if( table[i] != 0 ) return table[i]; // if table[i] == 0, 代表這格沒有被填過 要算!
        if( i == 1 || i == 2 )
            return 1;
        else{
        	table[i] = fib(i-1) + fib(i-2); // 算完了當然要記起來啊
            return table[i];
        }
    }
    int main(void){
        int n;
        scanf("%d", &n);
        printf("%d\n", fib(n));
        return 0;
    }
    ```

+ $C(m,n)$ 的概念差不多，用迴圈也是可以，大家都試試看吧。

+ Hint : $C(m,n) = C(m-1,n)+C(m-1,n-1)$，遞迴條件

+ Hint : $C(m,m) = C(m,0) = 1$，邊界條件


## 快速冪 ($a^n$) ##

目標 : 算出 a 的 n 次方。

當然大家都會一個很簡單的方法，我們用個迴圈或是遞迴，把它跑個 N 次就有答案了。這當然也是對的，但是當 N 很大的時候，跑起來感覺很沒效率，所以我們有一個聰明的方法 -- 快速冪 (聽起來就很快)，以下的程式碼暫時先不考慮數字太大，int 會炸裂的情況。

主要的想法是這樣的 : 如果我們有 a， 可以經過一次算出平方，在經過一次算出四次方，直接跳過三次方。也就是說 $a^n$ 可以直接得到 $a^{2n}$ ，不用辛苦的算了 N 次。但如果有奇數怎麼辦，我們就用老方法 -1 把它變成偶數。舉例來說，如果要算13次方好了，一個不錯的順序應該是，`13->12->6->3->2->1` ，用這樣的方法大約可以在 logN 次完成計算。在之後的課程會學到兩者的時間複雜度分別是 $O(N)$、 $O(logN)$。

由於 $a^n$ 通常會很大，所以我們往往會讓它 mod 一個數字 ($p$)，基本上計算的精神不會變，只要在遞迴的過程中，隨時注意有沒有 overflow 的問題，也要隨題目數字大小的要求，適時的使用 `long long` 。

`int` 約可到 $10^9$ ， `long long` 約可到 $10^{18}$。

```C
// A piece of C code
#include <stdio.h>
int power(int a, int n){
    if( n == 1 ) return a;
  	int sqrt = power(a,n/2); // 這裡有使用到整數除法無條件捨去的特性。
    if( n % 2 == 0 )
      	return sqrt * sqrt;
    else 
        return a * sqrt * sqrt;
}
int main(void){
    int a, n;
    scanf("%d%d", &a, &n);
    printf("%d\n", power(a,n));
    return 0;
}
```

## Tower of Honoi ##

規則如下 : https://zh.wikipedia.org/zh-tw/%E6%B1%89%E8%AF%BA%E5%A1%94

河內塔的步驟可以分解如下：

+ 若有10層需要由A移到C，可以拆解成：上面9層A →B；第10層A →C；上面9層B →C
+ 上面九層A →B的移動同理，拆解成：上面8層A→C；第9層A→B；上面8層C→B
+ 其餘同理

利用遞迴將問題像洋蔥一樣一步步剝開，每一個遞迴函式只看這一層的移動。例如上面10層A →C的範例，這個遞迴函式只負責呼叫另一個函式處理上面9層A →B，再自己處理第10層A →C，再呼叫另一個函式處理上面9層B →C。通過這樣的方式，河內塔問題就變得簡單多了。

```c
// A piece of C code
#include <stdio.h>
// move n disks from a to b using c as a buffer
void hanoi(int a, int b, int c, int n){
    if ( n == 0 ) return;
    hanoi( a, c, b, n-1 );      // move the top n-1 disks from a to c
	printf("Move the #%d disk from Tower %d to Tower %d\n", n, a, b);
	hanoi( c, b, a, n-1 );      // move the top n-1 disks from c to b
}

int main(void){
	int n; // number of disks
	scanf("%d", &n);
	hanoi(1,3,2,n);
	return 0;
}
```

## 換銅板 ##

當我們有1元、5元、10元時，我們有哪些組合方法可以用這些錢幣湊出50元呢？

換銅板就是解決這類的問題，用某些幣值組合出想要的數量。

+ 我們可以先盡量用越多最大幣值，再用小幣值補足剩下的餘額。
+ 將最大幣值的數量減一，餘額再用小幣值捕。
+ 以此類推，我們可以得到所有可能的組合。

用遞迴呼叫的好處是，我們用一個 function 紀錄某一幣值一個一個減少的情況，同時呼叫另一個函式來計算下一個幣值的數量。透過一層一層的呼叫，在一個 function 內就不需要處理太多東西，問題也會變得簡單許多。

```c
// A piece of C code
#include <stdio.h>
#define MAXNV 5
int values[MAXNV];
int numbers[MAXNV];

void show(int nv);
void change(int amount, int smallest, int nv);

int main(void)
{
	int nv, i;
	int money;

	scanf("%d", &nv);
	if (nv>MAXNV || nv<1) return 0;

	for (i=0; i<nv; i++) {
		scanf("%d", &values[i]);
	}

	scanf("%d", &money);
	change(money, 0, nv);

	return 0;
}

void show(int nv)
{
	int i;
	printf("(%d", numbers[0]);
	for (i=1; i<nv; i++) {
		printf(",%d", numbers[i]);
	}
	printf(")\n");
}

void change(int amount, int smallest, int nv)
{
	if (smallest<nv) {
		if (amount == 0) {
			show(nv);
		} else if (amount > 0) {
			change(amount, smallest+1, nv);
			numbers[smallest]++;
			change(amount-values[smallest], smallest, nv);
			numbers[smallest]--;
		}
	}
}
```

## 城堡問題 ##

有玩過西洋棋嗎？在西洋棋的規則當中，城堡是可以不限距離橫向或直向移動的。如果想要在一個 n 乘 n 的棋盤上擺放最多的城堡，使每一個城堡都沒辦法一步就吃到對方。那這樣最多會有哪多少擺放方式呢？

我們可以這樣想

+ 先在第一列第一個位置擺上一個城堡，在到第二列看哪些位置可以再擺上另一個城堡使新擺上的城堡不和之前的城堡互相衝突。第一列第一個位置的組合全部試過了之後，將這個城堡移到第一列的第二個位置，再看下一列之後可以放在哪裡。以此類推。
+ 在每一步中往前看有沒有和之前的城堡衝突，若沒有，先擺上去在檢查下一列。當之後的列都檢查完組合了，則將這列的城堡往下移到下一行，繼續檢查之後的列。停止條件為到最後一列結束。

我們可以看到這個問題非常適合用遞迴，因為這一列的問題不會影響到前面的列。若我們用遞迴呼叫 function ，那這個問題就變得簡單得多，因為 function 就只需要判斷他那一列的工作就可以了。讓我們來看看下面的程式碼

```c
// A piece of C code
#include <stdio.h>
#define n 3//棋盤大小

int board[n+2][n+2];
void place(int row);
void show_board(void)
{
	int i, j;
	for (i=1; i<=n; i++) {
		for (j=1; j<=n; j++) {
			printf(" %d", board[i][j]);
		}
		printf("\n");
	}
	printf("\n");
}
int main(void)
{

	place(1);
	return 0;
}
int valid(int row, int col)
{
    int i;
    for (i=1; i<row; i++) {
        if (board[i][col]) return 0;
    }
    return 1;
}
void place(int row)
{
	int i;
	if (row>n) {
		show_board();
	} else {
		for (i=1; i<=n; i++) {
		    if (valid(row, i)) {//判斷這個位置會不會跟前面已經擺放的起衝突
			    board[row][i] = 1;
			    place(row+1);
			    board[row][i] = 0;
			}
		}
	}
}
```

## 八皇后 ##

八皇后的問題和城堡問題很像，只是皇后的走法不太一樣。皇后除了可以像城堡那樣走之外，還可以不限距離45度鞋向走。

雖然皇后的走法不一樣，但是解決問題的基本概念和城堡問題一樣，都是一步一步看這個位置有沒有辦法放皇后，可以的話再移到下一列檢查之後的可能。

```c
// A piece of C code
#include <stdio.h>
#define NQ 8//棋盤大小

int q[NQ] = {0};

void place(int row);
int valid(int row, int col);
void display(void);

int main(void)
{

	place(0);
	return 0;
}


int valid(int row, int col)
{
	int i;
	for (i=0; i<row; i++) {
		if ( q[i] == col || row-i == col-q[i] || row-i == q[i]-col )
		return 0;
	}
	return 1;
}

void display(void)
{
    int i, j;
    for (i=0; i<NQ; i++) {
	    for (j=0; j<NQ; j++) {
	        if (q[i]==j) printf("@");
	        else printf("O");
	    }
	    printf("\n");
	}
	printf("\n");
}

void place(int row)
{
	int j;
	if (row == NQ) {
		display();
	} else {
		for (j=0; j<NQ; j++) {
			if (valid(row, j)) {//判斷這個位置會不會跟前面已經擺放的起衝突
				q[row] = j;
				place(row+1);
			}
		}
	}
}
```

## Knight's Tour ##

在西洋棋盤上面，騎士的走法是日字形，就像象棋中馬的走法一樣。 Knight's Tour 的問題是，我們要如何找出一條路徑，可以讓騎士把棋盤上所有的格子都走遍。

這個問題看似比八皇后的問題複雜，其實概念上是差不多的。因為騎士的走法，在一個位置的騎士有八個可能的方向可以走。在計算時每走完一個方向後必須退回到目前的位置，再往另一個方向接著計算。藉由遞迴的特性，我們可以在一個函式中呼叫下一個函式做接下去的運算，同時卻不會忘記目前的狀態。

```c
// A piece of C code
#include <stdio.h>
#define n 5//棋盤大小
#define start_row 3
#define start_col 3

int board[n+2][n+2];

void place(int row, int col, int num);

void show_board(void)
{
    int i, j;
    for (i=1; i<=n; i++) {
        for (j=1; j<=n; j++) {
            if(board[i][j]<10){                   //為了讓輸出的棋盤更好看
                printf(" %d ", board[i][j]);
            }else{
                printf("%d ", board[i][j]);
            }
        }
        printf("\n");
    }
    printf("\n");
}

int main(void)
{
    place(start_row, start_col, 1);
    return 0;
}

int valid(int row, int col)
{
    if(board[row][col]==0 && row>=1 && row<=n && col>=1 && col<=n){
        return 1;
    }
    return 0;
}

void place(int row, int col, int num)
{
    if (num==n*n+1) {
        show_board();
    } else {
        if(valid(row, col)){
            board[row][col]=num;
            place(row-2, col+1, num+1);
            place(row-1, col+2, num+1);
            place(row+1, col+2, num+1);
            place(row+2, col+1, num+1);
            place(row+2, col-1, num+1);
            place(row+1, col-2, num+1);
            place(row-1, col-2, num+1);
            place(row-2, col-1, num+1);
            board[row][col]=0;
        }
    }
}
```

## Prefix ##

如果一個東西能畫成一棵樹 (樹狀圖)，那它通常都有三種表示法:

+ prefix
+ infix
+ postfix

數學的四則運算正好也有這個特性，

```c
// A piece of C code
#include <stdio.h>
#include <ctype.h>
int cal(void){
    int ans, op1, op2;
    char c = getchar();
    if ( c == ' ' ) return cal();
    if ( isdigit(c) ) {
        ungetc(c, stdin);
        scanf("%d", &ans);
        return ans;
    }
    op1 = cal();
    op2 = cal();
    if( c == '+' ) return op1 + op2;
    if( c == '-' ) return op1 - op2;
    if( c == '*' ) return op1 * op2;
}
int main(void){
    printf("%d\n", cal());
    return 0;
}
```

## DFS (Depth-First Search) ##

深度優先搜尋法

##行列式**（Determinant）(補充)

```C
我寫不出漂亮的code QQ
```

## Binary Search (補充) ##

在一個排序過後的數列中找尋一個數，最快的方式是什麼呢？當然，一個一個掃過一定是沒問題的，這樣的時間複雜度是O(n)。但是其實有另一個更快的方法。

因為數列是已經排序過的，所以我們可以先取排列在中間的值，看這個值跟我們所要找的數是不是一樣大。若是一樣大，那我們就找到了；若是這個值比較小，那我們可以從比它大的那一群數中再找，反之亦然。如此一步一步取區間內中間位置的值比對，和第一個方法相比，可以省下很多時間。這樣的方法的時間複雜度是O(logn)，在數列的大小越大時，時間的差距就會越明顯。

以下是範例程式碼

```c
// A piece of C code
#include <stdio.h>
int data[]={1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
void find(int, int, int);

int main(void)
{
    int number;
    printf("Enter a number between 1 and 10 you want to find:\n");
    scanf("%d", &number);
    find(number, 0, 9);
}

void find(int number, int head, int tail)
{
    int middle=(head+tail)/2;
    if(head>tail){
        printf("%d is not in the data.\n", number);
    }
    else if(data[middle]==number){
        printf("%d is at position %d.\n", number, middle);
    }
    else if(data[middle]>number){
        find(number, head, middle-1);
    }
    else{
        find(number, middle+1, tail);
    }
}
```

這樣的例子，我們是已經提供一組排序過的資料。若需要在一組未排序的資料中找值這樣的方法是不行的，唯一的方法是一個一個掃過。這樣的方法在搜尋的數字比較少時沒問題，但壞處是當尋找的數字很多時就很沒效率。但是我們可以先對未排序的資料先排序 (sort) ，再用 binary search 。這樣的優缺點和前面的方法相反，在搜尋的數字少時，因為一開始需要 sort ，所以會多花時間；但是當尋找的數字多時，binary search的速度就會體現出來。而sort的方法有很多種，下面要介紹的就是用遞迴實現Merge Sort。

## Merge Sort (補充) ##

```C
// A piece of C code
#include <stdio.h>
#define MAXN 1000000

int a[MAXN], buffer[MAXN];
void merge(int *A, int lena, int *B, int lenb){
    int i = 0, j = 0, k = 0; // index for A, B, and buffer
    while( i < lena && j < lenb ){
        if( A[ i ] < B[ j ] ) // use '<' to make merge sort stable
            buffer[ k++ ] = A[ i++ ];
        else
            buffer[ k++ ] = B[ j++ ];
    }
    while( i < lena )  buffer[ k++ ] = A[ i++ ];
    while( j < lenb )  buffer[ k++ ] = B[ j++ ];
}

void merge_sort(int l, int r){
    if( l >= r ) return; // bound condition
    int mid = (l+r)/2;   // recurtion
    merge_sort( l, mid );
    merge_sort( mid+1, r );
    merge( a+l, mid-l+1, a+mid+1, r-mid );
    int i;
    for(i=0; i<r-l+1; i++){
    	a[l+i] = buffer[i];
    }
}

int main(){
    int n, i;
    scanf("%d", &n);
    for(i=0; i<n; i++){
        scanf("%d", &a[i]);
    }
    merge_sort( 0, n-1 );
    for (i=0; i<n; ++i){
        printf("%d ", a[i]);
    }
    return 0;
}
```

