# C++ Intro 

## C++簡介

C++可以說是C語言的衍伸，C++標準程式庫是以C的標準程式庫少許修改而成。和C語言最大的不同之一，就是C++是一個物件導向程式。再比較早期的時候，C++被稱作 "C With Classes" ，就可以看出C++是以C為基礎，再加上其他標準的一種語言。以上都是較簡略的介紹，上網查查會找到更多資料

C++至今也一直不斷的演變，從C++11到C++14，再到目前的C++17，都增加不少新 features，由於C++的內容實在有點多，要全面熟悉也必須靠不斷的實作和應用

由於剛開始接觸C++，我們就先以 "What's New?" 做為主軸來認識C++。這個標題除了有 “比較C++和C的不同之處“ 的意涵之外，也想點出C++最近有什麼進展和演變。由於採用舉例說明的方式來介紹，所以內容可能會比較片面，不足的地方就要靠自己上網 Google 補足囉！現在我們不需要像一開始學C語言時連 printf 都要介紹了

## What's New

### 編譯環境與編譯參數

在 Windows 環境，需要先下載編譯器，在 Linux 或 MacOS 因為已經內建，所以不需要特別下載。

要編譯C++，需要在 command line 裡面多加入 -std=c++11 。例如

```
g++ myprog.cpp -std=c++11 -o myprog
```

若已經有最新的 clang++ 或 g++ ，就可以試試 C++17 。參數則改成-std=c++1z 。例如

```
g++ myprog.cpp -std=c++1z -o myprog
clang++ myprog.cpp -std=c++1z -o myprog
```

### cin, cout

在C語言，我們學過輸入和輸出是用 scanf 和 printf，在C++當然也是可以通用。但是C++多了 cin 和 cout 的用法。scanf 和 cin 的功能差不多，但是結構卻不同

```c++
cin >> 變數1 >> 變數2 >> 變數3;
```

上面的式子是讓使用者輸入三個變數，並依序將輸入的值放入變數1,2,3中。我們可以看到`>>`這個符號，由兩個大於符號組成，叫做 insertion operator。可以看成將輸入的數「insert」到變數裡面

cout 的用法和 cin 很像

```c++
cout << 變數1 << 變數2 << 變數3 << endl;
```

在這邊箭頭的方向相反了，變成兩個小於符號，可想成將變數「insert」到 cout 這個標準輸出函數裡面，cout 就會依序相三個變數輸出在螢幕上。`endl`代表換行，也可以用"\n"代替。 cout 也可以輸出字串，只需要用雙引號將字串括起來即可

```c++
cout << "Hello, World!\n";
cout << "Hello, World!" << endl;
```


## Hello, World!

就讓我們開始第一個C++程式吧

第一件事還是 Hello, World! 。可以看到不同的地方是 ` #include <stdio.h>`變成了`#include <iostream>`，然後是用`std::cout`來輸出。`std::`是用來指定 cout 的 namespace 是屬於 standard library ，這樣的用意是避免同名的衝突。如果在程式碼前面加上`using namespace std;`，那之後使用 standard library namespace 底下的東西時就可以省略開頭的`std::`

```c++
#include<iostream>
int main(void)
{
    std::cout << "Hello, World!\n";
}
```

```c++
#include<iostream>
using namespace std;
int main(void)
{
    cout << "Hello, World!\n";
}
```

輸出：

```
Hello, World!
```

### Namespace

既然有 std 的 namespace，那我們可不可以自己創造 namespace 呢？

當然可以。其實 namespace 最重要的目的是讓程式中的名稱可以有區別。寫大型程式時變數名稱常常會重複，尤其是當程式開發者不只有一個人時，互相設置的變數名稱就更有可能會重複而造成程式錯誤。這時就可以使用 namespace 做區隔。宣告方式很簡單

```c++
namespace name{
	...
}
```

以下範例變數和函式名稱雖然相同，但是卻是不同 namespace 的成員，也有正確的被引用，所以程式並不會有錯誤

```c++
#include <iostream>
using namespace std;
namespace Troy{
	int var;
	int calculate(int a, int b){
        return a+b;
    }
}
namespace Eric{
	int var;
  	int calculate(int a, int b){
        return a-b;
    }
}

int main(void){
    Troy::var=2;
  	Eric::var=1;
  	cout << Troy::calculate(Troy::var, Eric::var) << endl
    	 << Eric::calculate(Troy::var, Eric::var) << endl;
  	return 0;
}
```

用 `using namespace std;`的話，之後使用 std 底下的成員時就都不用特別指定，其實也可以適用在 namespace 裡面的成員。例如 `using std::cout;`，這樣在使用 cout 時就不需要再加上`std::`，但是在使用 namespace std 內的其他成員時還是需要用`std::`。

### String

在C++裡面，有屬於 standard library 的 string 資料型態可以用，所以可以視為內建。在複製 string 時比在C時簡單很多，用+就行了

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string name, ans;
    cout << "Hi! My name is Hal." << "What's your name?\n";
    cin >> name;
    ans = "Nice to meet you, " + name + ".";
    cout << ans << endl;
    cout << "Your name has " << name.size() << " characters." << endl;
}
```

### auto

在C++裡面，有一個很好用的功能，就是可以將變數的型別設成 auto ，編譯器會幫我們做 auto type inference ，依照前後文決定變數的型別，例如`auto i=5;`或`auto j=2.3`，這樣 i 的型別會自動被設為 int ， j 會被設為 double 。這樣的功能可以非常好用，請看下面的範例

### Range-based for loop 

```c++
#include <iostream>
#include <vector>
#include <iomanip>
using namespace std;
int main()
{
    vector<int> v{1,2,3,4,5};
    for (auto i : v)
    cout << setw(3) << i;
}
```

```c++
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<string> vs{"Tom", "John", "Amy", "Cathy"};
    for (auto n : vs)
        cout << n << endl;
}
```

vector 是 standard library 裡面定義的 container，`vector<int> v;`代表 v 是由 int 構成的 vector，裡面元素的型別是 int。同理`vector<string> vs;`的元素是 string

在C++可以使用之前的 C for loop的寫法，但是除此之外，C++也提供 range-based for loop，範例中的 for，會循序取出 vector 裡面的元素。這樣的寫法搭配 auto 變數，可以當成片語來使用，也會更為方便。例如，當我們需要用 for loop 掃過一個 `vector<pair<int, pair<float, float>>`等等比較複雜的資料型態時，我們就不用手動把 vector 元素的資料型態打出來。這樣的方法比起以往在 C 需要用額外的 index 存取 array 元素的寫法來的簡潔許多，也不用擔心 index 超出陣列範圍。

### for loop

在C++裡寫 for loop 時，有一種較特別的寫法，計數的變數可以在迴圈內宣告。這個在迴圈內宣告的變數只存在在迴圈內，迴圈結束就會消失

```c++
for(int i=0; i<10; i++){
 	...
}
```

### decltype

簡單的說，decltype可以查詢變數的類型，並可以宣告變數，其宣告的變數型態和查詢到的變數形態相同。它的功用和 auto 很像，但是有些細節卻不太一樣，使用時需要多注意。請看下方範例

```c++
int a;
decltype(a) b;	        //type of b is int
auto c = a;             //type of c is int

const float d = 2.3;
decltype(d) e = 5.1;    //type of d is const float
auto f = d;             //type of f is float
```

decltype 也有延伸的應用，例如

```c++
int a=1;
float b=2.3;
decltype(a+b) c = a+b;	//type of c is float
decltype(a>b) d;	    //type of d is bool
```


### Reference &

C++多了一種變數類型，叫做 reference。通常是用在函數傳遞參數，可以達到 call-by-reference 的效果。回憶一下，在 C 語言的函數呼叫，都是 call-by-value，函數呼叫時傳遞的都是參數的值，將值放進 stack。這時如果在函數中對值修改並不會影響到原本的參數(以底下的 swap 為例)。也因為如此，若想要在函數中改變原本的變數，需要傳遞變數的記憶體位置，達到 call-by-reference 的效果。在C++裡則直接提供能夠達到 call-by-reference 的機制。可以對照兩個範例的差易

```C++
#include <iostream>
using namespace std;
void swap(int i, int j)
{
    int t = i;
    i = j;
    j = t;
}
int main() {
    auto x = 5, y = 7;
    cout << "(" << x << "," << y << ")\n";
    swap(x, y);
    cout << "(" << x << "," << y << ")\n";
}
```

輸出：

```
(5,7)
(5,7)
```

```c++
#include <iostream>
using namespace std;
void swap(int& i, int& j)
{
    int t = i;
    i = j;
    j = t;
}
int main() {
    auto x = 5, y = 7;
    cout << "(" << x << "," << y << ")\n";
    swap(x, y);
    cout << "(" << x << "," << y << ")\n";
}
```

輸出：

```
(5,7)
(7,5)
```

差別只是參數的型別從 int 變成了 int &。Reference 相當於是幫變數取了一個代稱，所以在 swap 函數裡面的 i 就相當於是 main 裡面的 x ，j 相當於 main 裡面的 y 

除了參數，回傳值也可以用 reference。但是使用時需要多加注意，請看下方範例

正確：

```c++
#include <iostream>
using namespace std;
int& max(int& i, int& j)
{
    return (i>j) ? i : j;
}
int main() {
    auto x = 5, y = 7;
    cout << x << " " << y << "\n";
    max(x, y) = 0;
    cout << x << " " << y << "\n";
}
```

錯誤：

```c++
#include <iostream>
using namespace std;
int& max(int& i, int& j)
{
    int m;
    m = (i>j) ? i : j;
    return m;
}
int main() {
    auto x = 5, y = 7;
    cout << x << " " << y << "\n";
    max(x, y)=0;
    cout << x << " " << y << "\n";
}
```

原因是當韓式結束之後，函數原本記錄在 stack 裡面的局部變數都不能再被使用，所以把局部變數的 reference 傳回去是沒有意義的，會造成程式錯誤，編譯時就不會通過。除此之外，若不希望回傳回去的 reference 再被更改，可以把函數改為 `const int& max(int& i, int& j)`，這樣一來如果使用`max(x, y)=0;`，在編譯時就會提供錯誤訊息，讓程式設計者知道程式不對

### Lambda functions

C++11將 lambda functions 納入標準之中，所以C++可以寫出沒有名字的函數，讓很多應用變得方便許多

```c++
// -std=c++11  or   -std=c++14   or   -std=c++1z
#include <algorithm>
#include <iostream>
#include <vector>
#include <iomanip>
using namespace std;
int main()
{
    vector<int> v;
    for (int i = 1; i < 10; ++i) v.push_back(i*5);
    for (auto i : v) cout << setw(5) << i;
    cout << endl;
    for_each(v.begin(), v.end(), [] (int n) {
        if (n % 2 == 0) {
            cout << n <<" is even " << endl;
        }
    });
    for (const auto & y : v) cout << setw(5) << setfill('#') << y;
    cout << endl;
    for (auto & y : v) y = y+1;
    for (auto y : v) cout << setw(5) << setfill('*') << y;
    cout << endl;
}
```

輸出：

```
    5   10   15   20   25   30   35   40   45
10 is even 
20 is even 
30 is even 
40 is even 
####5###10###15###20###25###30###35###40###45
****6***11***16***21***26***31***36***41***46
```

在一開始需要引入好幾個 header，其中`<algorithm>`是為了要用到的 for_each，`<iomanip>`則是為了設定 cout 的格式。程式碼一開始用 for 把資料 push_back  到 vector 裡面，之後再將 vector 的資料一一顯示出來。其中 setw() 是`<iomanip>`提供的函數，作用是將 cout 的寬度固定在5格，所以顯示出來的每筆數字都會占5個字元，不足的部分會補上空白。接下來的 for_each 函數，用來將 vector 的元素一一帶入指定的函數之中。for_each的前兩個參數用來指定 vector 要拿來處理的範圍，第一個參數代表起始位置，第二個參數則代表結束位置。for_each的第三個參數就是要被帶入的函數，在範例中就是用 lambda functions 的方式，直接定義要被帶入的函數。語法如下，和一般的函數相差不大，只是不需要寫名字，只要用[ ]就行了

```c++
[] (int n) {
	if (n % 2 == 0) {
    	cout << n <<" is even " << endl;
    }
```

當然也可以不要用  lambda functions，改成這樣就可以了

```c++
// -std=c++11  or   -std=c++14   or   -std=c++1z
#include <algorithm>
#include <iostream>
#include <vector>
#include <iomanip>
using namespace std;
void print_even(int n){
    if (n % 2 == 0) {
        cout << n <<" is even " << endl;
    }
}
int main()
{
    vector<int> v;
    for (int i = 1; i < 10; ++i) v.push_back(i*5);
    for (auto i : v) cout << setw(5) << i;
    cout << endl;
    for_each(v.begin(), v.end(), print_even);

    for (const auto & y : v) cout << setw(5) << setfill('#') << y;
    cout << endl;
    for (auto & y : v) y = y+1;
    for (auto y : v) cout << setw(5) << setfill('*') << y;
    cout << endl;
}
```

### More Lanbda Functions

```c++
#include <iostream>
#include <functional>
#include <math.h>
#include <iomanip>
#define TOL 0.00001
using namespace std;
void print(function<double(double)> f, double x)
{
    cout << "f(" << fixed << setprecision( 5 )  << x << ") = " << f(x) << endl;
}
void print(double x)
{
    cout << "ans = " << x << endl;
}
double fixed_point(function<double(double)> f, double guess)
{
    auto close_enough = [](double v1, double v2)
        { return fabs(v1-v2)<TOL; } ;
    auto next = f(guess);
    if (close_enough(next, guess)) return next;
    else return fixed_point(f, next);
}
double mysqrt(double x)
{
    return fixed_point([x](double y){return (y+(x / y))/2.0; }, 1.0);
}
double mycbrt(double x)
{
    return fixed_point([x](double y){return (y+(x / (y*y)))/2.0; }, 1.0);
}
int main() {
    auto x = 2.0;
    print(mysqrt, x);
    print(mycbrt(5.0));
}
```

