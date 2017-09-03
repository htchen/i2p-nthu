# Water Jugs Problem （倒水問題）
在進入 Water Jugs Problem 之前，我們先來練習 library 的使用  
## 內容
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
除了可以用之前所教的 `Map[ key_value] = mapped_value; ` 這樣的輸入方法， map 本身也有 `.insert( )` 的功能，但裡面放的東西必須是成對的，也就是 (key_value, mapped_value）這樣，所以 insert 時，要使用 `pair` 來處理  

### 觀念三 大括號在 c++ 的意義
這部分可能是許多人在大一結束後，仍不知道的觀念（至少我認識的人幾乎都沒有這個觀念）  
  
大括號在 C 和 C++ 有==指定變數==的意義，所以在大括號裡面宣告的變數，有效區域僅限於大括號裡面，上面的 `a.second = i++`，明明已經 `i++` 了，但由於 a 是在 for loop 裡面的==局部變數==，所以他只會==複製==外面 Map 裡所有的 `pair` 去進行修改，在離開大括號後 Map 又會變成原來的值   

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
### 性質：
在 cplusplus.com 的講解：
  
Sets are containers that store unique elements following a specific order.  

- 把資料放入 set，可以用 initializer list 的方式，在產生物件的時候，用 copy constructor 把資料複製到 set 裡面
- 也可以用 `insert()` 函數，把新的資料放入 set 裡面。
- 在 set 裡面找資料，也是用 `find()` 函數

範例的後半段，示範如何用 `next_permutation` 來產生不同的排列，而且把結果放入 `set<vector<int>>`，set 裡面的每個元素都是一個由 int 構成的 vector，。

範例的最後，則是示範如何用 `bitset` 來窮舉 powerset。
`bitset` 可以把整數轉成二進位表示法，我們可以透過查詢每個位元是 `0` 或 `1`，來決定是否要把對應到該位元的資料挑選出來。

```c++
#include <iostream>
#include <set>
#include <algorithm>
#include <bitset>
using namespace std;  // otherwise we need to write std::set

int main()
{
    set<int> S{1, 2, 3, 4};

    // insert a new element into a set
    for (auto c : S)
        cout << c << ' ';
    cout << '\n';
    cout << "insert 5: ";
    S.insert(5);
    for (auto c : S)
        cout << c << ' ';
    cout << '\n';

    // insert an existing element into a set
    S.insert(1);
    cout << "insert 1: ";
    for (auto c : S)
        cout << c << ' ';
    cout << '\n';

    // We use the following idiom to find an element in a set.
    auto x = S.find(3); // x is an iterator
    if (x != S.cend()) {
        cout << "Found: " << *x << '\n';
    }


    // Generate all permutations and store them in a set.
    vector<int> v{1,2,3};
    set<vector<int>> T;
    do {
        T.insert(v);
    } while ( next_permutation(v.begin(), v.end()) );

    for (auto c : T)
        cout << c[0] << ' ' << c[1] << ' ' << c[2] << '\n';
    cout << '\n';

    // using bitset to generate power sets
    vector<string> vs{"RED","GREEN","BLUE"};
    for (int i=0; i<8; ++i) {
        bitset<3> b(i);
        for (int j=0; j<3; ++j) {
            if (b[j]==1) {
                cout << vs[j] << ' ';
            }
        }
        cout << '\n';
    }


}
```

## Water Jugs Problem
需要一個基本的資料結構，用來描述每個水桶在某個時刻的狀態，`using State = vector<int>;`  
  
例如 :   
```
{3, 0}
```
代表第一個水桶裡面有三加侖的水，另一個則是空的  

## Step 1 定義 class  
接下來我們要定義 `class Pouring`，用來描述 Water Jugs Problem 的倒水過程  
```c++
private: 
    vector<int> _capacities; // {3, 5}
    set<State> _explored;
    set<list<State>> _paths;
    set<list<State>> _solutions;
```
- `_capacities` : 用來記錄每個水桶的容量上限  
- `_explored` : 用來標記已經出現過的狀態，避免重複  
- `_paths` : 則是一堆 `list<State>`的集合，`list<State>` 構成了一條路徑(一種解法、一種倒水順序)，我們把全部的路徑都放在 `_paths` 裡面，解題的過程中，可以取出任意一條路徑，然後試著往下走  
- `_solutions` 跟 `_paths` 一樣，不過只儲存能夠達成目標的解法  
  
> Note :  
>`class Pouring` 的 constructor 只需要把 `_capacities` 設定好  
## Step 2 
按照題目定義，我們可以做三種操作：`Empty`、`Fill`、`Pour`，把水桶的水倒光、把水桶的水裝滿、把某個水桶的水倒到另一個水桶。我們必須實做出這三項操作  
```
    State Empty(State s, int jug_no);
    State Fill(State s, int jug_no);
    State Pour(State s, int from, int to);
```

再來是 `set<State> extend(State s)` 函數，傳入的參數是某個狀態，然後依據狀態，用上述的三種操作，把狀態改變，然後把產生的新狀態，通通存入 `set<State>` 裡面

## Step 3 關鍵的 solve function 
`void solve(int target, int steps)`  

1. `target` : 想要達成的目標（某個桶子裡面有指定的水量）  
2. `steps` : 希望在幾部內解出答案。


## Step 4 解題觀念
1. For each path in the set of candidate paths  
     - Extract the last state of that path and  add it into the set of explored states  
   - Extend the last state to get a set of next states that have not been visited yet  
   - For each of the new next states, append it to the end of the current path and therefore obtain a new path  

2. If the new state is the target, we are done   

3. Otherwise , add the new path into the set of candidate paths  

對照程式碼會更清楚上述的步驟如何實現  


## 完整的程式碼
```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <set>
#include <list>
#include <iterator>
#include <string>
#include <sstream>
using namespace std;
using State = vector<int>;
class Pouring
{
private:
    vector<int> _capacities; // {3, 5}
    set<State> _explored;
    set<list<State>> _paths;
    set<list<State>> _solutions;

public:
    Pouring(vector<int> cp): _capacities{cp} { }
    State Empty(State s, int jug_no)
    {
        s[jug_no] = 0;
        return s;
    }
    State Fill(State s, int jug_no)
    {
        s[jug_no] = _capacities[jug_no];
        return s;
    }
    State Pour(State s, int from, int to)
    {
        State t = s;
        int diff = _capacities[to]-s[to];
        if (diff < s[from]) 
        {
            t[to] = _capacities[to];
            t[from] = s[from]-diff;
        } else 
        {
            t[from] = 0;
            t[to] = s[to] + s[from];
        }
        return t;
    }
    set<State> extend(State s)
    {
        set<State> SS;
        for (int i=0; i<_capacities.size(); ++i) 
        {
            SS.insert(Empty(s, i));
            SS.insert(Fill(s, i));
            for (int j=0; j<_capacities.size(); ++j) 
            {
                if (i!=j)
                    SS.insert(Pour(s, i, j));
            }
        }
        return SS;
    }

    void show_state(State s)
    {
        for (auto i : s)
            cout << i << ", " ;
        cout << "->";
    }
    bool found(State s, int target)
    {
        for (auto t : s)
        {
            if (t==target) return true;
        }
        return false;
        // return any_of(s.cbegin(), s.cend(), [=](int v){ return target==v;});
    }
    void solve(int target, int steps)
    {
        list<State> initialPath;
        initialPath.push_back(State(_capacities.size()));
        _paths.insert(initialPath);

        while (steps > 0) 
        {
            set<list<State>> newPaths;
            set<list<State>> oldPaths;

            for (auto p : _paths) 
            {
                _explored.insert(p.back());
                auto nextStates = extend(p.back());
                for (auto s : nextStates) 
                {
                    if (found(s, target)) 
                    {
                        auto np = p;
                        np.push_back(s);
                        _solutions.insert(np);
                    } else 
                    {
                        auto search = _explored.find(s);
                        if (search == _explored.cend()) 
                        {
                            auto np = p;
                            np.push_back(s);
                            newPaths.insert(np);
                        }
                    }
                }
                oldPaths.insert(p);
            }

            for (auto p : oldPaths)  
            {
                _paths.erase(p);
            }
            for (auto p : newPaths) 
            {
                _paths.insert(p);
            }
            --steps;
        }

    }
    void show_solutions()
    {
        for (auto path : _solutions)
        {
            for (auto state : path) 
            {
                show_state(state);
            }
            cout << "\n";
        }
    }
};

int main()
{
    vector<int> jugs = {3, 5};
    Pouring problem(jugs);
    problem.solve(4, 6);
    problem.show_solutions();
}

```

