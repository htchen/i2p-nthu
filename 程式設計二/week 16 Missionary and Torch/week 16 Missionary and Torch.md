## C++ Missionary and Torch
學習完上堂課教的 water jugs problem 之後，這週我們可以接著看下面兩個類似的經典範例  

### 範例一：`Missionary.cpp`
Missionary 問題：有三個傳教士和三個食人族要渡過一條河，而河中有一條船，船只能容納兩個人。而且在任何一個地方（無論是岸邊還是船上），如果食人族的數量多於傳教士的數量，食人族就會吃掉傳教士。怎樣才能讓這些人全都安全過河？（來回的船上都必須要有人操作）  
有關問題的解法可以參考附件 missionary.pptx，認識一下我們要解決的問題  

程式的一開始，我們需要一個基本的資料結構，用來描述每個時刻中傳教士、食人族和船的狀態，這裡我們用 `using State = vector<int>;`  
例如：{2,1,0,0,1} 代表的是左岸有兩個傳教士和一個食人族且船停在左岸；{1,2,2,3,-1} 則代表是有一個傳教士和兩個食人族在左岸，兩個傳教士和三個食人族在右岸且船停在右岸，前面兩個數字分別代表傳教士和食人族在左岸的個數，第三個和第四個數字則是代表右岸的個數，最後一個數字代表的是船的停靠位置，1 代表船停在左岸，-1 代表停在右岸   

接著我們要定義 `class Crossing`，用來描述整個過河的過程，整個 class 中包含幾個 private member：
```c++
vector<int> _npeople;
set<list<State>> _paths;
set<State> _explored;
set<list<State>> _solutions;
```
- `_npeople`：用來記錄一開始有幾個傳教士和幾個食人族要過河  
- `_paths`：為一堆 `list<State>` 的集合，由 `list<State>` 構成的路徑(一種解法或是一種過河順序)，我們把全部的路徑都放在 `_paths` 裡面，可以在解題的過程中任意取出一條路徑，然後試著往下走  
- `_explored`：用來記錄已經出現過的 state，避免一直重複同一個 State  
- `_solutions`：和 `_paths`一樣用來記錄路徑，不過只用來紀錄能夠成功過河的解法  
- `class Crossing` 的 constructor 只需要把 `_npeople` 設定好
```c++
Crossing(vector<int> np): _npeople {np} { }
``` 
接著我們需要寫出 `Go` 這個 function，用來產生每次狀態改變後的結果  
```c++
State Go(State s, int missionary, int cannibal)
    {
        s[0] = s[0] - s[4]*missionary;
        s[1] = s[1] - s[4]*cannibal;
        s[2] = s[2] + s[4]*missionary;
        s[3] = s[3] + s[4]*cannibal;
        s[4] = -s[4];
        return s;
    }
```
s[0]、s[1] 分別是傳教士和食人族在左岸的個數；s[2]、s[3]則是傳教士和食人族在右岸的個數，而 s[4] 是用來記錄船當下的位置，1 代表停在左岸，-1 則停在右岸，所以我們可以將傳教士和食人族過河後的狀況寫成如上述程式碼，並將改變後的狀態回傳回去  

完成可以產生 `State` 的 function 後，我們需要另一個 function 來檢驗我們新產生的 `State` 是不是合法的，所以我們必須寫一個 `valid` function 來檢驗  

```c++
bool valid(State s)
    {
        if (s[0]<0 || s[1]<0|| s[2]<0 || s[3]<0) return false;
        if (s[0]<s[1] && s[0]!=0) return false;
        if (s[2]<s[3] && s[2]!=0) return false;

        return true;
    }
```
這裡我們寫一個回傳 `bool` 型別的 function  
按照題目的規定，在兩岸的食人族人數都不能多於傳教士且人不會是負值，所以如果發生上述的情況就回傳 false，其他則回傳 true，寫法就像上面的程式碼  

再來我們需要寫一個 function 讓我們可以從我們現在的 State 延伸到新的 State，這裡我們寫一個 `extend` function  
```c++
set<State> extend(State s)
    {
        set<State> nextStates;

        for (int m=0; m<=2; m++) {
            for (int c=0; c<=2; c++) {
                if (m+c>=1 && m+c<=2) {
                    State ss = Go(s, m, c);
                    if (valid(ss))
                        nextStates.insert(ss);
                }
            }
        }
        return nextStates;
    }
```
由於每個 State 可以延伸出來的 `State` 不只一種，所以我們寫一個 `set<State>` 型別的 function 來記錄所有延伸出去且可行的 `State`，可以對照上面的局部程式碼，我們用 `nextStates `來記錄所有可以延伸出去的 `State`，接著我們寫雙層 for 迴圈，令 m 代表傳教士，c 代表食人族，由於一艘船一次最多只能載 2 個人，所以 m 和 c 的範圍是 `0 < m、c <= 2`，接著我們將產生的結果傳進剛剛寫好的 Go function 和 valid function 中，如果合法我們便將其存進 `nextStates` 中，最後回傳 `nextStates`  

我們還需要一個 function 讓我們可以判斷是不是所有人都過河了，這裡我們寫一個 `bool found(State s)`，當左岸的傳教士和食人族都為 0 且船停在右岸的時候就代表已經找到結果了，寫法如下：  
```c++
bool found(State s)
    {
        if (s[0]==0 && s[1]==0 && s[4]==-1) return true;
        else return false;
    }
```
最關鍵的一步，我們要完成 function `void solve(int steps)`，steps 是希望在多少步驟之內解出答案，局部程式碼如下：  
```c++
    void solve(int steps)
    {
        list<State> initialPath;

        State ss{_npeople};
        ss.push_back(0); 
        ss.push_back(0); 
        ss.push_back(1); 
        initialPath.push_back(ss);
        _paths.insert(initialPath); 

        while (steps>0) {
            set<list<State>> newPaths;
            set<list<State>> oldPaths;

            for (auto p : _paths) {
                auto nextStates = extend(p.back());
                _explored.insert(p.back());
                for (auto s : nextStates) {
                    if (found(s)) {
                        auto np = p;
                        np.push_back(s);
                        _solutions.insert(np);
                    } else {
                        auto search = _explored.find(s);
                        if (search == _explored.cend()) {
                            auto np = p;
                            np.push_back(s);
                            newPaths.insert(np);
                        }
                    }
                }
                oldPaths.insert(p);
            }

            for (auto p : oldPaths) {
                _paths.erase(p);
            }
            for (auto p : newPaths) {
                _paths.insert(p);
            }

            --steps;
        }
    }
```
程式的一開始，我們建立了一個 `list<State> initialPath`，用來記錄一開始的走法，並宣告了一個 `State ss` 用來記錄最一開始的 `State`，這裡我們把 `_npeople` 傳進去，且將右岸的傳教士和食人族的個數設為 0，船的狀態設為 1 並 push 進 `ss` 中，然後將 `ss` 這個 `State` push 進 `initialPath` 中，並將它加到 `_paths` 裡  
接下來的步驟就和前面講解過的 water jugs problem 相同，進入 while 迴圈， 在 `steps` 減至 0 之前找出可能的解；在迴圈內，宣告兩個 `set<list<State>>`，分別用來記錄新的路線群和舊的路線群，接著進入迴圈，這裡我們寫 `for (auto p : _paths)` 來對 `_paths` 中的每個路線作延伸，再來我們宣告一個 `auto nextStates` 來記錄現在取出來的路線的最後一個 `State` 經過 `extend` function 後的結果；再進入下一層迴圈 `for (auto s : nextStates)`，這裡我們將每個新產生的 `State` 傳進 `found` function，如果有解，我們宣告一個 `np` 用來記錄當前的這條路徑，並將這個 `State` 加進 `np` 中，最後將 `np` 加到 `_solutions` 中；如果沒有找到解，則是檢查 `_explored` 中有沒有出現過這個 `State`，避免進入無限迴圈，如果沒有出現過，則宣告一個 `np` 用來記錄當前的這條路徑，並將這個 `State` 加進 `np` 中，最後將 `np` 加到 `newPaths`中  
在檢查完所有的路線後，最後將所有的 `oldPaths` 從 `_paths` 裡刪掉，並將 `newPaths` 中的所有新路徑加到 `paths` 中並將 `steps` 減一，如果 `steps` 大於 0 且尚未找到解的話則進行下一次尋找  
***
最後，我們要能將符合我們想要的解輸出到螢幕上，所以我們要完成  `show_solutions` 這個 function，這裡我們ㄧ樣使用 for 迴圈搭配 auto ，去跑過 `_solutions` 中的所有解並將結果輸出到螢幕上，如以下這種寫法：  
```c++
void show_solutions()
    {
        for (auto path : _solutions) {
            for (auto s : path) {
                if (!s.empty()) {
                    cout << "(" << s[0] << ", " << s[1] << ")";
                    cout << "(" << s[2] << ", " << s[3] << ")";
                    if (s[4]==1) cout << " left\n";
                    else cout << " right\n";
                }
            }
            cout << "done" << endl;
        }
    }
```


完整程式碼如下：  
```c 
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
class Crossing
{
private:
    vector<int> _npeople;  
    set<list<State>> _paths;
    set<State> _explored;
    set<list<State>> _solutions;
public:
    Crossing(vector<int> np): _npeople {np} { }
    State Go(State s, int missionary, int cannibal)
    {
        s[0] = s[0] - s[4]*missionary;
        s[1] = s[1] - s[4]*cannibal;
        s[2] = s[2] + s[4]*missionary;
        s[3] = s[3] + s[4]*cannibal;
        s[4] = -s[4];
        return s;
    }
    bool valid(State s)
    {
        if (s[0]<0 || s[1]<0|| s[2]<0 || s[3]<0) return false;
        if (s[0]<s[1] && s[0]!=0) return false;
        if (s[2]<s[3] && s[2]!=0) return false;

        return true;
    }
    set<State> extend(State s)
    {
        set<State> nextStates;

        for (int m=0; m<=2; m++) {
            for (int c=0; c<=2; c++) {
                if (m+c>=1 && m+c<=2) {
                    State ss = Go(s, m, c);
                    if (valid(ss))
                        nextStates.insert(ss);
                }
            }
        }
        return nextStates;
    }
    bool found(State s)
    {
        if (s[0]==0 && s[1]==0 && s[4]==-1) return true;
        else return false;
    }
    void solve(int steps)
    {
        list<State> initialPath;

        State ss{_npeople};
        ss.push_back(0);
        ss.push_back(0);
        ss.push_back(1); 

        initialPath.push_back(ss);
        _paths.insert(initialPath);

        while (steps>0) {
            set<list<State>> newPaths;
            set<list<State>> oldPaths;

            for (auto p : _paths) {
                auto nextStates = extend(p.back());
                _explored.insert(p.back());
                for (auto s : nextStates) {
                    if (found(s)) {
                        auto np = p;
                        np.push_back(s);
                        _solutions.insert(np);
                    } else {
                        auto search = _explored.find(s);
                        if (search == _explored.cend()) {
                            auto np = p;
                            np.push_back(s);
                            newPaths.insert(np);
                        }
                    }
                }
                oldPaths.insert(p);
            }

            for (auto p : oldPaths) {
                _paths.erase(p);
            }
            for (auto p : newPaths) {
                _paths.insert(p);
            }

            --steps;
        }
    }
    void show_solutions()
    {
        for (auto path : _solutions) {
            for (auto s : path) {
                if (!s.empty()) {
                    cout << "(" << s[0] << ", " << s[1] << ")";
                    cout << "(" << s[2] << ", " << s[3] << ")";
                    if (s[4]==1) cout << " left\n";
                    else cout << " right\n";
                }
            }
            cout << "done" << endl;
        }
    }
};

int main()
{
    vector<int> people = {3, 3};
    Crossing p(people);
    p.solve(15);
    p.show_solutions();
}
```
輸出  
```
(3, 3)(0, 0) left
(2, 2)(1, 1) right
(3, 2)(0, 1) left
(3, 0)(0, 3) right
(3, 1)(0, 2) left
(1, 1)(2, 2) right
(2, 2)(1, 1) left
(0, 2)(3, 1) right
(0, 3)(3, 0) left
(0, 1)(3, 2) right
(0, 2)(3, 1) left
(0, 0)(3, 3) right
done
(3, 3)(0, 0) left
(2, 2)(1, 1) right
(3, 2)(0, 1) left
(3, 0)(0, 3) right
(3, 1)(0, 2) left
(1, 1)(2, 2) right
(2, 2)(1, 1) left
(0, 2)(3, 1) right
(0, 3)(3, 0) left
(0, 1)(3, 2) right
(1, 1)(2, 2) left
(0, 0)(3, 3) right
done
(3, 3)(0, 0) left
(3, 1)(0, 2) right
(3, 2)(0, 1) left
(3, 0)(0, 3) right
(3, 1)(0, 2) left
(1, 1)(2, 2) right
(2, 2)(1, 1) left
(0, 2)(3, 1) right
(0, 3)(3, 0) left
(0, 1)(3, 2) right
(0, 2)(3, 1) left
(0, 0)(3, 3) right
done
(3, 3)(0, 0) left
(3, 1)(0, 2) right
(3, 2)(0, 1) left
(3, 0)(0, 3) right
(3, 1)(0, 2) left
(1, 1)(2, 2) right
(2, 2)(1, 1) left
(0, 2)(3, 1) right
(0, 3)(3, 0) left
(0, 1)(3, 2) right
(1, 1)(2, 2) left
(0, 0)(3, 3) right
done
```
### 範例二：`torch.cpp`
Torch 問題：有幾個人要過橋，每個人過橋行走的時間有差别。橋的承載力只夠同時有二人在上頭走，並且因環境是黑夜，眾人只有一根火把可用，所以凡二人過橋，須有一人把火把帶回出發處。問題要求全員過橋花費的最短時間  
有關問題的解法可以參考附件 torch.pptx，認識一下我們要解決的問題  

和前一個範例一樣，一開始需要一個基本的資料結構，我們先建立 `class Torch`，再來第一步我們要在 `Torch` 中建立一個 `struct State`，用來記錄每一次移動後橋兩側的狀態，以下為局部程式碼：  
```c++
struct State {
        vector<int> here;
        vector<int> there;
        int direction;
        State() { }
        State(vector<int> ini): here(ini.size()), there(ini.size()), direction {1} {
            for(int& i : here)
            {
                i = 1;
            }
        }
        State(const State& s): here{s.here}, there{s.there}, direction{s.direction} { }
        State& operator=(const State & s)
        {
            State localstate{s};
            swap(here, localstate.here);
            swap(there, localstate.there);
            direction = localstate.direction;
            return *this;
        }

        bool operator<(const State & s) const
        {
            return (here < s.here) || ((here == s.here) && (there < s.there))
            || ((here == s.here) && (there == s.there) && direction < s.direction);
        }

        void show()
        {
            for (auto t : here) {
                cout << t << " ";
            }
            cout << "| ";
            for (auto t : there) {
                cout << t << " ";
            }
            if (direction == 1)
                cout << " Left\n";
            else
                cout << " Right\n";
        }
    };
```
這裡我們用 `vector<int> here` 和 `vector<int> there` 分別記錄橋的兩側目前每個人的分布狀況，例如：如果 `here` 中有一個人的值為 1，代表這個人現在在橋的右側(起點)，0 則代表這個人現在不在橋的右側(起點)，`there` 中的資料紀錄方式和 `here` 相同；接著宣告一個 `int direction` 去記錄火把目前的位置，1 代表火把在橋的右側(起點)，-1 則是在左側(終點)，所以 constructor 會先把 `here` 裡每個人的值都設為 1  
另外我們還需要多載 `＝` 和 `<` 這兩個運算子，前者可以對 struct 裡面的 member 設值與改值的動作，後者用於後面我們追溯過程時的判斷以及比較出最短過橋時間  
最後我們還需要一個 function 可以將我們的結果輸出到螢幕上，這裡我們寫一個 `void show()` function，可以參考上面的寫法  

***

再來我們還需要定義幾個變數型態，用來記錄一些資料：
```c++
using Choice = pair<int, State>;
```
這裡我們使用 int 來記錄當前已花費的時間，並用前面定義好的 `State` 來記錄此時橋兩側過橋的情況，再將這兩個資料 pair 起來並將它定義成 `Choice`  
```c++
    vector<int> walking_time;
    map<State, int> best_cost;
    map<State, State> prev_state;
```
- `walking_time`：用來記錄每個人過橋所需的時間  
- `best_cost`：用來記錄走完全程最短的時間花費和走法  
- `prev_state`：用來記錄每一個 state 的上一個 state，用於追溯前一步  

再來我們需要一個 function 來判斷是不是每個人都過橋了，這裡我們寫一個 `bool found(State s)`，當火把在橋的左邊(終點)且橋的右邊都沒有人時，代表所有人都已經過橋了，寫法如下：
```c++
bool found(State s)
    {
        if (s.direction==1) return false;
        for (auto e : s.here) {
            if (e!=0) return false;
        }
        return true;
    }
```
所以只有當 `direction`的值不等於 1 且 `here` 裡的所有數字皆為 0 時，才會回傳 true，其餘皆回傳 false  
***
因為我們除了找出最短的花費時間外，我們還需要將走法印出來，所以這裡我們要寫一個可以追溯每一步走法的 function，可以參考下面的局部程式碼：  
```c++
void trace_back(State s)
    {
        State ini(walking_time);
        while (s<ini || ini<s) {
            s.show();
            State ns = prev_state[s];
            s = ns;
        }
        ini.show();
    }
```
這裡就會用到一開始我們定義的 `<` 運算子，以免我們已經追溯到第一步了還繼續往前追溯下去  
***

接下來我們需要寫一個 function 讓我們可以從現在的 `Choice` 延伸到新的 `Choice`，這裡我們寫一個 `extend` function  
```c++
 set<Choice> extend(Choice ch)
    {
        set<Choice> nextChoices;
        if (ch.second.direction == 1) {
            for (unsigned int i=0; i<ch.second.here.size(); ++i) {
                if (ch.second.here[i]==1) {
                    // alone
                    State ns(ch.second);
                    ns.here[i] = 0;
                    ns.there[i] = 1;
                    ns.direction = -1;
                    Choice nc = make_pair(ch.first-walking_time[i], ns);
                    nextChoices.insert(nc);
                    // with company
                    for (unsigned int j=i+1; j<ch.second.here.size(); ++j) {
                        if (ns.here[j]==1) {
                            State ns2(ns);
                            ns2.here[j] = 0;
                            ns2.there[j] = 1;
                            int total_cost =
                                (walking_time[i]>walking_time[j])
                                ? walking_time[i] : walking_time[j];
                            Choice nc2 = make_pair(ch.first-total_cost, ns2);
                            nextChoices.insert(nc2);
                        }
                    }
                }
            }
        } else {
            for (unsigned int i=0; i<ch.second.there.size(); ++i) {
                if (ch.second.there[i]==1) {
                    // alone
                    State ns(ch.second);
                    ns.here[i] = 1;
                    ns.there[i] = 0;
                    ns.direction = 1;
                    Choice nc(ch.first-walking_time[i], ns);
                    nextChoices.insert(nc);
                    // with company
                    for (unsigned int j=i+1; j<ch.second.there.size(); ++j) {
                        if (ns.there[j]==1) {
                            State ns2(ns);
                            ns2.here[j] = 1;
                            ns2.there[j] = 0;
                            int total_cost =
                                (walking_time[i]>walking_time[j])
                                ? walking_time[i] : walking_time[j];
                            Choice nc2(ch.first-total_cost, ns2);
                            nextChoices.insert(nc2);
                        }
                    }
                }
            }
        }

        return nextChoices;
    }
```
因為每次都會有不只一種選擇可以選，所以這裡我們宣告一個 `set<Choice> nextChoices` 去記錄所有可能的選擇，接著我們將可能性分成兩種：火把在橋的左邊還是右邊  
火把在右邊的情況：我們可以用迴圈去跑過所有的情況，因為火把是在右邊，所以我們檢查 `here` 裡為 1 的人，找到後將其 `here` 改為 0，`there` 改為 1，並將 `direction` 改為 -1，代表這個人下一步會帶著火把過橋，然後宣告一個 `Choice nc` 將我們選擇的人和他過橋所花的時間 pair 起來；再來寫第二層迴圈，去尋找和我們選擇的人一起過橋的人，這裡的做法和第一層迴圈類似，需要特別注意的是，我們必須比較兩人誰的過橋時間花費較長，而不是直接將兩人的時間相加，這裡我們宣告一個 `int total_cost` 去計算兩人過橋所需的時間，最後將結果存進 `nextChoices`裡  
而火把在橋的左邊的情況和在右邊的情況做法是相同的，大家可以參考上面的程式碼  

和前一個範例一樣，最關鍵的一步是要寫出 `void solve()` 這個 function，局部程式碼如下：
```c++
void solve()
    {
        State initialState(walking_time);
        Choice item = make_pair(0, initialState);
        prev_state[initialState] = initialState;

        priority_queue<Choice> pq;
        pq.push(item);

        while (!pq.empty()) {
            Choice currentChoice = pq.top();
            pq.pop();

            if (found(currentChoice.second)) {
                cout << "Answer: ";
                cout << -currentChoice.first << "\n";
                cout << "Tracing back:\n";
                trace_back(currentChoice.second);
                break;
            }

            set<Choice> chs = extend(currentChoice);
            for (auto c : chs) {
                int newCost = c.first;
                State newState = c.second;
                auto search = best_cost.find(newState);
                if (search == best_cost.cend()) {
                    best_cost[newState] = newCost;
                    prev_state[newState] = currentChoice.second;
                    Choice newItem = make_pair(newCost, newState);
                    pq.push(newItem);
                } else if (newCost > best_cost[newState]) {
                    best_cost[newState] = newCost;
                    prev_state[newState] = currentChoice.second;
                    Choice newItem = make_pair(newCost, newState);
                    pq.push(newItem);
                }
            }


        }

    }
```
第一步我們一樣是先宣告一個 `State initialState`來記錄起始狀態，然後宣告 `Choice item` 來將原本時間與 `State` pair 在一起，並將 `prev_state[initialState]` 的值指定好  

接著我們利用 queue 來尋找答案，但是值得注意的是，這裡我們使用 `priority_queue<Choice> pq` 來做，它和一般的 queue 不同的是，它會將排在 queue 裡面的資料做優先排序，而不是以往的先進先出，以這題來說，因為前面我們先定義了 `<` 運算子，所以會依照 `Choice` 中 int 的大小做排序，這樣我們就可以直接將目前花費時間比較少的 `Choice` 取出繼續做，不必將每個資料都跑過一遍，藉此加快程式解題的速度  

下一步我們進入迴圈，並宣告一個 `Choice currentChoice`來取出 `pq` 中的資料，因為已經排序過了，所以取出來的資料會是目前花費時間最少的；接著將當前 `currentChoice` 裡紀錄的 `State` 傳進 `found` function 中，如果已經找到解了，就將所花費的最短時間輸出到螢幕上並呼叫 `trace_back` function 將過程輸出  
這裡需要注意一點，我們在記錄所花費的時間的時候，因為時間是越短越好，但是用 priority_queue 排序的話是大的優先，所以我們在記錄時先加上負號處理，因為負號數字越大取絕對值越小，所以在輸出時要記得先加上負號再輸出  
而沒有找到解的情況，我們宣告一個 `set<Choice> chs` 去紀錄將 `currentChoice` 傳進 `extend` function 後的結果，接著進入迴圈，跑過 `chs` 中所有新延伸出來的解法，這裡有兩種情況，一種是延伸出來的解法有新的 `State`，另一種則是新的 `newCost` 比 `best_cost` 中紀錄的時間來的短，針對這兩種情況，我們繼續去尋找解，這樣當找出解時，將會是花費時間最少的最佳解   

完整程式碼如下：  
```c 
#include <iostream>
#include <vector>
#include <algorithm>
#include <map>
#include <set>
#include <queue>
#include <iterator>
using namespace std;
class Torch
{
private:
    struct State {
        vector<int> here;
        vector<int> there;
        int direction;
        State() { }
        State(vector<int> ini): here(ini.size()), there(ini.size()), direction {1} {
            for(int& i : here)
            {
                i = 1;
            }
        }
        State(const State& s): here{s.here}, there{s.there}, direction{s.direction} { }
        State& operator=(const State & s)
        {
            State localstate{s};
            swap(here, localstate.here);
            swap(there, localstate.there);
            direction = localstate.direction;
            return *this;
        }

        bool operator<(const State & s) const
        {
            return (here < s.here) || ((here == s.here) && (there < s.there))
            || ((here == s.here) && (there == s.there) && direction < s.direction);
        }

        void show()
        {
            for (auto t : here) {
                cout << t << " ";
            }
            cout << "| ";
            for (auto t : there) {
                cout << t << " ";
            }
            if (direction == 1)
                cout << " Left\n";
            else
                cout << " Right\n";
        }
    };

    using Choice = pair<int, State>;

    vector<int> walking_time;
    map<State, int> best_cost;
    map<State, State> prev_state;


    set<Choice> extend(Choice ch)
    {
        set<Choice> nextChoices;
        if (ch.second.direction == 1) {
            for (unsigned int i=0; i<ch.second.here.size(); ++i) {
                if (ch.second.here[i]==1) {
                    // alone
                    State ns(ch.second);
                    ns.here[i] = 0;
                    ns.there[i] = 1;
                    ns.direction = -1;
                    Choice nc = make_pair(ch.first-walking_time[i], ns);
                    nextChoices.insert(nc);
                    // with company
                    for (unsigned int j=i+1; j<ch.second.here.size(); ++j) {
                        if (ns.here[j]==1) {
                            State ns2(ns);
                            ns2.here[j] = 0;
                            ns2.there[j] = 1;
                            int total_cost =
                                (walking_time[i]>walking_time[j])
                                ? walking_time[i] : walking_time[j];
                            Choice nc2 = make_pair(ch.first-total_cost, ns2);
                            nextChoices.insert(nc2);
                        }
                    }
                }
            }
        } else {
            for (unsigned int i=0; i<ch.second.there.size(); ++i) {
                if (ch.second.there[i]==1) {
                    // alone
                    State ns(ch.second);
                    ns.here[i] = 1;
                    ns.there[i] = 0;
                    ns.direction = 1;
                    Choice nc(ch.first-walking_time[i], ns);
                    nextChoices.insert(nc);
                    // with company
                    for (unsigned int j=i+1; j<ch.second.there.size(); ++j) {
                        if (ns.there[j]==1) {
                            State ns2(ns);
                            ns2.here[j] = 1;
                            ns2.there[j] = 0;
                            int total_cost =
                                (walking_time[i]>walking_time[j])
                                ? walking_time[i] : walking_time[j];
                            Choice nc2(ch.first-total_cost, ns2);
                            nextChoices.insert(nc2);
                        }
                    }
                }
            }
        }

        return nextChoices;
    }

    bool found(State s)
    {
        if (s.direction==1) return false;
        for (auto e : s.here) {
            if (e!=0) return false;
        }
        return true;
    }

    void trace_back(State s)
    {
        State ini(walking_time);
        while (s<ini || ini<s) {
            s.show();
            State ns = prev_state[s];
            s = ns;
        }
        ini.show();
    }

public:

    Torch(vector<int> wt): walking_time {wt} { }
    void solve()
    {
        State initialState(walking_time);
        Choice item = make_pair(0, initialState);
        prev_state[initialState] = initialState;

        priority_queue<Choice> pq;
        pq.push(item);

        while (!pq.empty()) {
            Choice currentChoice = pq.top();
            pq.pop();

            if (found(currentChoice.second)) {
                cout << "Answer: ";
                cout << -currentChoice.first << "\n";
                cout << "Tracing back:\n";
                trace_back(currentChoice.second);
                break;
            }

            set<Choice> chs = extend(currentChoice);
            for (auto c : chs) {
                int newCost = c.first;
                State newState = c.second;
                auto search = best_cost.find(newState);
                if (search == best_cost.cend()) {
                    best_cost[newState] = newCost;
                    prev_state[newState] = currentChoice.second;
                    Choice newItem = make_pair(newCost, newState);
                    pq.push(newItem);
                } else if (newCost > best_cost[newState]) {
                    best_cost[newState] = newCost;
                    prev_state[newState] = currentChoice.second;
                    Choice newItem = make_pair(newCost, newState);
                    pq.push(newItem);
                }
            }


        }

    }
};

int main()
{
    vector<int> walking_time = {1, 2, 5, 10};
    Torch problem(walking_time);
    problem.solve();
}
```
輸出
```
Answer: 17
Tracing back:
0 0 0 0 | 1 1 1 1  Right
1 1 0 0 | 0 0 1 1  Left
1 0 0 0 | 0 1 1 1  Right
1 0 1 1 | 0 1 0 0  Left
0 0 1 1 | 1 1 0 0  Right
1 1 1 1 | 0 0 0 0  Left
```