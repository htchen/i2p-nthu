# week 12 Standard library
### 內容：
map
list
猜數字
## Map
### 宣告
```c
#include <map>

std::map<key_value, mapped_value> Map_Name;
```

舉例：
```c 
#include <map>
#include <string>
using namespace std;

map<string, pair<int, string> > Map;
```
> Note:
>- key_value : string
>- mapped_value : pair<int, string>
>- Map_name : Map

### 性質
在 cplusplus.com 的講解：  

Maps are associative containers that store elements formed by a combination of a key value and a mapped value, following a specific order.   
  
In a map, the key values are generally used to sort and uniquely identify the elements, while the mapped values store the content associated to this key.   
  
> Note:
> 1. key value
> 2. mapped value
> 3. 存在 map 裡的東西會依照 key value 來排序
  
map 用中文來講了話，是一個能儲存東西的容器，裡面的東西都是一對一的（key value -> mapped value)，有點類似之前學的 array ，但 map 的 key value 不限於整數，而是任意型別的參數都行，但要注意==每一個 key value 都只能出現在 map 一次==，意即不可能有兩個以上的 mapped value 同時對應到同一個 key value  
  

Map reference : [Map - cplusplus](http://www.cplusplus.com/reference/map/map/)  

### 實作
題目：計算每個單字出現了幾次，並排序輸出。

這題我們先看 sample input 和 output 就很好理解了。
input
```
Hello World Bye World Hello Hadoop Goodbye Hadoop 
```
output
```
Bye 1 
Goodbye 1 
Hadoop 2 
Hello 2 
World 2  
```
#### step 1 宣告 map
```c++
#include<iostream>
#include<map>
#include<string>
using namespace std;
int main()
{
    map<string, int> Map;
}
```
我們定 map 的 `key value` 為 `string`，`mapped value` 為 `int` 
也就是說 `key value` 來存我們的單字，`mapped value` 來存他總共出現了幾次  
#### step 2 輸入
```c++
string key;
while( cin >> key)
{
    if ( Map.find(key) == Map.end())
    {
        Map[key] = 1;
    }else
    {
        Map[key]++;
    }
}
```
我們先看 if 的判斷條件 `if ( Map.find(key) == Map.end( ))`， 在 library 裡面， `.find( )`會用來找一個特定的東西，並依據結果回傳 iterator，如果他回傳得值為整個 map 的結尾了話（ `Map.end( )` or `Map.cend( )`），表示沒有找到  
  
map 也有類似 array 的用法，所以我們可以用 `Map[key] = 1`這種寫法來添加東西進入 map 裡面  
  
在中括號裡面的東西就是我們的 `key value`，而等號後面的就是所謂的 `mapped value`  

#### step 3 排序
就如同剛開始在介紹時也提到，map 會自動排序，所以這題我們並不需要多做任何的排序動作  
#### step 4 輸出
```c++
for ( auto i : Map)
{
    cout << i.first << " " << i.second << endl;
}
```
這題我們用前面教過的 range-based for loop 一一將 map 裡面的東西輸出，這時我們可以用 `.first` 跟 `.second` 分別將 key value 跟 mapped value 輸出  

#### 完整的程式碼
```c++
#include<iostream>
#include<map>
#include<string>
using namespace std;

int main()
{
    map<string, int> Map;
    string key;
    
    while( cin >> key)
    {
        if ( Map.find(key) == Map.end())
        {
            Map[key] = 1;
        }else
        {
            Map[key]++;
        }
    }
    
    for ( auto i : Map)
    {
        cout << i.first << " " << i.second << endl;
    }
    return 0;
}
```
#### 補充
在 map library 裡面除了上述的幾個功能外，還有許多實用的技巧，詳情請閱 Map reference : [Map - cplusplus](http://www.cplusplus.com/reference/map/map/)  

## List
### 宣告
```c++
#include <list>
std::list<T> List_name;
```
舉例：
```c++
#include<list>
using namespace std;

list<string> List;
```

### 性質
在 cplusplus.com 的講解：  
Lists are sequence containers that allow constant time insert and erase operations anywhere within the sequence, and iteration in both directions.  

List containers are implemented as doubly-linked lists; Doubly linked lists can store each of the elements they contain in different and unrelated storage locations. The ordering is kept internally by the association to each element of a link to the element preceding it and a link to the element following it.  
  
簡單來說，list 的功能好像之前我們寫的 doubly-linked list，但現在我們不用再自己寫模板了，直接 include library 就好，而且他有很多很方便的功能可以直接使用，詳情請自己看 [reference](http://www.cplusplus.com/reference/list/list/)  

Map reference : [list - cplusplus](http://www.cplusplus.com/reference/list/list/)  
### 實作
題目：There are four types of command:

- “1 integerA” represents inserting a node with int value A at the head of the list.
- ”2 integerB” represents inserting a node with int value B at the end of the list.
- “3” represents removing the node at the head of the list
- “4“ represents removing the node at the end of the list
  
Input terminated by EOF and finally output the list.  
  
原題目：[Problem 11010](http://acm.cs.nthu.edu.tw/problem/11010/)  
  
簡單來說，就是總共有四種指令，最後輸出整個 list  
原本這一題在 OJ 了話，是要我們去寫模板，但在這裡我們直接 include library 來寫  

#### 完整程式碼
```c++
#include <iostream>
#include <string>
#include <list>
using namespace std;
int main()
{
    list<int> List;
    
    int command, value;
    while (cin >> command)
    {
        if (command == 1)
        {
            cin >> value;
            List.push_front(value);
            
        }else if (command == 2)
        {
            cin >> value;
            List.push_back(value);
            
        }else if (command == 3)
        {
            if (!List.empty())
                List.pop_front();
        }else
        {
            if (!List.empty())
                List.pop_back();
        }
    }
    for (auto i : List)
        cout << i << " ";
}
```

## 猜數字
題目：大家小時候應該有玩過`幾Ａ幾Ｂ`這個遊戲吧，這題就要大家用 c++ 來寫幾Ａ幾Ｂ  

- Ａ 表示有數字放在對的位置
- Ｂ 表示有對的數字但位置錯了

舉例來說：  
正確答案：  
```
1 5 6 2
```
電腦猜：
```
1 4 2 0
```
那我們就輸入：
```
1 1
(代表 1 A 1 B)
```
### main function 
```c++
int main()
{
    
    GuessNumber gn;
    
    while (gn.size() > 1)
    {
        cout << gn.guess() << endl;
        cout << "? A ? B" << endl;
        int A, B;
        cin >> A >> B;
        
        gn.trim(A, B);
        if (gn.size()<10)
        {
            cout << "------" << endl;
            gn.show();
            cout << "------" << endl;
        }
        
        if (gn.size()==1)
        {
            cout << "I got it!" << endl;
            gn.show();
        }
        if (gn.size()==0)
            cout << "No way!" << endl;
    }
}
```
首先，我們先來討論 main function  
1. `GuessNumber` 是一個我們自定義的 class ，等等會介紹
2. `while (gn.size() > 1)` 是程式執行的條件，當 gn 裡面只有一組數字了話，表示他就是正解，所以程式可以停止了  
3. 程式一開始就先輸出一個 gn.guess ，讓使用者可以開始遊戲
4. gn.trim( )用來處理幾Ａ幾Ｂ的問題，把不符合的可能刪掉，只留下可能的結果
5. `if ( gn.size( ) < 10 ) ` 時就把所有可能輸出
6. `if ( gn.size( ) == 1 ) ` 時輸出 `I got it`
7. `if ( gn.size( ) == 0 ) ` 時表示使用者輸入的某部分出錯了，所以無解  

### GuessNumber class
```c++
class GuessNumber
{
public:
    GuessNumber(){...}
    void trim(int A, int B){...}
    string guess(){...}
    size_t size()
    {
        return solutions.size();
    }
    void show()
    {
        for (auto s: solutions)
            cout << s << endl;
    }
private:
    mt19937 rng;
    string my_guess;
    vector<string> solutions;
};

```
#### private : 
首先，我們先看 private 裡面的參數：
1. `mt19937 rng`：`mt19937` 是一種偽亂數的產生型別，使用時要先`#include <random>`，[更多有關 mt19937的解釋](http://www.cplusplus.com/reference/random/mt19937/)
2. `string my_guess` ：`my_guess` 是當下要輸出的猜測值，使用者會以這個猜測值為基礎，回應幾Ａ幾Ｂ
3. `vector<string> solutions`：`solutions` 是一個 string 的集合，裡面存有所有可能的猜測值，會隨著每次與使用者互動而減少，當裡面只剩一個猜測值時，那就是正確答案  
ps. 在這裡，我們將所有的猜測值都存成四位數的整數 string      
  
#### public :
#### 1. GuessNumber constructor：
```c++
    GuessNumber()
    {
        rng = mt19937(random_device{}());
        for (int i=0; i<10000; i++)
        {
            stringstream os;
            os << setfill('0') << setw(4) << i;
            solutions.push_back(os.str());
        }

        solutions.erase(remove_if(solutions.begin(), solutions.end(), [] (string& s)
                                  {
                                      for (int j = 0; j < 4; ++j)
                                      {
                                          for (int k = 0; k < j; ++k)
                                          {
                                              if (s[j] == s[k]) return true;
                                          }
                                      }
                                      return false;
                                  }), solutions.end());
    }
```
##### random_device 在做的事
我們在 constructor 裡面，用到了 `random_device{}()`，這是一種偽亂數的生成器，會產生一個偽亂數，[更多關於 random_device 的解說](http://www.cplusplus.com/reference/random/random_device/)  
ps. 在使用 `random_device{}()`時，我們必須先`#include <random>` 和 `using namespace std;`  
##### for loop 在做的事  
就如同剛剛所說的，所有猜測值都是被存成四位數，所以我們先在此先生成所有四位數，在這裡我們使用到`stringstream`這個功能，你可以把它想成一個字串轉換的方式，他能夠將將字串轉成其他形別，或將其他型別的東西轉成字串，一個在 c++ 很實用的功能，詳情請看[ stringstream reference](http://www.cplusplus.com/reference/sstream/stringstream/)  
  
#### `setfill('0') 搭配 setw(4)` 輸出
`setw(4)` 的功能就類似之前 c language 所學過的 `%4s`，它能夠設定輸出的欄位長度，而`setfill('0')`，則是能把剛剛`setw(4)`所空出來的空格改補 `'0'`進去  
舉例：  
```c++
#include <iostream>
#include <iomanip>
int main()
{
    for (int i = 0; i < 5; ++i)
    {
        cout << setfill('0') << setw(4) << i << endl;
    }
}
```
> Note:
> 使用`setfill( ) `和`setw()`前，要先 `#include <iomanip>`

##### solutions.erase( ) 在做的事
由於在 幾Ａ幾Ｂ 這個遊戲中，不會出現重複的數字，所以必須先把不符合格式的四位數刪除，在這裡我們採用的方法是用 `.erase( )` 搭配 `remove_if( )`來將不符合的結果刪除  
 
> Note:
> remove_if ( ) 格式
> ```c++
> remove_if(range_from, range_to, 篩選方法 );
> ```
> range_from 和 range_to 的型別都是 Iterator
> 以上面這個例題為例，篩選方法就是上面那個 lambda function


1. [vector :: erase( )參考資料](http://www.cplusplus.com/reference/vector/vector/erase/)
2. [remove_if( ) 參考資料](http://www.cplusplus.com/reference/algorithm/remove_if/)

#### 2. trim function
```c++
void trim(int A, int B)
{
    string cur = my_guess;
    solutions.erase(remove_if(solutions.begin(), solutions.end(), [=](string& str)
                                 {
                                     int AA = A, BB = B;
                                     for (int j = 0; j < 4; ++j)
                                     {
                                         if (str[j] == cur[j]) --AA;
                                         for (int k = 0; k < 4; ++k)
                                         {
                                             if (j != k && str[j] == cur[k]) --BB;
                                         }
                                     }
                                     if (AA == 0 && BB == 0)   return false;
                                     else                      return true;
                                 }), solutions.end());

}
```
這個 function 會根據使用者所傳進來的幾Ａ幾Ｂ，刪除掉不可能的結果  
> Note :
> lamdba function 中括號裡面符號代表的意思：
> - `[ ]` ：lambda function 外的參數並不會傳進 lambda function 裡
> - `[=]` ：在 lambda function 外的參數可被複製（bu value)進 lambda function
> - `[&]` ：外面的參數能被以 by reference 的方式傳進 lambda function
> - `[a, &b]` ：a 這個參數用傳值的方式， b 用 by reference 的方式傳進 lambda function


#### 3. guess function
```c++
string guess()
{
    uniform_int_distribution<int> dis(0,size()-1);
    my_guess = solutions[dis(rng)];
    return my_guess;
}
```
`uniform_int_distribution<>` 在 [cplusplus.com](http://www.cplusplus.com/reference/random/uniform_int_distribution/) 的解釋：Random number distribution that produces integer values according to a uniform discrete distribution  

簡單來說， 他是一個能設定亂數範圍的機制，接著我們統整一下從剛剛到現在所用有關 random 的語法：
```c++
#include <iostream>
#include <random>
using namespace std;
int main()
{
    for (int i = 0; i < 10; i++)
    {
        mt19937 rng = mt19937(random_device {}());// mt19937(...) 為強制轉型
        //mt19937 rng = (mt19937)random_device {}(); 這樣寫也可以
        
        uniform_int_distribution<int> dis(0, 100);// range : 0 ~ 100
        cout << i << " : " << dis(rng)<< endl ;
    }
    
    return 0;
}
```
output  
```
0 : 16
1 : 82
2 : 85
3 : 66
4 : 66
5 : 5
6 : 43
7 : 68
8 : 67
9 : 11
```
統整：  
1. `mt19937`：為 random 的型別
2. `random_device {}()`：為 random 的生成器（engine or generator )
3. `uniform_int_distribution`：為控制 random 所生成的範圍( Random number distribution)
相關資料：  
- [mt19937](http://www.cplusplus.com/reference/random/mt19937/)
- [random_device](http://www.cplusplus.com/reference/random/random_device/)
- [uniform_int_distribution](http://www.cplusplus.com/reference/random/uniform_int_distribution/)  


#### 4. 完整的程式碼
```c++
#include <iostream>
#include <iomanip>
#include <sstream>
#include <string>
#include <vector>
#include <algorithm>
#include <random>

using namespace std;

class GuessNumber
{
public:
    GuessNumber()
    {
        rng = mt19937(random_device{}());
        for (int i=0; i<10000; i++)
        {
            stringstream os;
            os << setfill('0') << setw(4) << i;
            solutions.push_back(os.str());
        }

        solutions.erase(remove_if(solutions.begin(), solutions.end(), [] (string& s)
                                  {
                                      for (int j = 0; j < 4; ++j)
                                      {
                                          for (int k = 0; k < j; ++k)
                                          {
                                              if (s[j] == s[k]) return true;
                                          }
                                      }
                                      return false;
                                  }), solutions.end());
    }
    void trim(int A, int B)
    {
        string cur = my_guess;
        solutions.erase(remove_if(solutions.begin(), solutions.end(), [=](string& str)
                                 {
                                      int AA = A, BB = B;
                                      for (int j = 0; j < 4; ++j)
                                      {
                                          if (str[j] == cur[j]) --AA;
                                          for (int k = 0; k < 4; ++k)
                                          {
                                              if (j != k && str[j] == cur[k]) --BB;
                                          }
                                      }
                                      if (AA == 0 && BB == 0)   return false;
                                      else                      return true;
                                  }), solutions.end());

    }
    string guess()
    {
        uniform_int_distribution<int> dis(0,size()-1);
        my_guess = solutions[dis(rng)];
        return my_guess;
    }
    size_t size()
    {
        return solutions.size();
    }
    void show()
    {
        for (auto s: solutions)
            cout << s << endl;
    }
private:
    mt19937 rng;
    string my_guess;
    vector<string> solutions;
};
int main()
{
    
    GuessNumber gn;
    
    while (gn.size() > 1)
    {
        cout << gn.guess() << endl;
        cout << "? A ? B" << endl;
        int A, B;
        cin >> A >> B;
        
        gn.trim(A, B);
        if (gn.size()<10)
        {
            cout << "------" << endl;
            gn.show();
            cout << "------" << endl;
        }
        
        if (gn.size()==1)
        {
            cout << "I got it!" << endl;
            gn.show();
        }
        if (gn.size()==0)
            cout << "No way!" << endl;
    }

}
```
 