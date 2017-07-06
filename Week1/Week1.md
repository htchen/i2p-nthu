# C 程式設計簡介
## 程式編寫流程
- text editor  
<br>是用來寫程式的純文字編輯器，通常還會有一些額外的輔助功能，譬如縮排或改變關鍵字顏色，讓你的程式碼看起來更一目了然。</br>
- compiler  
<br>是程式語言的核心工具，用來將你寫的 C source code 變成一堆要讓電腦執行的指令。由於我們要使用的程式開發工具 Visual C++、Dev C++ 或 Code::Blocks 所包含的 compiler 其實可以編譯 C 或 C++ source code，不過這兩種語言還是有一定程度的差異，所以必須強迫 VC++ 或 Dev C++ compiler 使用 C 語言的語法來編譯你的 source code。只要你的source code 檔案存檔時選擇用的副檔名是 ".c"， VC++ 或 Dev C++ 就會用 C compiler 來編譯你的程式。VC++使用的是 Microsoft 自行開發的 compiler，而 Dev C++ 和 Code:Blocks 則通常是用公開原始碼的 MinGW GCC。 另外，在 Dev C++ 裡可以從主選單裡選 [Tools]→[Compiler Options]→[Settings] 然後在 C compiler 的項目裡把 "Support all ANSI standard C programs" 項目選成 "Yes"。</br>
- linker  
<br>是用來將編譯好的 object code 連結成最後的執行檔。因為除了我們自己寫的程式碼之外，無可避免一定會用到許多現成的 library (函式庫)。在現實的情況下，不可能每一東西都要靠自己創造，所以絕大多數的時候我們都會使用內建的或別人寫好的函式庫，來達到自己想要的功能。 linker 就是用來把我們的程式碼和需要的函式庫裡的程式碼連結在一起，形成單一的可執行檔。在 Windows 裡，執行的的副檔名為 ".exe"。</br>    
## C語言簡介
```C
#include <stdio.h>int main(void) /* display an integer on screen */
{int num;num = 40;
printf("The number is %d.\n", num);return 0;
}
```
這個簡單的程式會在螢幕上顯示一串文字 The number is 40. 。這個程式包含了每個程式幾乎都會有的幾個主要結構。我們一行一行來解釋它們的作用。程式開頭第一行

```#include <stdio.h>```

使用 #include 來將 stdio.h 檔案的內容加到你的程式的最前面。因為 stdio.h 檔案裡包含了許多和輸入輸出 (I/O) 有關的東西，當我們需要用到標準的輸入輸出功能時，只要用 #include 的方式把 stdio.h 引入，就不用重打 stdio.h 的內容。stdio.h 這個檔案包含在你安裝的 C compiler 中，可以試著搜尋 Dev C++ 或 VC++路徑裡的 stdio.h 檔案，然後用 Dev C++ 或其他純文字編輯器 (例如 notepad 或 UltraEdit) 打開來看看裡面有多少內容。接下來看下一行

```int main(void)```

基本上 C 程式可以看成是許多 functions 的集合，每個 function 會負責某項工作，依照輸入的參數對資料作處理，然後將結果傳回。其中最重要的而且一定要包含的 function 就是 main()，它是你的程式的進入點，當你的程式開始執行時，首先就會執行 main()，然後在 main() 裡面再去呼叫其他unctions。我們在接下來的課程會在詳細介紹 C functions，目前只要先記得你的程式裡一定要有一個名稱是 main() 的 function。在 main() 前面的 int 表示 main() 結束後回傳值(return value) 的型別是 int，也就是整數 (integer)。這個回傳值會傳回給 function 的呼叫者。因為我們是在作業系統下執行你的程式，所以在這個例子裡 main() 會把值回傳給作業系統。另外，在括號中的 void 表示這個程式的 main() 在呼叫時不需要傳入任何參數，我們稍後也會再介紹如何讓 main() 從作業系統中取得參數。

這個範例還包含了註解 ```/* display an integer on screen */ ```所有被包含在 ```/* ```和 ```*/ ```之間的文字，都會被 Ccompiler 忽略。註解的主要作用讓我們能在程式碼片段中夾雜文字說明，幫助撰寫或修改程式的人看懂程式碼。當程式碼越寫越多，程式變得很複雜的時候，註解可以讓我們更快速了解每段程式碼的意義。

接下來會看到波浪括號 { ... }，中間夾著 main() 的內容。波浪括號的作用相當於標示出 main() 的勢力範圍。當你的程式還有其他 functions 時，也是用 { } 來決定每個 function 涵蓋的範圍。

再來看到的是 int num; 這行程式，這是所謂的變數宣告，我們要讓compiler 知道在 { } 範圍內，將會用到一個整數(integer) 變數 num。 前面提過 int 代表 integer， num 則是我們自己取的變數名稱。int 屬於的 C keywords 之一，而每個 keyword 都有特殊的意義，所以在替變數取名時要注意不要和這些 keywords 重複。(如果想先知道還有哪些keywords，可以在網路上搜尋 "C keywords" 就會找到全部的 keywords 列表。) 在 C 裡面每個被用到的變數都必須先宣告過，如果忘了先宣告變數就直接使用，在 compile 的時候就會出錯。除了不能和已有的 keywords 重複之外，變數的取名還有一些限制。變數的名稱只能包含連續的大小寫英文字母、 _ (底線)、以及數字等三類字元，而且只能用英文字母或底線當作第一個字元，變數開頭不能用數字，例如，例如： num_dogs, B612, iPod，都是合法的變數名稱 。變數名稱的長度最長不能超過 63 個字元，但是其實只有前 31 個字元是有效的，超過的部份會被忽略。C 語言會區分字母的大小寫，所以u2 和 U2 代表兩個不同變數。寫程式時最好能使用固定命名規則。可以上網搜尋"Naming Conventions(programming)"，參考慣用的規則來替變數和 function 取名。最後要特別注意的是，整個敘述句後面要用分號 ; 來結尾，不能省略。剛開始寫程式時很容易因為忘記加分號而在 compile 時得到錯誤訊息。

```num = 40; ```這個敘述是所謂的 assignment statement，用來把變數 num 的值設定成 40。因為已經宣告過 num 是個整數變數，在記憶體會有個位置存放它的值。等號的意義就是把右邊的值放到左邊的變數裡。結尾同樣要有分號。

```printf("The number is %d.\n", num); ```這個敘述句呼叫了 printf() 這個 function。在剛開始學 C 的階段，printf() 會是我們最常用到的 function 之一，它的作用是把文字顯示在螢幕上。我們必須把想要顯示的字串當作參數傳給 printf()，在這個例子裡，我們傳了兩個參數給 printf()，兩個參數用逗號隔開。第一個參數是
```“The number is %d.\n”```，第二個參數則是 num。然後 printf() 會把這兩個參數做適當的結合，把第一個參數裡的 %d 用 num 變數目前的值 (40) 取代，再把結果顯示在螢幕上。
第一個字串參數裡還有一個奇怪的東西 ```\n```， 它代表換行字元，你可以試著把它去掉看看程式執行結果會有什麼不同。另外可以試試看如果把那一行打成下面這種形式 (在程式碼中直接按 Enter 鍵換行) 會發生什麼結果：
```C=printf("The number is %d.", num);```

在 compile 的時候應該會出現錯誤訊息。由此可見，```\n``` 是為了描述一些難以輸入的字元而設計的變通表示法 (稱作escape sequence)，類似的字元還有 ```\t``` 表示 tab 空格， ```\b``` 表示 backspace，```\"```表示雙引號，```\\```表示反斜線。
我們再進一步解釋 %d 這個用法，它的作用是告printf() 這個地方將會顯示一個整數，而且要用十進位 (decimal) 方式顯示。這樣的用法稱作格式化輸出，printf() 還有更多格式化輸出的選擇，我們會在稍後的例子裡一一介紹。

最後來到了 return 0; 這個敘述句。我們前面說過 main() 需要回傳一個整數給作業系統，這也是 main() 前面的 int 所代表的意義。既然已經指定要回傳整數，所以在程式結束時要用 return 這個 keyword 來指出我們要回傳的值是多少。在這個例子回傳值是 0，至於它所代表的意義我們後續會再做介紹，目前暫時先忍耐一下，容許你的腦中有一些不明確的東西存在。

到目前為止，我們已經學會了最基本的 C 程式結構：

```C
#include <stdio.h>
int main(void){
<statements>
return 0;}
```

在我們前幾週的課程中會不斷地用到類似的結構。剛開始學寫程式，都有一些固定的模式可以套用，只要照著把固定的程式碼輸入就可以，真正需要變動 (設計) 的部份是 <statements> 那一段，這部份就要依照我們想達到的功能，填入適當的程式碼。

```C
#include <stdio.h>
int main(void)
{
int feet;int inches;int centimeters;
feet = 6;inches = 3;
centimeters = (feet*12 + inches) * 2.54;
printf("%d feet %d inches = %d centimeters.\n", feet, inches, centimeters);
return 0;
}
```

變數的數目增加成三個，一樣都是整數變數。這個範例還多了乘法和加法運算，把英制呎吋換算成公分。  
變數宣告的部份，由於三個變數都是整數，也可以用另一種寫法，把它們合在一起宣告，看起來稍微簡潔一點：  

```C
int feet, inches, centimeters;
```

要注意變數之間是用逗號隔開，最後再用分號結束。  

主程式 <statements> 的部份，首先是設定 feet 和 inches 的值，然後再依照換算公式 (星號 * 代表乘法運算)，一呎等於 12 吋，一吋等於 2.54 公分，把運算的結果設定給 centimeters，所以 centimeters 就會記錄著換算出來的公分是多少，最後再呼叫 printf() 把結果顯示在螢幕上。這次為了顯示三個變數值，總共需要傳四個參數給 printf()，第一個是描述顯示格式的字串，裡面包含了三個 %d，分別對應到後面三個參數 `feet`, `inches` ,` centimeters`，到時候這三個變數所儲存的值就會顯示在對應的位置，而且都是以十進位方式顯示。接下來我們可以更改 ```feet``` 和 ```inches``` 的值，但是要重新 ```compile``` 產生新的執行檔，再執行才會得到新的換算結果。
## Debuger ##
