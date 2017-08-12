## List 範例-找路徑

可以先去台北市政府資料開放平台網站，找台北捷運站的站名，以及站與站的連結資料。類似底下這樣  
```
淡水      紅樹林    
紅樹林    竹圍    
竹圍      關渡    
關渡      忠義    
忠義      復興崗    
復興崗    北投    
北投      奇岩    
奇岩      唭哩岸    
唭哩岸    石牌    
```

台北市政府資料開放平台：<http://goo.gl/4xrzcW>  


![map](http://taipei.network.com.tw/taipeimrtmap/images/map.png)  

問題：假如想從 **內湖** 到 **小南門**，有哪些路線可行?  

第一步，我們可以用 `map<string, set<string> >`來儲存網路結構的資料，使用`list<string`來紀錄路徑  

算法:
- 不斷向前推進
- 指定某個起點 **src** 以及終點 **dst** 試著找可以到達的路徑
- 如果 **src** 等於 **dst**，那就不用再繼續找了，先看看目前已知的最佳路徑，和當前的路徑，哪一個比較短，傳回短的那條路徑
- 如果 **src** 還不等於 **dst**，就看看 **src** 的鄰站有哪些，只要鄰站不在已走過的路徑中，就試著從鄰站繼續往前找 **dst**，並且持續更新目前最短路徑
- 最終，應該會把全部可到達目的地的路徑都走過，然後留下最短的那條


完整程式碼如下：
```c++
#include <iostream>
#include <fstream>
#include <map>
#include <set>
#include <list>
#include <algorithm>

using namespace std;

using Graph = map<string,set<string>>;
using Path = list<string>;

Path has_path(Graph &g, string src, string dst, Path path, Path best_path)
{
    if (src==dst) {
        path.push_back(dst);
        if (best_path.empty())
            return path;
        else if (path.size() < best_path.size())
            return path;
        else
            return best_path;
    }

    path.push_back(src);

    for (auto& n: g[src]) {
        if (find(path.begin(), path.end(), n) == path.end() )
            best_path = has_path(g, n, dst, path, best_path);
    }
    return best_path;
}


int main()
{
    ifstream fin;
    fin.open("mrt.txt");

    Graph mrt_graph;

    string dep, arv;
    while (fin >> dep) {
        fin >> arv;
        mrt_graph[dep].insert(arv);
        mrt_graph[arv].insert(dep);
    }
    fin.close();

    Path p, bp;
    bp = has_path(mrt_graph, "內湖", "小南門", p, bp);
    for (auto &r: bp) {
        cout << r << "-->" ;
    }
    cout << endl;
}
```
### step 1 定義 Graph 和 Path
```c++
using Graph = map<string,set<string>>;
using Path = list<string>
```
這裡我們定義了 Graph 是一個 string 對應一個的 set<string> map，Path 是一個由 string 組成的 list 

### step 2 讀取地圖

```c++
ifstream fin;
    fin.open("mrt.txt");

    Graph mrt_graph;

    string dep, arv;
    while (fin >> dep) {
        fin >> arv;
        mrt_graph[dep].insert(arv);
        mrt_graph[arv].insert(dep);
    }
    fin.close();
```
一開始我們先宣告 mrt_graph 用來存地圖，這裡我們讀取 mrt.txt 中各個相鄰的站與站的資料，並將其存在 mrt_graph 中  

### step 3 建立 Path
```c++
Path has_path(Graph &g, string src, string dst, Path path, Path best_path)
{
    if (src==dst) {
        path.push_back(dst);
        if (best_path.empty())
            return path;
        else if (path.size() < best_path.size())
            return path;
        else
            return best_path;
    }

    path.push_back(src);

    for (auto& n: g[src]) {
        if (find(path.begin(), path.end(), n) == path.end() )
            best_path = has_path(g, n, dst, path, best_path);
    }
    return best_path;
}
```
- 傳進來的參數

```c
1.Graph ＆g：紀錄Path的地圖
2.string src：當前所在的站
3.string dst：目的地的站
4.Path path：當下的路徑
5.path best_path：最短路徑
```
整個 function 在一開始會判斷 src 和 dst 是否相同，如果相同則代表我們已經到達目的地了，那我們只需要將 dst 加到目前的 path 中，接著與 best_path 比較，如果 best_path 比較短的話則回傳 best_path，反之則回傳當前的 path  
而如果 src 不等於 dst，則先將 src 加到 path 中，接著進入迴圈，這裡我們使用  auto 及 iterator 搜尋整個 g 裡面的資料，如果在路徑中不存在當下的 src 代表還沒走過，則進入遞迴，直到將 best_path 找出並回傳  

### 另一種做法：使用 queue 找出最短路徑

利用 queue 來把每個能夠往前拓展的路徑依序檢驗，像排隊一樣，先進來的先檢驗。只要隊伍中還有人在排，就繼續檢驗  

過程中我們需要記住每個節點，以及從 src 到達該節點的路徑，這樣的資料用 pair 來儲存  

我們可以把全部找的路徑，都先放入一個用來計錄路徑的集合 ==set<Path>== 裡面。設定一個上限，只要長度小於上限的路徑，都可以放進集合中

完整程式碼如下：  

```c++
#include <iostream>
#include <fstream>
#include <map>
#include <set>
#include <list>
#include <algorithm>
#include <queue>
#include <utility>

using namespace std;

using Graph = map<string,set<string>>;
using Path = list<string>;


set<Path> has_path(Graph g, string src, string dst, int limit)
{
    Path pa;
    pa.push_back(src);
    queue<pair<string,Path>> qq;
    pair<string,Path> src_path;
    src_path = make_pair(src, pa);
    qq.push( src_path );

    set<Path> sol;
    while (!qq.empty()) {
        pair<string,Path> pa;
        pa = qq.front();
        qq.pop();
        Path path;
        path = pa.second;
        for (auto n: g[pa.first]) {
            if (find(path.begin(), path.end(), n) == path.end() ) {
                Path tmp = path;
                tmp.push_back(n);
                if (n==dst) {
                    if (tmp.size()<limit)
                        sol.insert(tmp);
                } else {
                    pair<string,Path> tmp_path;
                    tmp_path = make_pair(n, tmp);
                    qq.push( tmp_path );
                }
            }
        }
    }

    return sol;
}

int main()
{
    ifstream fin;
    fin.open("mrt.txt");

    Graph mrt_graph;

    string dep, arv;
    while (fin >> dep) {
        fin >> arv;
        mrt_graph[dep].insert(arv);
        mrt_graph[arv].insert(dep);
    }
    fin.close();

    set<Path> sbp;
    sbp = has_path(mrt_graph, "內湖", "小南門", 15);
    for (auto & sr: sbp) {
        for (auto &r: sr) {
            cout << r << "-->" ;
        }
        cout << endl;
    }
}
```
### 建立 has_path function
```c++
set<Path> has_path(Graph g, string src, string dst, int limit)
{
    Path pa;
    pa.push_back(src);
    queue<pair<string,Path>> qq;
    pair<string,Path> src_path;
    src_path = make_pair(src, pa);
    qq.push( src_path );

    set<Path> sol;
    while (!qq.empty()) {
        pair<string,Path> pa;
        pa = qq.front();
        qq.pop();
        Path path;
        path = pa.second;
        for (auto n: g[pa.first]) {
            if (find(path.begin(), path.end(), n) == path.end() ) {
                Path tmp = path;
                tmp.push_back(n);
                if (n==dst) {
                    if (tmp.size()<limit)
                        sol.insert(tmp);
                } else {
                    pair<string,Path> tmp_path;
                    tmp_path = make_pair(n, tmp);
                    qq.push( tmp_path );
                }
            }
        }
    }

    return sol;
}
```
- 傳進來的參數
```c
1.Graph g：紀錄Path的地圖
2.string src：當前所在的站
3.string dst：目的地的站
4.int limit：站數限制
```
整個 function 在一開始先宣告出 Path pa 並將當前的站加入這條 Path 中，接著我們宣告出 queue<pair<string,Path>> qq，用於接下來的路徑尋找，如果 qq 裡面還有東西，代表我們還沒有將路徑找出；接著我們宣告出 pair<string,Path> src_path，並將 src 與 pa pair的結果指定給src_path 
> Note : 
> 要先 include <utility> 才可以使用 make_pair這個函式庫

再來我們宣告出 set<Path> sol 紀錄所有延伸出來的path
接著進入迴圈，當 qq 不是空的時候，代表路徑還沒有找出，則我們宣告一個變數 pair<string,Path> pa 去記住目前 qq 裡最前面的資料，再宣告出 Path path 去記住剛剛從 qq 裡取出的 path 並將 qq 最前面的資料從 qq 裡 pop 掉代表這條 path 我們走過了，避免待會再走一遍
接著進入 for 迴圈，這裡我們和前面的作法相同，使用 auto 和 iterator 去搜尋整個 Graph g，如果當前所在的站是沒有拜訪過的，則宣告 Path tmp 去記住現在的 path 並將當前的站加到這條 path 中，如果當前的站是目的地站且站數小於站數限制的話，則將這條 path 加到 sol 中；否則就是將當前的站加入 path 中並重新 push 進 qq 中做下一次的尋找