# Water Jugs Problem （倒水問題）
在進入 Water Jugs Problem 之前，我們先來練習 library 的使用  
### 內容
map 應用  
vector 應用  
set 應用  
Water Jugs Problem（倒水問題） 
## map library 練習
雖然前面講過 map 了，但這裡再藉著 map library 替大家建立幾個重要的觀念  
  
先看看下面的程式碼，然後想想，如果 input 為  `3 7 4`，那 output 會為何  
```c++
#include <iostream>
#include <map>

using namespace std;
int main()
{
    map<int, int> Map;
    int n;
    while (cin >> n)
    {
        Map.insert(pair<int, int> (n, 0));
    }
    
    int i = 0;
    
    for (auto a : Map)
    {
        a.second = i++;
    }
    
    for (auto a : Map)
    {
        cout << a.first << " : " << a.second << endl;
    }
    
    return 0;
}
```
input  
```
3
7
4
```
output  
```
3 : 0
4 : 0
7 : 0
```
沒錯，輸出結果，不只已經排序過了，且`.second`的結果都是 `0`  
### 觀念一 map 的自動排序
上週的講義就有講過了， map 本身會自動根據 key value 來排序，所以在輸出的時候 key value 的順序才會變成  
```
3 : 0
4 : 0
7 : 0
```
而不是根據原先輸入順序輸出  
```
3 : 0
7 : 0
4 : 0
這是錯的！！
```
 
### 觀念二 map 的輸入
除了可以用之前所教的 `Map[key_value] = mapped_value; ` 這樣的輸入方法， map 本身也有 `.insert( )` 的功能，但裡面放的東西必須是成對的，也就是 (key_value, mapped_value）這樣，所以 insert 時，要使用 `pair` 來處理  

### 觀念三 大括號在 c++ 的意義
這部分可能是許多人在大一結束後，仍不知道的觀念（至少我認識的人幾乎都沒有這個觀念）  
  
大括號在 C++ 有==指定變數==的意義，所以在大括號裡面宣告的變數，有效區域僅限於大括號裡面，上面的 `a.second = i++`，明明已經 `i++` 了，但由於 a 是在 for loop 裡面的==局部變數==，所以他只會==複製==外面 Map 的值去進行修改，在離開大括號後 Map 又會變成原來的值   

簡單來說，你可以把它想成大括號的功用類似於之前 c language 所用的 function 傳變數的概念（僅複製過去而已）  

舉一個更明顯的例子：
```c++
#include <iostream>
#include <map>

using namespace std;
int main()
{
    int a = 3;
    
    {
        int a = 19;
        cout << "a in { } : " << a << endl;
    }
    
    cout << "a out of { } : " << a << endl;
    return 0;
}
```
Output : 
```
a in { } : 19
a out of { } : 3
```
這樣應該很明顯了吧  
  
接著改成這樣  
```c++
#include <iostream>
#include <map>

using namespace std;
int main()
{
    int a = 3;
    
    {
        a = 19;
        cout << "a in { } : " << a << endl;
    }
    
    cout << "a out of { } : " << a << endl;
    return 0;
}
```
輸出：
```
a in { } : 19
a out of { } : 19
```
由於這時的 a 並不是在大括號裡面宣告的，所以他不是局部變數，我們才能直接修改他  
  
回到本題目，如果想要讓輸出變成  
```
3 : 0
4 : 1
7 : 2
```
了話，在 for loop 宣告 a 的時候，就要使用 reference 的方法宣告才行，完整的程式碼如下：  
```c++
#include <iostream>
#include <map>

using namespace std;
int main()
{
    map<int, int> Map;
    int n;
    while (cin >> n)
    {
        Map.insert(pair<int, int> (n, 0));
    }
    
    int i = 0;
    
    for (auto & a : Map) // 差別在這裡！！！！
    {
        a.second = i++;
    }
    
    for (auto a : Map)
    {
        cout << a.first << " : " << a.second << endl;
    }
    
    return 0;
}
```
## vector library 練習
這一題主要讓大家練習一些 c++ 的語法，務必熟讀  
  
題目：Input some intergers and output them  
### step 1 input
```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
int main()
{
    vector<int> v;
    int n;
    
    cout << "Enter some integers ";
    while (cin >> n)
    {
        v.push_back(n);
    }
}
```
值得注意的是，`vector` 搭配 `.push_back( )`會自己去跟系統要記憶體，不用擔心不夠用  

### step 2 output
這裡我們會用四種不同的方式輸出  
#### 第一種： output with [ ]
```c++
for (int i = 0; i < v.size(); i++)
{
    cout << v[i] << ' ';
}
```

#### 第二種：range-based for loops
```c++
for (auto i : v)
{
    cout << i << ' ';
}
```
要注意 range-base for loops 需要在 `C++11`的環境下才能編譯  

#### 第三種：`for_each( )` 搭配 lambda function
```c++
#include <algorithm>

for_each(v.cbegin(), v.cend(), [](const int v)
             {
                 cout << v << " ";
             });
```

在使用 `for_each( )` 之前要 `#include <algorithm>`  

> Note : 
> ```c++
> for_each (range_from, range_to, 判斷式);
> ```
  
reference : [for_each - cplusplus.com](http://www.cplusplus.com/reference/algorithm/for_each/)


#### 第四種： use an iterator
```c++
for (auto it = v.cbegin(); it != v.cend(); ++it)
{
    cout << *it << ' ';
}
```
#### 完整程式碼
```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;
int main()
{
    vector<int> v;
    int n;
    
    cout << "Enter some integers ";
    while (cin >> n)
    {
        v.push_back(n);
    }
    
    //------------------------------------------------
    // 1st : output with [ ]
    //------------------------------------------------
    for (int i = 0; i < v.size(); i++)
    {
        cout << v[i] << ' ';
    }
    cout << endl;
    
    //------------------------------------------------
    // 2nd : range-based for loops
    //------------------------------------------------
    for (auto i : v)
    {
        cout << i << ' ';
    }
    cout << endl;
    
    //------------------------------------------------
    // 3rd : for_each 搭配 lambda function
    //------------------------------------------------
    for_each(v.cbegin(), v.cend(), [](const int v)
             {
                 cout << v << " ";
             });
    
    cout << endl;
    
    //------------------------------------------------
    // 4th : use an iterator
    //------------------------------------------------
    for (auto it = v.cbegin(); it != v.cend(); ++it)
    {
        cout << *it << ' ';
    }
    cout << endl;
    
    return 0;
}
```

## vector library 練習 2：Other type of vector
  
#### 1. 宣告一個 string 的 vector
```c++
vector<string> heroes {"Ironman", "Batman", "Superman", "Spiderman", "Thor"};
```

#### 2. sort  
```c++
sort(heroes.begin(), heroes.end());
```
reference : [vector :: sort - cplusplus.com](http://www.cplusplus.com/reference/algorithm/sort/)  
  
#### 3. random_shuffle( )
```c++
random_shuffle(heroes.begin(), heroes.end());
```
  
Rearranges the elements randomly  
reference : [random_shuffle - cplusplus.com](http://www.cplusplus.com/reference/algorithm/random_shuffle/) 

#### 4. functions in <algorithm>

```c++
all_of(heroes.cbegin(), heroes.cend(), [](string s){ return s.length() > 3; })
```
在 algorithm library 裡面還有很多好用的 function 可以呼叫，例如：  
  
- [`for_each( )`](http://www.cplusplus.com/reference/algorithm/for_each/)：apply all elements to the target function
- [`any_of( )`](http://www.cplusplus.com/reference/algorithm/any_of/)：If the function returns true for any of elements, it returns true, otherwise return false
- [`none_of( )`](http://www.cplusplus.com/reference/algorithm/none_of/)：if the function returns ==false== for all elements, it return true
- [`all_of( )`](http://www.cplusplus.com/reference/algorithm/all_of/)：if the function returns ==true== for all elements, it returns true


#### 完整程式碼
```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
int main()
{
    vector<string> heroes {"Ironman", "Batman", "Superman", "Spiderman", "Thor"};
    
    for (auto s : heroes)
    {
        cout << s << ' ';
    }
    cout << endl;
    
    cout << "Sorted: \n";
    
    sort(heroes.begin(), heroes.end());
    
    for (auto s : heroes)
    {
        cout << s << ' ';
    }
    cout << endl;
    
    
    cout << "Shuffle: \n";
    
    random_shuffle(heroes.begin(), heroes.end());
    for (auto s : heroes)
    {
        cout << s << ' ';
    }
    cout << endl;
    
    // functions in <algorithm>
    // all_of, none_of, any_of
    if ( all_of(heroes.cbegin(), heroes.cend(), [](string s){ return s.length() > 3; }) )
    {
        cout << "All names have more than three letters.\n";
    }
    else
    {
        cout << "false\n";
    }
    return 0;
}
```

## set library 練習


## Water Jugs Problem
