# Classes II

目錄  

*   [Special Member Function](##special-member-function)  
*   [Destructor](#destructor)  
*   [Copy Constructor, Copy Assignment Operator](#copy-constructor-copy-assignment-operator)  
*   [Move Constructor, Move Assignment Operator](#move-constructor-move-assignment-operator)  
*   [Initializer List](#initializer-list)  
*   [總結](#總結)  

## Special Member Function

我們已經提到了 default constructor 屬於 special member functions 中的其中一種  
在繼續下一個`class`的設計之前，希望在此先將會用到的另外幾個 special member functions 一併討論  

在 C++ 中，有六種 special member functions  
以下的程式碼片段在說明怎麼樣宣告會被認為是使用者自訂的該 special member function  
並不是該 special member function 如果被隱式地定義時，會有怎樣的型式  

*   **default constructor**  
    ```C++
    class_name();
    ```
    沒有任何 parameters，或是所有 parameters 都有 default arguments  
    
    在沒有提供任何 arguments 時呼叫的 constructor  

*   **copy constructor**  
    ```C++
    class_name( cv_qualifiers class_name & );
    ```
    第一個 parameter 型別為`cv_qualifiers class_name &`  
    並且如果有其他 parameters，必須都有 default arguments  
    
    如果有需要 copy initialization 的時候，便是呼叫此 constructor  
    ```C++
    struct Foo {};
    void Test(Foo arg) {}
    
    Foo b; // b is default-initialized
    Foo a = b; // a is copy-initialized
    Test(a); // arg is copy-initialized
    ```

*   **copy assignment operator**  
    ```C++
    return_type class_name::operator= ( cv_qualifiers class_name & );
    return_type class_name::operator= ( cv_qualifiers class_name );
    ```
    只能有一個 parameter，型別為`cv_qualifiers class_name &`或`cv_qualifiers class_name`  
    
    ```C++
    struct Foo {};
    
    Foo a; // a is default-initialized
    Foo b = a; // b is copy-initialized
    b = a; // copy assignment
    ```

*   **move constructor**  
    ```C++
    class_name( cv_qualifiers class_name && );
    ```
    第一個 parameter 型別為`cv_qualifiers class_name &&`  
    並且如果有其他 parameters，必須都有 default arguments  
    
    如同 copy constructor，但第一個 argument 必須為 rvalue  

*   **move assignment operator**  
    ```C++
    return_type class_name::operator= ( cv_qualifiers class_name && );
    ```
    只能有一個 parameter，型別為`cv_qualifiers class_name &&`  
    
    如同 copy assignment operator，但 argument 必須為 rvalue  

*   **destructor**
    ```C++
    ~class_name();
    virtual ~class_name();
    ```
    
    既然有像是 constructor 那樣在物件初始化時呼叫的 function  
    那麼相對應的，destructor 是在物件即將被銷毀被呼叫的 function  

以上就是 C++ 中的 special member functions  
當一個`class`裡面沒有特地去定義這些 special member functions 時，編譯器可能會隱式地宣告  
並且在需要時，會隱式地定義這些 special member functions  

在接下來的實作中，我們可以看到這些 special member functions 是如何幫助我們進行資源管理  

## Destructor

這次我們想要實作的是`Vector`，interface 是模仿`std::vector`，但是功能被精簡許多  
簡單來說，可以把`Vector`想成是一個被封裝的 integer array  
我們能夠更簡單地插入、刪除元素，並且進行複製、查詢大小等操作  

在此，我們首先要介紹的是 destructor  
當一個物件要被銷毀時，譬如由`new`產生的物件被`delete`時  
或是因為離開 scope 導致 local variables 即將被銷毀的時候  
destructor 都會被呼叫  

利用 constructor 與 destructor 的特性，可以讓資源管理更為輕鬆  
在 C 的時候，得自己利用`malloc`與`free`來配置動態的 array 並且刪除  
但現在利用 C++ 的語法，只要將「資源的初始化」放在 constructor，「資源的釋放」放在 destructor  
就可以配合變數本身的 lifetime 對資源進行更好的管理，而不需要自己在意什麼時候要如何處理  

```C++
#include<cstddef>
#include<iostream>
#include<algorithm>

class Vector
{
public:
	Vector()
		:mBegin(nullptr), mLast(nullptr), mEnd(nullptr)
	{
		std::cout << "Default constructor" << std::endl;
	}

	std::size_t capacity() const
	{
		return mEnd - mBegin;
	}

	std::size_t size() const
	{
		return mLast - mBegin;
	}

	int& operator[](const std::size_t pos)
	{
		return mBegin[pos];
	}

	const int& operator[](const std::size_t pos) const
	{
		return mBegin[pos];
	}

	void push_back(const int val)
	{
		if (mEnd == mLast)
		{
			auto cap = std::max(capacity() + 1, capacity() * 3);
			reserve(cap);
		}

		*mLast = val;
		mLast++;
	}

	void pop_back()
	{
		if (size() > 0)
		{
			mLast--;
		}
	}

	void insert(const std::size_t pos, const int val)
	{
		if (capacity() < size() + 1)
		{
			auto cap = std::max(capacity() + capacity() / 2, capacity() + 1);
			reserve(cap);
		}

		std::copy_backward(mBegin + pos, mLast, mLast + 1);
		std::fill(mBegin + pos, mBegin + pos + 1, val);
		mLast += 1;
	}

	void erase(const std::size_t pos)
	{
		std::copy(mBegin + pos + 1, mLast, mBegin + pos);
		mLast--;
	}

	void reserve(const std::size_t new_capacity)
	{
		if (capacity() < new_capacity)
		{
			auto* start = new int[new_capacity];
			std::copy(mBegin, mLast, start);
			delete[] mBegin;

			auto s = size();
			mBegin = start;
			mLast = mBegin + s;
			mEnd = mBegin + new_capacity;
		}
	}

	void resize(const std::size_t new_size)
	{
		reserve(new_size);

		if (mLast < mBegin + new_size)
			std::fill(mLast, mBegin + new_size, 0);

		mLast = mBegin + new_size;
	}

	~Vector()
	{
	    std::cout << "Destructor" << std::endl;
	    
		delete[] mBegin;
		mBegin = mLast = mEnd = nullptr;
	}

private:
	int* mBegin;
	int* mLast;
	int* mEnd;
};

std::ostream& operator<<(std::ostream& o, const Vector& vec)
{
	o << "{ ";
	for (std::size_t i = 0; i < vec.size(); i++)
	{
		o << vec[i] << ", ";
	}
	o << " } size: " << vec.size() << " capacity: " << vec.capacity();

	return o;
}
```

程式碼看上去應該會有很多未解之處  
首先我們要先對`Vector`的運作有些基礎的認知  

*   `push_back(n)`  
    在`Vector`的尾端插入值為`n`的元素  
    
*   `pop_back`  
    刪除在`Vector`尾端的元素  
    
*   `insert(pos,n)`  
    在`Vector`的第`pos`個元素前面插入值為`n`的元素  
    
*   `erase(pos)`  
    刪除在`Vector`的第`pos`個元素  

還有，對於`Vector`來說，`capacity`與`size`是不同的想法  
`size`是`Vector`當下有幾個元素，`capacity`則是代表`Vector`在配置陣列時可以存放的元素數量  
用罐子來比喻，`size`是現在罐子中溶液的存量，`capacity`則是罐子最多可以裝多少溶液  

這樣做的好處是，譬如`Vector`支援`push_back`的操作，也就是從`Vector`尾端新增一個元素  
如果`size`等同於`capacity`，就代表每次新增元素的話，都必須配置一塊新的陣列  
將舊的陣列內的元素全部複製到新的陣列以後，刪除舊的陣列以後，才能在尾端插入新元素  

隨著`Vector`的`size`漸增，插入一個元素所需花費的時間就越多，這樣的操作代價是相對高的  
所以與其這樣，不如在一開始配置陣列的時候，就比要存放的元素總數要再多一些  
這樣子在`size`超過`capacity`之前，`push_back`就會是很簡單的操作  
而在每次要重新配置陣列時，要如何決定`capacity`的大小，則是看實作如何，在此就不深入討論  

而對於使用者來說，在有些狀況可能會知道`Vector`最極端的狀況會是幾個元素  
這時候就可以先利用`reserve`來指定`capacity`，如此一來能更有效地避免重新配置陣列的操作  

`resize`則是將元素的數量擴大或是縮減，假設現在`Vector`的`size`為 5：如果是`resize(3)`  
可以想像成是呼叫了兩次`pop_back`；如果是`resize(7)`，則可以想像成呼叫了兩次`push_back(0)`  

了解了概念以後，就來看看三個 data members 各自的意義

*   `mBegin`  
    指向配置的陣列的起始位址  

*   `mLast`  
    指向的位址是`mBegin + size`  

*   `mEnd`  
    指向的位址是`mBegin + capacity`  

從上面的定義可以發現到，實際上存放著使用者插入的元素的區間是`[ mBegin, mLast )`  
而配置的陣列的有效區間則是`[ mBegin, mEnd )`  

換句話說，`mLast`是指向最後一個元素的下個元素的位址  
`mEnd`是指向配置陣列時，所能存放的最後一個元素的下一個元素的位置  

譬如

```C++
mBegin = new int[5];
mLast = mBegin;
mEnd = mBegin + 5;

mBegin[0] = 100; mLast++;
mBegin[1] = 99; mLast++;
mBegin[2] = 98; mLast++;
```

 `mBegin + 0` | `mBegin + 1` | `mBegin + 2` | `mBegin + 3` | `mBegin + 4` | `mBegin + 5`   
--------------|--------------|--------------|--------------|--------------|--------------  
100           |99            |98            |?             |?             |?               
↑`mBegin`     |              |              |↑`mLast`      |              |↑`mEnd`         

這樣的設計或許看上去很奇怪，但只要考慮到，如果`mLast`實際上是指向最後一個儲存的元素  
那麼很多時候，對於「`Vector`中沒有儲存任何元素」與「`Vector`中儲存一個元素」的處理將會相當麻煩  
讀者們如有興趣，可以在本章結束後自行改寫看看，便能理解  

了解了每個 member functions 想完成甚麼功能以後，剩下的就是實作部分  
其中可以注意到，對於`operator[]`有 const 與 non-const，這是決定`vec[0] = 100;`合不合法的關鍵  
而`size`與`capacity`也都是 const member functions  

其中，有使用到`std::fill`、`std::copy`、`std::copy_backward`這些 functions  
請自行查詢資料並試著了解如何使用，其中可以注意到，對於存取區間的有效範圍是`[ first, last )`的狀況  
在這些 functions 是屢見不鮮  

回到本節標題，destructor 就是負責把配置的陣列給刪除  
可以將`Vector`變數定義在各種地方觀察一下執行結果  
又，請記得複製程式碼時，要複製到`operator>>()`

```C++
/* Vector defined as above*/

int main()
{
    Vector a;
    std::cout << a <<std::endl;
    a.push_back(3);
    a.push_back(4);
    a.push_back(5);
    std::cout << a <<std::endl;
    
    {
        Vector b;
        b.resize(10);
        std::cout << b <<std::endl;
    }

    return 0;
}
```

## Copy Constructor, Copy Assignment Operator

如果依照上個小節的實作，寫出如下的程式碼來進行測試，可以很輕易地發現有錯誤  

```C++
/* Vector defined as above */

int main()
{
    {
        Vector a;
        a.push_back(1);
        a.push_back(2);
        a.push_back(3);
        
        {
            Vector b = a; // 等同於 Vector b(a);
        }
    }

    return 0;
}
```

在這裡的`Vector b = a;`是屬於 copy initialization  
回想起上面提到的 special member functions，在這裡編譯器就是隱式地替我們宣告且定義了 copy constructor  
而這個 copy constructor 的作用就是簡單地把每個 data members 都複製一遍到新的物件裡面  
又，這裡的 data members 只是 pointers，所以就是把`a`的 pointers 所指向的地址都複製到`b`的  

但是離開`{}`以後，`b`的 destructor 就被呼叫，把`b.mBegin`的記憶體給釋放  
這時候`a.mBegin`已經成為了 dangling pointer，之後再次釋放的時候就會引起錯誤  

同樣的狀況也發生在如下的程式碼  

```C++
/* Vector defined as above */

int main()
{
    {
        Vector a;
        a.push_back(1);
        a.push_back(2);
        a.push_back(3);
        
        {
            Vector b;
            b.push_back(1);
            
            b = a;
        }
    }

    return 0;
}
```

這裡的`b = a;`是屬於 copy assignment operator  
原理如同上面，被編譯器隱式宣告的作用就是簡單地把每個 data members 都複製到`b`  
而且除了`a.mBegin`成了 dangling pointer，甚至連原先`b.mBegin`所配置的陣列都沒有釋放到  
就把原本儲存的位址給丟棄，更新成`a.mBegin`  

這告訴我們，當`class`的 data members 本身並不是個被封裝管理的資源時  
我們沒辦法簡單地依賴編譯器替我們產生的 copy constructor 與 copy assignment operator  
而需要自己定義到底該如何針對這些情形處理  

首先，處理的是 copy constructor  
我們要做的事情就是分配一個相同`capacity`的陣列，並把要複製的物件中的元素全部複製過來  

```C++
Vector(const Vector& vec)
	:mBegin(new int[vec.capacity()]), mLast(mBegin + vec.size()), mEnd(mBegin + vec.capacity())
{
	std::cout << "Copy constructor" << std::endl;	

	std::copy(vec.mBegin, vec.mLast, mBegin);
}
```

接著是 copy assignment operator  

```C++
Vector& operator=(const Vector& vec)
{
	std::cout << "Copy assignment" << std::endl;
		
	Vector temp(vec);
	std::swap(mBegin, temp.mBegin);
	std::swap(mLast, temp.mLast);
	std::swap(mEnd, temp.mEnd);

	return *this;
}
```

這裡有個方法稱為 copy and swap，`temp`是這個 member function 的 local variable  
所以`temp`在離開這個 scope 的時候會呼叫 destructor，釋放掉`temp.mBegin`  
首先，我們利用 copy constructor，將`vec`的內容複製一份到`temp`裡面  
接著，透過將這個物件所有的 data members 與`temp`的 data members 交換  
這個物件就相當於複製了`vec`，然後把原先自己的陣列給了`temp`  
等到離開這個 function 以後，`temp`就會把這個陣列給釋放掉  

如此一來，我們就不需要再次重複寫類似 copy constructor 的內容  
也不需要考慮如果使用者寫出`a = a;`時，會不會造成錯誤刪除的情況  
self assignment 是在實作 assignment operator 時要特別注意的一個問題  
讀者可以試試看如果不使用 copy and swap，而是直接操作 pointer 的話該如何撰寫  

又，在以下的片段中，`b`、`c`、`d`都是透過 copy constructor 來複製`a`的內容  
而`a=b;`則是透過 copy assignment operator 來複製`b`的內容  
區別`Foo b = a;`與`a = b;`的關鍵在於：如果有`=`時，要先看該句是不是正在定義變數  

```C++
Foo a;
Foo b = a;
Foo c{a};
Foo d(a);
a = b;
```

## Move Constructor, Move Assignment Operator

rvalue reference 可以說是 C++ 11 中非常顯著的一樣新語法  
rvalue reference 所參考的是一個「暫時物件」，代表這個物件很快地就會被銷毀  

以往我們使用 lvalue reference，代表可以去更改所參考的物件  
透過使用 const lvalue reference，代表所參考的物件是一個常數，所以不能對其進行更動  
其中，「暫時物件」可以被 const lvalue reference 所參考，而不能被 lvalue reference 參考  

既然如此，為何我們還需要 rvalue reference 呢？  
因為我們沒有辦法判斷到底一個 const lvalue reference 所參考的物件是不是臨時物件  
所以，如果在需要複製其內容時，沒辦法只是簡單地將該物件的內容直接搬過來  
該物件有可能不是臨時物件，為此總是需要如 copy constructor 或是 copy assignment operator 等複製方法  

現在透過引入 rvalue reference，使用者就可以透過型別得知到底是不是指向一個臨時物件  
而且因為沒有了`const`的限制，我們還可以存取其 non-const members  
這樣一來，就不需要透過別的方法，而可以簡單地利用語言的機制在某些場合避免掉複製的操作  

而 move constructor 與 move assignment operator 的實作相當簡單  

```C++
Vector(Vector&& vec)
	:mBegin(vec.mBegin), mLast(vec.mLast), mEnd(vec.mEnd)
{
	std::cout << "Move constructor" << std::endl;

	vec.mBegin = nullptr;
	vec.mLast = nullptr;
	vec.mEnd = nullptr;
}

Vector& operator=(Vector&& vec)
{
	std::cout << "Move assignment" << std::endl;
		
	std::swap(mBegin, vec.mBegin);
	std::swap(mLast, vec.mLast);
	std::swap(mEnd, vec.mEnd);

	return *this;
}
```

在 move constructor 中，就是將`vec`的內容拿過來，並將其清空而已  
在 move assignment operator 中，也只是簡單地交換彼此的 data members  
原理如同上文，舊有的陣列被交換到`vec`中，因為`vec`參考的是臨時物件，待其 lifetime 結束  
自然也就會釋放自己原本的陣列  

以下有個簡單的片段，讀者們可以試試看有無 move constructor 時會產生怎樣的結果  

```C++
/* Vector defined as above */

Vector Create_Vector_With_Strange_Values()
{
    Vector v;
    v.push_back(9);
    v.push_back(4);
    v.push_back(8);
    v.push_back(7);
    
    return v;
}

int main()
{
    Vector a = Create_Vector_With_Strange_Values(); // move constructor called

    return 0;
}
```

## Initializer List

在 C++ 11 當中引入了`{}`來進行初始化，被稱作 list initialization  
其中也支援了一個特殊的語法，使得我們在初始化一個如同`std::vector`的物件時，有如同 array 的語法  

```C++
#include<vector>
#include<iostream>

int main()
{
    std::vector<int> v{1, 2, 3, 4, 5, 6};
    for(auto i:v)
    {
        std::cout << i << " ";
    }
    std::cout << std::endl;
    
    return 0;
}
```

在經過一些判斷以後，當`{}`裡面所填的 arguments 的型別一致，皆為`T`時  
編譯器會嘗試尋找有沒有 constructor 的 parameter 的型別是`std::initializer_list<T>`  
如果有的話，就會呼叫這個 constructor  

> Note:  
> 假設有兩個 constructor 分別為`Foo(int)`與`Foo(std::initializer_list<int>)`  
> `Foo a{0}`會呼叫後者，因為上面的規則，後者會被優先選中  

透過`#include<initializer_list>`，我們就可以使用該`class`  
這個`class`提供了`begin`、`end`、`size`等介面，故我們可以將裡面的值都複製出來  

```C++
Vector(std::initializer_list<int> v)
	:mBegin(new int[v.size()]), mLast(mBegin + v.size()), mEnd(mBegin + v.size())
{
	std::cout << "Constructor with initializer list" << std::endl;
				
	std::copy(v.begin(), v.end(), mBegin);
}
```

於是透過這個 constructor，我們也可以寫出底下這樣的程式碼  

```C++
/* Vector defined as above */

int main()
{
    Vector a{1, 2, 3, 4, 5, 6}; // using Vector(std::initializer_list<int> v)
    Vector b{a}; // using Vector(const Vector& vec)

    return 0;
}
```

## 總結

以下是這次`Vector`的完整程式碼，其中可以注意的是多了一個名為`swap`的 private member function  
單純就是為了讓 copy assignment operator 與 move assignmnet operator 中的過程更好閱讀  

```C++
#include<cstddef>
#include<iostream>
#include<initializer_list>
#include<algorithm>

class Vector
{
public:
	Vector()
		:mBegin(nullptr), mLast(nullptr), mEnd(nullptr)
	{
		std::cout << "Default constructor" << std::endl;
	}

	Vector(const Vector& vec)
		:mBegin(new int[vec.capacity()]), mLast(mBegin + vec.size()), mEnd(mBegin + vec.capacity())
	{
		std::cout << "Copy constructor" << std::endl;	

		std::copy(vec.mBegin, vec.mLast, mBegin);
	}

	Vector(Vector&& vec)
		:mBegin(vec.mBegin), mLast(vec.mLast), mEnd(vec.mEnd)
	{
		std::cout << "Move constructor" << std::endl;

		vec.mBegin = nullptr;
		vec.mLast = nullptr;
		vec.mEnd = nullptr;
	}

	Vector(std::initializer_list<int> v)
		:mBegin(new int[v.size()]), mLast(mBegin + v.size()), mEnd(mBegin + v.size())
	{
		std::cout << "Constructor with initializer list" << std::endl;
				
		std::copy(v.begin(), v.end(), mBegin);
	}

	Vector& operator=(const Vector& vec)
	{
		std::cout << "Copy assignment" << std::endl;
		
		Vector temp(vec);
		swap(temp);

		return *this;
	}


	Vector& operator=(Vector&& vec)
	{
		std::cout << "Move assignment" << std::endl;
		
		swap(vec);

		return *this;
	}

	std::size_t capacity() const
	{
		return mEnd - mBegin;
	}

	std::size_t size() const
	{
		return mLast - mBegin;
	}

	int& operator[](const std::size_t pos)
	{
		return mBegin[pos];
	}

	const int& operator[](const std::size_t pos) const
	{
		return mBegin[pos];
	}

	void push_back(const int val)
	{
		if (mEnd == mLast)
		{
			auto cap = std::max(capacity() + 1, capacity() * 3);
			reserve(cap);
		}

		*mLast = val;
		mLast++;
	}

	void pop_back()
	{
		if (size() > 0)
		{
			mLast--;
		}
	}

	void insert(const std::size_t pos, const int val)
	{
		if (capacity() < size() + 1)
		{
			auto cap = std::max(capacity() + capacity() / 2, capacity() + 1);
			reserve(cap);
		}

		std::copy_backward(mBegin + pos, mLast, mLast + 1);
		std::fill(mBegin + pos, mBegin + pos + 1, val);
		mLast += 1;
	}

	void erase(const std::size_t pos)
	{
		std::copy(mBegin + pos + 1, mLast, mBegin + pos);
		mLast--;
	}

	void reserve(const std::size_t new_capacity)
	{
		if (capacity() < new_capacity)
		{
			auto* start = new int[new_capacity];
			std::copy(mBegin, mLast, start);
			delete[] mBegin;

			auto s = size();
			mBegin = start;
			mLast = mBegin + s;
			mEnd = mBegin + new_capacity;
		}
	}

	void resize(const std::size_t new_size)
	{
		reserve(new_size);

		if (mLast < mBegin + new_size)
			std::fill(mLast, mBegin + new_size, 0);

		mLast = mBegin + new_size;
	}

	~Vector()
	{
		std::cout << "Destructor" << std::endl;

		delete[] mBegin;
		mBegin = mLast = mEnd = nullptr;
	}

private:
	void swap(Vector& vec)
	{
		std::swap(mBegin, vec.mBegin);
		std::swap(mLast, vec.mLast);
		std::swap(mEnd, vec.mEnd);
	}

	int* mBegin;
	int* mLast;
	int* mEnd;
};

std::ostream& operator<<(std::ostream& o, const Vector& vec)
{
	o << "{ ";
	for (std::size_t i = 0; i < vec.size(); i++)
	{
		o << vec[i] << ", ";
	}
	o << " } size: " << vec.size() << " capacity: " << vec.capacity();

	return o;
}

void Test(const Vector vec)
{
	std::cout << vec << std::endl;
}

int main()
{
	Vector a = { 1,2,3 };

	{
		std::cout << a << std::endl;
		a.insert(3, 4);
		std::cout << a << std::endl;
		a.resize(1);
		std::cout << a << std::endl;

		Vector b = a;
		std::cout << b << std::endl;
		b.push_back(2);
		b.push_back(3);
		std::cout << b << std::endl;
		b.pop_back();
		std::cout << b << std::endl;
		b = { 1,2,3,4,5 };
		std::cout << b << std::endl;
		a = std::move(b);
		std::cout << a << std::endl;
		std::cout << b << std::endl;

		a = a;
	}

	{
		Vector b{ 6,8,9 };
		std::cout << b << std::endl;
		b.insert(1, 7);
		std::cout << b << std::endl;

		Test(std::move(b));
		std::cout << b << std::endl;
	}

	{
		Vector b;
		b.reserve(15);
		std::cout << b << std::endl;

		b.resize(10);
		std::cout << b << std::endl;

		b.insert(0, -1);
		std::cout << b << std::endl;

		b.insert(11, 1);
		std::cout << b << std::endl;

		b.pop_back();
		std::cout << b << std::endl;

		b.push_back(2);
		b.push_back(3);
		std::cout << b << std::endl;

		b.erase(11);
		std::cout << b << std::endl;

		b.resize(0);
		std::cout << b << std::endl;

		b.resize(2);
		std::cout << b << std::endl;

		b.pop_back();
		std::cout << b << std::endl;
	}

	return 0;
}
```

讀者們可以試著閱讀並執行程式碼，並依照輸出的資訊來檢閱一下自己的觀念正不正確  
希望透過這次的`Vector`，能夠讓讀者理解到利用這些方法，可以對資源管理進行更好的包裝  

最後，有個程式碼可以讓讀者執行看看，並試著解釋一下理由  

```C++
/* Vector defined as above */

struct Foo
{
	Vector vec;
};

int main()
{
	Foo a;
	a.vec.resize(2);
	std::cout << a.vec << std::endl;

	Foo b(a);
	a.vec.push_back(99);
	std::cout << b.vec << std::endl;

	{
		Foo c;
		c = a;
		c.vec.push_back(100);
		std::cout << c.vec << std::endl;
	}

	std::cout << a.vec << std::endl;

	return 0;
}
```
