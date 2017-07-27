# 第四章：字串與格式化輸入輸出 #
## 字串 ##
```C
範例 1
#include <stdio.h>
#include <string.h>
#define SPEED 0.083

int main(void)
{
float minutes, distance;
int size, letters;
char name[10];
printf("Hi! What's your first name? ");
scanf("%s", name);
printf("\n%s, how many minutes does it take to walk from\n", name);
printf("your dormitory to the CS building? ");
scanf("%f", &minutes);
size = sizeof(name);
letters = strlen(name);
distance = minutes * SPEED;
printf("\nThe distance from your dormitory to the CS building\n");
printf("is about %.3f kilometers.\n\n", distance);
printf("By the way, your first name has %d letters,\n", letters);
printf("and we have %d bytes to store it in.\n", size);
return 0;
}
```
我們來看看這個程式做了哪些事情以及用到了哪些新的東西。執行這個程式會得到類似下面的結果:
```
Hi! What's your first name? Stevie
Stevie, how many minutes does it take to walk from your dormitory to the CS building? 
The distance from your dormitory to the CS building is about 0.664 kilometers.
By the way, your first name has 6 letters,and we have 10 bytes to store it in.
```
程式先用 `printf()` 顯示訊息，詢問使用者的名字，然後用 `scanf()` 來讀取使用者輸入的 "字串"，把字串儲存在變數name 裡面。再來就可以用儲存起來的字串稱呼使用者，並且問一個簡單的問題，再把計算結果顯示在螢幕上。程式裡面用到的新東西首先是 `#include <string.h>`。回想當初程式需要 `#include <stdio.h>` 是因為要用到 `printf()`，同樣地，引入 `string.h` 是為了要用到其中相關的 `function`，在這個例子用到的是 `strlen()` 這個 `function`，它可以計算字串變數裡面所儲存的字串的長度。傳入的參數是字串，傳回的整數值就是字串長度，以 `Stevie` 為例，長度是 6。程式第三行的`#define SPEED 0.083` 利用 C Preprocessor 來做前處理，在 compile 之前，會先把程式裡所有出現的 `SPEED` 字眼通通取代成 `0.083`。字串其實是靠所謂的`haracter array` 來儲存，

```
char name[10];
```

表示 `name` 會佔用到十個 bytes 的記憶體，等於是有十個空位，每個空位的大小是一個 byte，每個 byte 的空間可以存放一個字元。接下來會看到`printf()` 和 `scanf()` 裡都用到 `%s`，表示要處理的輸入輸出資料是字串 (string)。有一個比較奇怪的地方是在 `scanf()` 裡 `weight` 前面有加 `&`，但是 `name` 前面卻沒有。(由此可以推測 `&weight` 和 `name` 應該代表著對等的性質。稍後我們就會解釋，其實它們都對應到記憶體位址。)

## 字元陣列 character arrays 與字串 ##
|s|t|e|v|i|t|\0|
|---|---|---|---|---|---|---|---|
字串裡的字元必須連續地存放在記憶體中，所以剛好可以用陣列來儲存，因為陣列就是一連串的記憶體空間。字元陣列的每一格空間可以存放一個字元 (char)，當我們宣告 `char name[10];` 表示要保留十格空間存放十個字元，每一格可以容
納一個 char 型別的資料。當然除了 char 之外，也可用其他型別如 int 來宣告陣列，目前我們先專心探討字元陣列。為了標記整個字串究竟在哪裡算是結尾，C 語言使用一個特殊的字元 '\0' 來表示字串結尾。字元 '\0' 對應到的SCII 值是0。我們也可以用整數 0 來代替字元 '\0'，但為了有所區別，當字元使用時最好寫成 '\0'。

宣告一個字元變數和宣告一個陣列的差別可以用下圖來表示。
```
char ch;   佔用一個 byte，型別為 char
char name[10];　佔用十個 bytes，型別為 char
```

宣告陣列產生一個可以容納十個字元的 array，準備用來記錄使用者輸入的字串 (使用者的名字)。因為要保留一格給 '\0'字元來標示字串結尾，所以其實真正能用來記錄的人名長度，最多只能包含九個字元。如何把字串存入陣列中呢？在前面的例子裡用的方法是 `scanf("%s", name);` 讀取使用者輸入的字串。所以 %s 就表示要把使用者輸入的東西當作 "字串" 讀進來，然後參數 name 就是要存放字串的陣列名稱，這個名稱所代表的意義是整個字串的開頭位址。因此 `scanf()`
就能由 name 找到陣列開頭位址，一格一格把字元填進去，而且會自動在最後加上 '\0' 當作結束。

現在暫時來想想下面這個沒什麼用的程式，其中的 printf() 有什麼差別。
```C
範例 2
  #include <stdio.h>
  int main(void)
  {
   printf("%d\n", '7');
   printf("%c\n", '7');
   printf("%s\n", "7");
   return 0;
  }
```
最需要注意的是 '7' 代表的是字元 (ASCII 碼是 55)，但是 "7" 是字串，它除了包含 '7' 這個字元之外，還多藏了一個'\0' 字元。

再回到範例 E04_01.c， 裡面又用到 sizeof，它可以計算型別 (譬如 sizeof(float)) 或資料 (譬如 sizeof(name) 或 sizeof("Stevie")) 的 byte 數。若把它用在宣告過的陣列上，它會告訴我們陣列有多少個bytes，如果是 char name[10]; 就是 10 bytes。另外 #include <string.h> 是為了用 strlen() 這個 function，它是針對字串而設計的function，可以算出字串長度。字串長度是靠 '\0' 來界定，所以如果把某個字串當參數傳給 strlen()，它就會一個字元一個字元地數，直到遇到 '\0' 就停下來，然後回報字串長，看看底下例子中的差異：
```C
範例 3
#include <stdio.h>
#include <string.h>
int main(void)
{
printf("The length of string is %d, but %d bytes are occupied.\n",
strlen("Stevie"), sizeof("Stevie"));
return 0;
}
```
## Constants and the C Preprocessor ##
接下來解釋如何在程式裡設定常數。在範例 E04_01.c 裡用到 #define SPEED 0.083 這一行的作用是告訴 C preprocessor 要在 compile 之前，先把程式碼裡面出現的 SPEED 字眼都取代成 0.083。它的主要功能是為了寫程式方便，因為你的程式可能會在許多地方用到 0.083 這個數據來做計算，為了避免打錯或是忘記，可以把 0.083 取名作 SPEED，程式裡只要直接使用 SPEED 就可以，等到要 compile 的時候，會有前處理的動作先幫你把 SPEED 都換成實際上需要的數值 0.083。另外還有一個方便的地方是當你想要更改數據，譬如 0.083 改成 0.082，你只需要改 #define SPEED 0.082這個地方，其餘的程式碼都不需要更改。假如你不是用 #define 而是直接把數字寫死，你就要自己找出每個出現 0.083的地方，然後把它改成 0.082，這樣做不但麻煩而且容易出錯。所以當你的程式用到某些常數時，最好不要直接使用常數的數值，而應該替它取個代名詞。

( 還有一種設定 "整數常數" 的方法是使用 enum，有興趣的話可以查詢課程網頁上的參考資料連結 The GNU C Programming Tutorial (ctut.pdf) Chapter 19, pp. 189-191。)

在送到 compiler 之前已經先做了取代的動作。
例如:
```C
#define speed 0.083
int main()
{
distance = minutes * speed;
}
```
=> preprocessing
```C
#define speed 0.083
int main()
{
distance = minutes * s0.083;
}
```
=>compiling

## 關於 limits.h 與 float.h ##
如果進入你的 compiler 軟體所在的目錄裡的 include 資料夾，譬如 D:\Dev-Cpp\include\ 裡面會有一個叫做 `limits.h` 的檔。用文字編輯器打開檔案你會發現裡面用 #define 設定了很多常數值，譬如

```C
#define LONG_MAX 2147483647L
```
這些常數值和型別的範圍有關。假如你想知道整數最大可以到多大，可以使用 `INT_MAX` 來表示。所以相較於把數字都寫死在程式碼裡面，使用這些常數帶來的額外好處是，假如你的程式拿到另外一台不同的電腦上要執行，只需要重新compile就可以取得相對應的 `INT_MAX`，程式碼完全不必更改，因為那台電腦也會有對應的 INT_MAX，縱使這個常數值和原本電腦上的有所差異也不會產生問題。另外 `float.h` 檔也設定了類似的常數 (檔案可能在D:\Dev-Cpp\lib\gcc\mingw32\3.4.2\include 路徑下)，底下的範例會把這些值顯示出來。

```C
範例 4
#include <stdio.h>
#include <float.h>

int main(void)
{
printf("Number of bits in the mantissa of a float: %d\n", FLT_MANT_DIG);
printf("Minimum number of significant decimal digits for a float: %d\n", FLT_DIG);
printf("Minimum value for a positive float retaining full precision: %e\n", FLT_MIN);
printf("Maximum value for a positive float: %e\n", FLT_MAX);
printf("Difference between 1.00 and the least float value greater than 1.00: %e\n",FLT_EPSILON);

return 0;
}
```

## 更進一步了解 printf() 和 scanf() ##
接下來我們要再多介紹一些 printf() 和 scanf() 的功能，也藉此多熟悉一下寫程式的技巧。首先再來做個練習複習一下：



大家目前應該和 printf() 混得蠻熟了，printf() 除了常用的 %d, %f, %s 這些格式之外，還有很多其他格式可用，詳細用法請參考課程網頁上所附的連結 The GNU C Library Reference Manual (glibc-manual-2.2.5.pdf) 12.12 FormattedOutput, p. 264。

雖然都沒正式提過，但是大家應該有都觀察的出來，printf() 的格式其實就是

```C
printf(control-string, item1, item2, ...);
```
其中 control-string 是用 "..." 指定的那一連串輸出格式設定，而 item1, item2, ... 就是你要輸出的資料。再次提醒要特別小心 control-string 裡的 %d 之類的輸出格式設定要和後面傳入的參數有一對一的對應，譬如
```C
printf(": %d ft %d in\n", "LeBron James", foot, inches);
```
是錯的，因為少了一個 %s，對應到 "LeBron James"。

用實際的例子來看看常用的格式設定的用法。
```C
[範例 E04_05.c]
#include <stdio.h>
#define ENGINE 1499.99

int main(void)
{
printf("~%f~\n", ENGINE);
printf("~%e~\n", ENGINE);
printf("~%4.2f~\n", ENGINE);
printf("~%3.1f~\n", ENGINE);
printf("~%10.3f~\n", ENGINE);
printf("~%-10.3f~\n", ENGINE);
printf("~%12.3e~\n", ENGINE);
printf("~%+4.2f~\n", ENGINE);
printf("~%010.2f~\n", ENGINE);
return 0;
}
```
從輸出結果可以猜出每種格式的功用。第三個 `printf()`，`%4.2f` 表示最少要顯示出四個字元寬度，而小數點後要顯示二位小數。第四個 `printf()` 用的是 `%3.1f`，所以最少要顯示出三個字元寬，而小數點後只顯示一位小數，這麼做會使得原本的數字被自動四捨五入。第五個 `printf()` 因為用了 `%10.3f` 所以最少要顯示十個字元寬 (不足的會補空白)，然後小數點後顯示三位小數；可以數數看在兩個 ~ 符號中間是否正好包含十個字元 (包含空白字元和小數點)。接下來 `%-10.3f` 意思和 `%10.3f` 相同，差別只是變成要向左靠齊 (這是加了 – 號的效果)。第七個 `printf()` 用的是 `%12.3e` 所以總寬度是12 個字元，一樣可以數數看是否有 12 個字元，不足的也會用空白來填補。而由於小數點後只顯示三位小數，所以無法完整表示成 `1.49999e+003`，會被自動四捨五入成 `1.500e+003`。第八個 `printf()` 用 `%+4.2f` 多了 + 號只是表示要在數
字前面顯示正負號，因為 `1499.99` 是個正數，所以就多顯示了 + 號。最後一個 `printf()` 用到 `%010.2f` 意思是要顯示最少十個字元寬，不足個地方不再是用空白來補，而是改成補零。以上這些就是可能會用到的格式設定。



電腦是用二進位表示法來儲存資料，它記錄的只是一堆 0 與 1 的 bits。譬如 76 這個十進位數字用二進位表示是01001100。假設有某個變數 x 它對應到的記憶體空間裡儲存著 01001100，這樣的資料究竟代表什麼意義要看我們怎麼解讀這些 bits。假設我們想要把它當作十進位整數，它的值就等於 76。而當我們想把 76 這個數字顯示到螢幕上時，如果使用 printf() 的 %d 格式， printf() 做的事情就變成要先顯示 '7' 這個字元，然後再顯示 '6' 這個字元 ( '7' 的ASCII code 是 55，'6' 是 54)。


再更進一步，如果我們把 01001100 當作 ASCII code 來解讀，使用 printf() 的 %c 把它顯示出來，它就變成對應到 'L' 這個字元。這些差異其實不難理解，雖然乍看之下很容易讓人混淆，但只要掌握一個原則就是二進位表示是資料真正的本質，至於如何解讀這些二進位 bits，就要看我們的需求和意圖來決定。

簡單地說， printf("%d", x); 的動作就是把 x 的值當成十進位整數，然後把每個位數取出來，一個個用字元顯示到螢幕上。至於 printf("%c", x); 的動作則是把 x 當成 ASCII code，把這個 ASCII code 對應的字元顯示到螢幕上。

## 關於 scanf() ##
用鍵盤打字是我們輸入資料到電腦裡的主要方式之一。譬如我們要輸入 2008 這個數目到電腦裡，我們會依次按下鍵盤上的數字鍵 2 0 0 8，每個鍵送出的資訊相當於一個字元，所以我們有四個字元 '2' '0' '0' '8'。如果我們想要的是把這四個數字合在一起看，當作是一個數值 (兩千零八)，這時候就需要用到 scanf() 幫我們把這一連串字元轉成一個整數，同時再把轉換的結果存進某個變數裡面，譬如
```C
scanf("%d", &x);
```
要再次強調的重點是使用 scanf() 時，如果輸入的資料要存入一般的變數中，變數前面要加 &；如果輸入的是字串，要存入字元陣列中，陣列名稱前不需要加 &。 所以如果是
```C
char name[10];
```
就只要
```C
scanf("%s", name);
```
我們再更深入地了解一下 scanf() 的運作方式。試試看底下的程式用各種不同格式輸入資料會產生什麼樣的結果。
```C
[範例 E04_06.c]
#include <stdio.h>
int main(void)
{
char name[10];
int x = -1;
scanf("%d", &x);
scanf("%s", name);
printf("x = %d\n", x);
printf("name: '%s'\n", name);
return 0 ;
}
```
1. 如果先輸入一堆空白字元 (例如按下空白鍵或 Enter 鍵)，然後再輸入一個數字，接著再打一堆空白字元，然後輸入一串字母，得到的結果會像下面這樣 (加底線的是使用者輸入的資料)：
```
輸入:   89 James
輸出:   x = 89
        name: 'James'
```
2. 假如沒有輸入數字，而是直接先輸入一串字母，則會得到類似下面的結果：
```
輸入: Andy
輸出: x = -1
     name: 'Andy'11
```
3.假如輸入數字但是緊接著又輸入字母，則會得到
```
輸入: 789Alex13 R
輸出: x = 789
     name: 'Alex13'
```
出四個字元寬度，而小數點後要顯示兩位小數。

整個運作的原則其實很單純。對於 %d 來說， scanf() 會忽略一開始輸入的空白字元，期待第一個數字出現，然後就開始讀取數字，一直到出現非數字的字元就停止，然後把數字所表示的十進位數值存到變數裡。對於 %s 來說，則是從第一個非空白字元開始讀，直到出現空白字元為止，把這中間所有字元當作一個字串存入字元陣列裡，同時還會在最後多加一個'\0' 放入字元陣列中，表示字串的結尾。

最後我們來看看 %* 在 printf() 和 scanf() 各有什麼作用。
```C
[範例 E04_07.c]
#include <stdio.h>
int main(void)
{
int width, precision;
double rate = 123.45;
printf("Enter a width and a precision: ");
scanf("%d %d", &width, &precision);
printf("rate: '%*.*f'\n", width, precision, rate);
return 0 ;
}
```
先讓使用者輸入想要顯示的浮點數的寬度和精確度(小數點後的位數)，把這兩個數據存在 width 和 precision 中，例如使用者輸入的分別是 8 和 3。然後在 printf() 裡用 %*.*f 的方式，按照 width 和 precision 所指定的
格式來顯示浮點數。所以 %*.*f 裡的兩個 * 號，會被取代成 %8.3f，所以 rate 這個變數就會顯示成' 123.450'。這樣的功能提供了更大的彈性，讓我們的程式可以動態地決定如何顯示資料。
接著再來看 scanf() 中使用 %* 有什麼不同的作用。
```C
[範例 E04_08.c]
#include <stdio.h>

int main(void)
{
int x;
printf("Please enter three integers:\n");
scanf("%*d %*d %d", &x);
printf("Only the last one is stored: %d\n", x);
return 0 ;
}
```
程式要求使用者輸入三個整數，但是在 scanf() 裡面，前兩個位置是用 %*d 格式，意思是忽略這兩個被輸入的整數。只有
第三個格式是標準的 %d，所以它才是真正對應到 &x，整個程式也只有記錄第三個被輸入的整數。

先猜猜底下的程式執行後，如果使用者輸入 Mississippi，程式會輸出什麼？
[練習 W04_04]
#include <stdio.h>
int main(void)
{
char word[46];
scanf("%45s", word);
printf("%.4s\n", word);
return 0 ;
}
因為 printf() 裡面用了 %.4s ，如果對照浮點數的用法 %.4f 表示顯示時精確度是 4，套用到字串上，意思變成只要顯示前四個字元，所以螢幕上會顯示 Miss 四個字元，而不是整個 word 字串 Mississippi。至於 scanf() 裡面用到 %45s 則是表示輸入的字串最長不超過 45 個字元 (第 46 個要留給 '\0')。

試著修改上面的程式碼，讓使用者輸入一個英文字，然後在 printf() 裡利用 %.*s 格式設定要輸出的字串長度，讓程式只輸出原本輸入的英文字的前半段，譬如 Mississippi 只輸出 Missi，或 university 只輸出 unive。

提示：用 strlen() 計算字串長度。

## [補充] 用 printf() 輸出字串時的格式設定　
各種用法的整理：

1. 如果寫
printf("%.3s\n", "university");
會輸出 uni 也就是只會輸出前三個字元

2. 如果寫
printf("%15s\n", "university");
會輸出 university 前面會多五個空格

3. 如果寫
printf("%15.3s\n", "university");
則會輸出 uni 也就輸出 university 的前三個字元 然後前面會多加入 12 個空格 總共長度是 15 個字元

4. 如果寫
printf("%*.*s\n", 15, 3, "university");
則是用動態的方式來設定格式 在這個例子得到的輸出結果也是 uni 也就是把兩個 * 號分別用 15 和 3 取代 相當於
"%15.3s\n" 的效果
