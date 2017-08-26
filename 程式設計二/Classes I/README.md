# Classes I

目錄  

*   [前言](#前言)  
*   [Data Members, Member Functions, Access Control](#data-members-member-functions-access-control)  
*   [Constructor, Default Constructor, Member Initializer List](#constructor-default-constructor-member-initializer-list)  
*   [Delegating Constructor, Converting Constructor](#delegating-constructor-converting-constructor)  
*   [Static Member, Const Member Function, This Pointer](#static-member-const-member-function-this-pointer)  
*   [Operator Overloading](#operator-overloading)  


## 前言

在 C 裡面，我們透過 structures 來定義自己的資料型別，藉此在處理某些問題時更為簡易  
而在 C++ 裡面，更是進一步地將 structures 與 classes 的功能擴展，藉此可更直接地支援 object-oriented programming  

而促使我們需要 structures 與 classes 的理由，主要是 data abstraction 與 encapsulation  
宗旨是希望在設計一個資料型別時，能夠將 interface 與 implementation 分開，用更為高階的角度去撰寫程式  
比起只專注在如何達到目的，我們還會關注於如何讓這個物件被使用，與其他物件又該如何協作  
而在使用一個別人已經設計好的 abstract data type 時，我們就不需要去了解背後的實作，只要了解如何使用就好  

而以下介紹的僅只是 C++ 做為一個程式語言，有哪些手段可以幫助我們設計出一個 abstract data type  
並不代表掌握了這些語法，就可以寫出很漂亮的程式架構  

> Note:  
> 在 C++ 中，`class`與`struct`的差別僅止於預設的 access control 不同  
> 為方便起見，下文在介紹語法時主要都以`class`作為關鍵字，但請記住這點  

在往下看以前，請先注意幾件事情  

1.  術語部分，本文盡量採用英文，是因為有的術語採用中文，閱讀起來有時會造成障礙  

2.  有些術語可能沒看過、或是有的地方並沒有講解得很細，是因為 C++ 的語法真的不是簡單一份講義就可以說完  
    有興趣的讀者可以自行用關鍵字找資料，或是閱讀 C++ Primer 等書籍  
    也因為很多語法部分會相互牽扯，故本文也並不會針對每個主題都附上參考連結  
    然而因保留了英文術語，故找尋資料時不致沒有線索可循，關於此點請多包涵  

3.  以下的程式碼，是以筆者當下認為較好的寫法進行介紹，非常有可能有更好的設計方法可以使用  
    如有興趣，讀者也可以自行改寫，看怎麼樣的設計會更為貼切，並試著提出理由說服自己  

4.  標題一方面也是配合程式碼進行語法介紹，相關的主題可能分散到不同段落  
    所以如同第二點，當有疑問時，請上網查閱資料或是參考書籍，避免只有片段的了解  

5.  當有不確定語法如何的時候，請盡量以最保險的方式撰寫程式碼  
    譬如不知道甚麼時候變數會被預設為`0`，那就每個地方都初始化也無妨  

## Data Members, Member Functions, Access Control

假設我們想要在程式中設計一個 abstract data type 來表達有理數  
起初我們可以先利用類似 C 的`struct`設計出以下的型別  

```C++
class Rational
{
    int mNumerator;
    int mDenominator;
};

Rational a;
// a.mNumerator = 5;
// a.mDenominator = 1;
```

可以看到定義`class`的方法與 C 定義`struct`的方法十分相似  
不過現在我們不用再寫上`typedef`，就可以直接透過`class`的名稱宣告變數  
其中可以注意到，在底下有兩行程式碼被註解掉，原因是是這樣的存取操作會違反 access control  

> Note:  
> 同理，在 C++ 中，`struct`也不需要`typedef`就可以直接用其名稱  

在 C++ 中，有三個關鍵字：`public`、`protected`、`private`可以掌控其 members 的存取方式  

*   **public**  
    最為寬鬆的存取權限，可在所有地方被存取  

*   **protected**  
    僅限於該類別的 derived class 與該類別的 members 和 friend 可存取  

*   **private**  
    僅限於該類別的 members 和 friend 可存取  

而在`class`中，如果定義 members 時並沒有指定權限，那麼預設的 members 都會是`private`  
在`struct`中，則是預設為`public`  

```C++
class A
{
    int mX; // private
public:
    int mY; // public
    int mZ; // public
private:
    int mW; // private
};

struct B
{
    int mX; // public
public:
    int mY; // public
    int mZ; // public
private:
    int mW; // private
};
```

回到原本的例子，我們可以利用 access specifiers 來讓那些 data members 不隨意被外界存取  
接著，我們可以定義 member functions 來作為給使用者的 interface  

```C++
class Rational
{
public:
    int Numerator()
    {
        return mNumerator;
    }
    
    int Denominator()
    {
        return mDenominator;
    }
    
    void Numerator(const int num)
    {
        mNumerator = num;
    }
    
    void Denominator(const int den)
    {
        mDenominator = den;
    }

private:
    int mNumerator;
    int mDenominator;
};

Rational a;
a.Numerator(1);
a.Denominator(1);
```

在`class`或是`struct`中可以宣告或定義 member functions  
我們在上面的例子裡宣告並定義了四個 member functions，並且將其設定為`public`  
代表不只 class 本身，外界也可以使用這四個我們所提供的 interface  
而使用這些 member functions 的方式如同 data members 一樣，可以透過`.`或是`->`  

另外可以注意到，member functions 也是同樣適用於 function overloading 的  
所以如果是`a.Numerator();`就會拿到`mNumerator`的值；或像上例那樣呼叫，便可設定`mNumerator`的值  

又，在 C++ 中，如果在 class 的定義裡面順便定義了 member functions，那麼他會被隱式地設定為 inline functions  
如果不需要 member functions 被隱式地加上`inline`，那麼建議應該將其宣告與定義分開  
這樣在`Rational`被多個程式碼檔案引用時，就不會在每個檔案編譯時都編譯一次其 member functions  

```C++
/* Rational.h */
#ifndef RATIONAL_H_
#define RATIONAL_H_

class Rational
{
public:
    int Numerator();
    int Denominator();
    void Numerator(const int num);
    void Denominator(const int den);

private:
    int mNumerator;
    int mDenominator;
};

#endif
```

```C++
/* Rational.cpp */
#include"Rational.h"

int Rational::Numerator()
{
    return mNumerator;
}
    
int Rational::Denominator()
{
    return mDenominator;
}

void Rational::Numerator(const int num)
{
    mNumerator = num;
}
    
void Rational::Denominator(const int den)
{
    mDenominator = den;
}
```

或許到這裡會有點疑惑，為甚麼只是這樣簡單的傳回值跟設定值也要用 member functions 來包裝  
其實這要看我們在設計這個 abstract data type 時的想法是如何，以有理數來說，我們不希望分母是`0`  

```C++
class Rational
{
public:
    int mNumerator;
    int mDenominator;
};

Rational a;
a.mNumerator = 1;
a.mDenominator = 0;
```

上面這種寫法顯然很難阻止分母被設為`0`的狀況  
如果想要禁止分母為`0`，就必須在每次使用到該 data member 的時候都進行檢查  

不過要是透過上面的寫法，我們只需要簡單地作如下的更動  
就可以在外部要改動分母時，先確認是不是`0`以後才設定`mDenominator`  

```C++
void Rational::Denominator(const int den)
{
    if(den == 0)
    {
        // TODO
    }
    
    mDenominator = den;
}
```

但這並不代表我們永遠都要把 data members 設為`private`，而是先構思該 abstract data type 是如何被使用  
再進行設計與實作，並盡量把 interface 設計地不容易被錯誤使用  

## Constructor, Default Constructor, Member Initializer List

在`class`中，constructor 是一種在物件需要被初始化時，被呼叫的 non-static member function  
而其功用就是初始化該物件的內容  

而 constructor 的宣告如下，類似於一般 non-static member functions 的宣告方式  
但是沒有回傳的型別，其名稱也必須與該`class`同名  

```C++
class_name( parameter_list );
```

於是我們可以進一步擴充`Rational`  

```C++
#include<cassert>

class Rational
{
public:
    Rational(const int n, const int d)
    {
        // 如果 d 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(d != 0);
        
        mNumerator = n;
        mDenominator = d;
    }
    
    Rational(const int n)
    {
        mNumerator = n;
        mDenominator = 1;
    }

    int Numerator()
    {
        return mNumerator;
    }
    
    int Denominator()
    {
        return mDenominator;
    }
    
    void Numerator(const int num)
    {
        mNumerator = num;
    }
    
    void Denominator(const int den)
    {
        // 如果 den 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(den != 0);
        mDenominator = den;
    }

private:
    int mNumerator;
    int mDenominator;
};

Rational a(8, 7);
Rational b(174);
// Rational c;
```

其中首先可以注意到，constructor 與其他 member functions 一樣，都適用於 function overloading  
而在變數被定義的當下，透過後面使用`()`或是`{}`並填入正確的引數，就可以讓編譯器找到符合的 constructor  

其中，以這個 constructor 來說  

```C++
Rational(const int n)
{
    mNumerator = n;
    mDenominator = 1;
}
```

也可以寫成這樣，這種語法稱作 member initializer list  

```C++
Rational(const int n)
    :mNumerator(n), mDenominator(1)
{
}
```

用 member initializer list 的好處是他相當於 direct initialization  
以`Rational`來說，兩者的效果其實一樣，並沒有什麼差別  
所以，在此讓我們看看有甚麼別的例子是會讓兩種效果有差異的  

```C++
#include<string>

class FooA
{
public:
    FooA(const std::string& str)
    {
        mStr = str;
    }
    
private:
    std::string mStr;
};

class FooB
{
public:
    FooB(const std::string& str)
        :mStr(str)
    {
    }
    
private:
    std::string mStr;
};

FooA a(std::string("A"));
FooB b(std::string("B"));
```

以此例來說，`a`裡面的`mStr`會先呼叫 default constructor 初始化自己以後  
再執行`mStr = str;`這行，利用 copy assignment operator 把`str`的內容複製給`mStr`  

但是`b`裡面的`mStr`會直接使用 copy constructor 將`str`的內容複製到`mStr`  
比起`a`來說，少了`mStr`先初始化自己的過程  

> Note:  
> 在`Rational`中，因為`mNumerator`與`mDenominator`的型別是`int`，如果不在 member initializer list 初始化  
> 而是在 constructor 的裡面才賦值時，其實這兩個 data members 並不會先被初始化，所以不用擔心有可能會被賦值兩次  

另外像是`const`變數或是沒有 default constructor 之類的物件，只能透過 member initializer list 初始化  
如下面的`FooD`就是錯誤的語法  

```C++
class FooC
{
public:
    FooC(const int n)
        :mNum(n)
    {
    }

private:
    const int mNum;
};

/*
class FooD
{
public:
    FooD(const int n)
    {
        mNum = n;
    }

private:
    const int mNum;
};
*/

class FooE
{
public:
    FooE(const int n)
        :mFoo(n)
    {
    }

private:
    FooC mFoo;
};

FooC c(0);
// FooD d(9487);
FooE e(100);
```

還有要注意的是 member initializer list 的填寫順序與初始化順序無關  
初始化的順序是依照 data members 在`class`定義時的順序  
所以通常會建議 member initializer list 的初始化順序要與定義 data members 時的順序一致  

```C++
class FooF
{
public:
    FooF(const int n)
        :mB(n), mA(mB) // mA 的值是 indeterminate value
    {
    }

private:
    int mA;
    int mB;
};

int main()
{
    FooF f(9487);

    return 0;
}
```

再次回到本節一開始的程式碼  
可以看到有行被註解的程式碼，`// Rational c;`，如果不使這句成為註解，那麼將會產生編譯錯誤  
原因出自於`Rational`沒有任何 constructor 是可以不填寫任何引數就可以被呼叫的  
而這種 constructor 又被稱為 default constructor  

到這裡，或許讀者會疑問，在一開始還沒有寫上我們自己的 constructor 以前，為甚麼就沒問題？  
因為一旦我們定義了自己的 constructor，編譯器就不會隱式地為我們產生一個 default constructor  

如果我們沒有自己定義的 default constructor，又希望保留編譯器替我們產生的 default constructor  
可以先寫出 default constructor 的宣告以後，在後面接上`= default`來達成此目的  
若是想明確地禁止，則是使用`= delete`  

像是 default constructor 這種在某些情況下，會由編譯器隱式地提供宣告或是定義  
這種 functions 均被稱為 special member functions，也可以用同樣方法保留或是禁止  

```C++
struct FooA
{
    FooA(int n)
    {
    }
    
    FooA() = default;
};

struct FooB
{
    FooB(int n)
    {
    }
    
    FooB() = delete;
};

FooA a;
FooA b(0);

// FooB c;
FooB d(0);
```

但是在這裡，如果我們希望允許 default constructor 能初始化成有意義的值的話，不能使用`= default`  
因為若是使用`= default`，承上面的 Note，`Rational`的 data members 都會被賦予 indeterminate value  
與其可能讓使用者做出錯誤的舉動，不如禁止此行為，或是自己明確地定義 default constructor  

```C++
#include<cassert>

class Rational
{
public:
    Rational()
        :mNumerator(0), mDenominator(1)
    {
    }

    Rational(const int n, const int d)
        :mNumerator(n), mDenominator(d)
    {
        // 如果 d 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(d != 0);
    }
    
    Rational(const int n)
        :mNumerator(n), mDenominator(1)
    {
    }

    int Numerator()
    {
        return mNumerator;
    }
    
    int Denominator()
    {
        return mDenominator;
    }
    
    void Numerator(const int num)
    {
        mNumerator = num;
    }
    
    void Denominator(const int den)
    {
        // 如果 den 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(den != 0);
        mDenominator = den;
    }

private:
    int mNumerator;
    int mDenominator;
};

Rational a(8, 7);
Rational b(174);
Rational c;
```

## Delegating Constructor, Converting Constructor

如果有很多個 constructor 可以進行 function overloading  
但其內容有部分是相同的，有沒有辦法試著將程式碼精簡一點呢？  

在此要介紹一種語法，是 delegating constructor  
也就是在 member initializer list 中，只有唯一一項，且該項是呼叫某個 constructor 並傳遞其能接受的引數  

```C++
#include<cassert>

class Rational
{
public:
    Rational(): Rational(0, 1) {}

    Rational(const int n): Rational(n, 1) {}
    
    Rational(const int n, const int d)
         :mNumerator(n), mDenominator(d)
    {
        // 如果 d 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(d != 0);
    }

    int Numerator()
    {
        return mNumerator;
    }
    
    int Denominator()
    {
        return mDenominator;
    }
    
    void Numerator(const int num)
    {
        mNumerator = num;
    }
    
    void Denominator(const int den)
    {
        // 如果 den 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(den != 0);
        mDenominator = den;
    }

private:
    int mNumerator;
    int mDenominator;
};

Rational a(8, 7);
Rational b(174);
Rational c;
```

這樣的好處在於萬一要改動 constructor 裡面的邏輯，只需要更改一次就好  
而如果某個 constructor 有使用到 delegating constructor  
則該 constructor 本身的內容會等委派的 constructor 執行完畢以後才會執行  

不過筆者在這裡偏向於利用 default argument 來解決  
畢竟每個 constructor 本身的內容都相同，僅是要填入的引數不同  

```C++
#include<cassert>

class Rational
{
public:
    Rational(const int n = 0, const int d = 1)
        :mNumerator(n), mDenominator(d)
    {
        // 如果 d 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(d != 0);
    }

    int Numerator()
    {
        return mNumerator;
    }
    
    int Denominator()
    {
        return mDenominator;
    }
    
    void Numerator(const int num)
    {
        mNumerator = num;
    }
    
    void Denominator(const int den)
    {
        // 如果 den 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(den != 0);
        mDenominator = den;
    }

private:
    int mNumerator;
    int mDenominator;
};

Rational a(8, 7);
Rational b(174);
Rational c;
```

而接下來要介紹的是 converting constructor  
只要是 constructor 前面沒有加上`explicit`關鍵字，都是 converting constructor  
最主要的差別是在 initialization 的時候，可以成為「自訂型別轉換」中的一環  
以及會不會被納入 function overloading 中進行決議  

```C++
/* class Rational defined as above */
Rational a = 100;
```

看上去似乎很難接受，但其實這邊做的事情是  

1.  這是個 copy initialization，`100`的型別為`int`，並不是`Rational`或是其 Derived Class  

2.  尋找`Rational`裡面的 converting constructor，發現可以透過`Rational(const int n = 0, const int d = 1)`  
    將`100`轉換成`Rational(100)`  

3.  透過 copy constructor 將`Rational(100)`這個暫時值複製給`a`  

> Note:  
> 然而在有些狀況，其實是不會有 copy constructor 的介入  
> 可能是透過編譯器，或是依照標準進行最佳化  

至於如果是利用`{}`來進行 initialization，那麼事情就會變得更加複雜了  
在這裡簡單舉例 converting constructor 適用於 copy list initialization 的情況  

```C++
/* class Rational defined as above */

Rational Test(Rational a, Rational b)
{
	return{ 100 };
}

int main()
{
	Test({}, { 5, 2 });

	return 0;
}
```

如果我們不希望這種轉換發生，我們只需要在 constructor 前面加上`explicit`就可以了  
但依照狀況的不同，有時可能在 function overloading 中也會把 explicit constructor 納入考量  
假使最後某個 explicit constructor 是最佳結果，將會產生編譯錯誤  

不過在此我們並不打算使用`explicit`，反而要保留這個特性  
之後實作 operator overloading 時，這個特性反而可以幫助我們  

關於這部分的規則，涉及到各種不同 initialization 的方法，並不是非得要知道的資訊  
在此用一個例子作為參考  

```C++
class Foo
{
public:
	explicit Foo(int)
	{
	}

	Foo(float)
	{
	}
};

int main()
{
	Foo a = 5; // OK
	// Foo b = { 5 }; // Failed

	return 0;
}
```

## Static Member, Const Member Function, This Pointer

暫且先放下`Rational`，接著要介紹的是 static members  
C++ 的`static`有兩種用途，一種是如同 C 一樣，指定 storage duration 與 linkage  
另一種用途則是在`class`或是`struct`中，可以指定該 member 獨立於 class instance 之外  

這樣說或許有點抽象，我們就先看一下到底 non-static member functions 是怎麼運作的  

```C++
#include<iostream>

class Foo
{
public:
    Foo(int n)
        :mNum(n)
    {
    }

    void Print()
    {
        std::cout << mNum << std::endl;
    }

private:
    int mNum;
};

int main()
{
    Foo a(100);
    a.Print();
    
    Foo b(99)
    b.Print();

    return 0;
}
```

為甚麼`Print`可以印出不同資料呢？在`Print`的內部，只有說將`mNum`輸出而已？  
可以試著將 non-static member functions 的運作想像成如下這樣  

```C++
/* 裡所當然會有編譯錯誤，因為這只是示意 */
#include<iostream>

class Foo
{
public:
    Foo(int n)
        :mNum(n)
    {
    }

    void Print(Foo* this)
    {
        std::cout << this->mNum << std::endl;
    }

private:
    int mNum;
};

int main()
{
    Foo a(100);
    Foo::Print(&a);
    
    Foo b(99)
    Foo::Print(&b);

    return 0;
}
```

可以想像成其實`Foo::Print`是一個屬於`Foo`這個 class scope 的 function，接收一個參數型別是`Foo*`  
而當使用到 member 的時候，都會透過`this`去指向我們所需要的東西  

實際上在 C++ 的`class`與`struct`的 non-static member functions 與 member initializer list 之中  
`this`是語言提供來表達指向這個 instance 的 pointer 的關鍵字  

> Note:  
> `this`是個 prvalue，正如同沒辦法對一個變數連續使用`&`兩次  
> `&this`當然也就是不合法的  

於是我們重新回到 static members，首先先提 static data members  
static data members 的 storage duration 也是程式開始到程式結束  
並且只會有一個 instance 在程式中  

宣告方式是在`class`裡面如同一般宣告 non-static data members 一樣，並且在前面加上`static`  
但是接下來，還要在`class`外面定義該 static data member，並且可以初始化  

```C++
#include<iostream>

class Foo
{
public:
    void IncreaseCount()
    {
        count++;
    }
    
    void DecreaseCount()
    {
        count--;
    }

    static int count;
};

int Foo::count = 0; // no 'static' keyword

int main()
{
    std::cout << Foo::count << std::endl;

    Foo a;
    a.IncreaseCount();
    std::cout << a.count << std::endl;
    
    Foo b;
    b.IncreaseCount();
    std::cout << b.count << std::endl;
    
    a.DecreaseCount();
    b.DecreaseCount();
    std::cout << Foo::count << std::endl;

    return 0;
}
```

從上面的例子可以看出來，除了可以用像是 non-static data members 一樣的`.`、`->`方法存取以外  
還可以用`class_name::`去存取  

並且在 non-static member functions 中也可以存取 static data members  
如果改成`this->count++`也是可以的  

> Note:  
> statc data members 並不總是需要特地在外面定義的  
> 但大部分的狀況是需要的  

接下來關於 static member functions 的部分，語法其實和 non-static member functions 十分類似  
存取的方式也和 static data members 如出一轍  

```C++
#include<iostream>

class Foo
{
public:
    void IncreaseCount()
    {
        mCount++;
    }
    
    void DecreaseCount()
    {
        mCount--;
    }

    static void PrintCount()
    {
        std::cout << mCount << std::endl;
    }

private:
    static int mCount;
};

int Foo::mCount = 0; // no 'static' keyword

int main()
{
    Foo::PrintCount();

    Foo a;
    a.IncreaseCount();
    a.PrintCount();
    
    Foo b;
    b.IncreaseCount();
    b.PrintCount();
    
    a.DecreaseCount();
    b.DecreaseCount();
    Foo::PrintCount();

    return 0;
}
```

但要特別注意一點，在 static member functions 中是沒有`this` pointer 的！  
這代表著，我們不能在 static member functions 中存取 non-static data members  
因為 static members 就代表與`class`的 instance 沒有關係  
既然如此，就不會跟 non-static member functions 一樣可以想像成呼叫時傳入一個 pointer  

以上面的例子來說，可以把`a.PrintCount()`的`a.`想成是提供編譯器可以推出是`Foo::`的線索  
而不是可以做為`this`所指向的 address 的線索  

最後，本節要介紹的就是 const member functions  
`const`代表常數，試圖直接更改 const object 將會引起編譯錯誤，間接更改也可能引起 undefined behavior  
而一個屬於`class`的 const object 是不能更動到 data members，也不能呼叫 non-const member functions  

```C++
struct Foo
{
    void Modify()
    {
        num = 100;
    }
    
    void Nothing()
    {
    }
    
    int num;
};

int main()
{
    const Foo a;
    // a.Modify();
    // a.num = 0;
    // a.Nothing();

    return 0;
}
```

但以上面的例子來說，`Foo::Nothing`什麼事情也沒做，可是對於`a.Nothing()`來說，還是會引起編譯錯誤  
為了解決這種狀況，只要在 non-static member functions 的宣告與定義中的尾巴加上`const`  
就代表這個 function 可以在該 instance 也為`const`所修飾時被呼叫  

```C++
struct Foo
{
    void Modify() /* const */
    {
        num = 100;
    }
    
    void Nothing() const
    {
    }
    
    int num;
};

int main()
{
    const Foo a;
    // a.Modify();
    // a.num = 0;
    a.Nothing();

    return 0;
}
```

其中`Foo::Modify`如果把註解中的`const`保留，也會引發編譯錯誤  
對於 const member functions 來說，`this`的型別是`const class_type*`  
那麼從這個 pointer 來說，也就無法更改 data members  
如果真的想要讓 data members 在該 instance 也為`const`所修飾時被改動，只要在前面加上`mutable`即可  

```C++
struct Foo
{
    void Modify() const
    {
        num = 100;
    }
    
    void Nothing() const
    {
    }
    
    mutable int num;
};

int main()
{
    const Foo a;
    a.Modify();
    a.num = 0;
    a.Nothing();

    return 0;
}
```

在回到`Rational`之前，再提一下 const and non-const member functions 的 function overloading  
前面有提過，可以把 member functions 想像成額外多一個參數，型別是`class_type*`  
其實更精確一點的說法是，其型別是`cv_qualifier class_type*`  
以`Foo::Nothing`來說，就是期待收到型別為`const Foo*`  

所以如果以`const Foo a`來說，呼叫`Foo::Nothing`是可以透過預設的轉型  
使得`Foo*`轉為`const Foo*`並傳給`Foo::Nothing`，所以可以成功呼叫  
請參考底下這個例子，並試著思考一下其結果  

```C++
#include<iostream>

struct Foo
{
    void Print() const
    {
        std::cout<< "const version" << std::endl;
    }
    
    void Print()
    {
        std::cout<< "non-const version" << std::endl;
    }
};

void Test(const Foo& f)
{
    f.Print();
}

int main()
{
    const Foo a;
    a.Print();
    
    Foo b;
    b.Print();
    
    Test(a);
    Test(b);

    return 0;
}
```

於是，回到`Rational`，我們就可以將某些 member functions 補上`const`  

```C++
#include<cassert>

class Rational
{
public:
    Rational(const int n = 0, const int d = 1)
        :mNumerator(n), mDenominator(d)
    {
        // 如果 d 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(d != 0);
    }

    int Numerator() const
    {
        return mNumerator;
    }
    
    int Denominator() const
    {
        return mDenominator;
    }
    
    void Numerator(const int num)
    {
        mNumerator = num;
    }
    
    void Denominator(const int den)
    {
        // 如果 den 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
        assert(den != 0);
        mDenominator = den;
    }

private:
    int mNumerator;
    int mDenominator;
};
```

> Note:  
> 沒有 const constructor、const destructor  
> 並且特別注意，如果把`const`寫在最前面，其修飾的是 return type  

## Operator Overloading

operator overloading 可以說是 C++ 為了使用者自訂型別所引入的功能  
原本使用 C 的時候，我們沒辦法直接對自訂型別使用`+`、`-`、`*`、`/`等 operator  
變成得寫一個 function 並取個`plus`或是`concat`之類的名字來表達這種運算，看上去似乎就沒那麼直觀  

而我們從一開始設計到現在`Rational`，正巧就是需要這些運算的  
因此我們可以利用語言提供的特性，幫助我們讓`Rational`使用起來就像是`int`一樣  

以下，首先我們先定義`+`、`-`、`*`、`/`的運算  
而實作的部分就不會多加琢磨了，因為讀者應該要具備這樣的程度  

```C++
#include<cassert>
#include<cmath>
#include<iostream>
#include<algorithm>

class Rational
{
public:
	Rational(const int n = 0, const int d = 1)
		:mNumerator(n), mDenominator(d)
	{
		// 如果 d 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
		assert(d != 0);
		Simplify();
	}

	int Numerator() const
	{
		return mNumerator;
	}

	int Denominator() const
	{
		return mDenominator;
	}

	void Numerator(const int num)
	{
		mNumerator = num;
		Simplify();
	}

	void Denominator(const int den)
	{
		// 如果 den 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
		assert(den != 0);
		mDenominator = den;
		Simplify();
	}

private:

	void Simplify()
	{
		auto factor = GCD(mNumerator, mDenominator);

		mNumerator /= factor;
		mDenominator /= factor;
	}

	static int GCD(int a, int b)
	{
		while (b != 0) 
		{
			a = a % b;
			std::swap(a, b);
		}

		return std::abs(a);
	}

	int mNumerator;
	int mDenominator;
};

const Rational operator+(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Denominator() + lhs.Denominator()*rhs.Numerator(), lhs.Denominator()*rhs.Denominator() };
}

const Rational operator-(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Denominator() - lhs.Denominator()*rhs.Numerator(), lhs.Denominator()*rhs.Denominator() };
}

const Rational operator*(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Numerator(), lhs.Denominator()*rhs.Denominator() };
}

const Rational operator/(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Denominator(), lhs.Denominator()*rhs.Numerator() };
}

std::ostream & operator<<(std::ostream& o, const Rational& n)
{
	if (std::abs(n.Denominator()) == 1)
		o << n.Numerator()*n.Denominator();
	else
		o << n.Numerator() << " / " << n.Denominator();
	
	return o;
}

int main()
{
	Rational a{ 2,3 };
	Rational b{ -4,6 };

	std::cout << Rational(2, 6) << std::endl;
	std::cout << Rational(-2, 6) << std::endl;
	std::cout << Rational(2, -6) << std::endl;
	std::cout << Rational(-2, -6) << std::endl;
	std::cout << Rational(0, -100) << std::endl;
	
	std::cout << a + b << std::endl;
	std::cout << a - b << std::endl;
	std::cout << a * b << std::endl;
	std::cout << a / b << std::endl;

	std::cout << a + 1 << std::endl;
	std::cout << a - 1 << std::endl;
	std::cout << a * 1 << std::endl;
	std::cout << a / 1 << std::endl;

	std::cout << 2 + b << std::endl;
	std::cout << 2 - b << std::endl;
	std::cout << 2 * b << std::endl;
	std::cout << 2 / b << std::endl;

	return 0;
}
```

在這裡，筆者的想法是，使用者給定的分母與分子的正負號都會保留  
譬如`1 / (-3)`就是保留成`1 / (-3)`，而非像是`(-1) / 3`這樣把正負號都利用分子表達  
而針對分子為`0`的狀況，都將分母設為`1`或是`-1`  

首先可以注意到，`Simplify`是一個 private member function  
並且在 constructor、`Numerator()`、`Denominator()`中都有呼叫這個 function  
這是因為只有在這些情況才會更動到內部的分子與分母，而化簡的行為是不需要暴露給使用者的  
它是只在`Rational`內部需要時才會使用的 function，屬於 implementation 的一環  

而`GCD`則是被定義成 private static member function  
同樣，之所以是`private`是因為不需要暴露這個介面給使用者  
而`static`的原因則是不需要有任何 members 的存取  

這兩個合起來的行為，就是無論分子分母的正負號，都會回傳兩者的最大公因數，並取絕對值  
在分子與分母除以最大公因數以後成為最簡分數之餘，仍然能保有原本的正負號  

而 operator overloading 就如上面所示，我們把這些 functions 定義在`class`之外  
還記得前面提過的，我們之所以不將`Rational`的 constructor 加上`explicit`  
就是為了能在 operator overloading 時，直接地支援與整數的運算  

以`2 + b`為例子，首先會先找到要呼叫的 function 是  
`const Rational operator+(const Rational& lhs, const Rational& rhs)`  
接著，會發現`lhs`參考的型態與`int`不符合，於是編譯器會嘗試尋找`Rational`的 converting constructor 中  
哪個 function 的參數是可以只用一個`int`進行呼叫，於是`2`就變成`Rational(2)`  
而`rhs`就可以參考到我們使用的`b`，於是兩者就可以進行運算  

而，如果將上面關於`+`、`-`、`*`、`/`的 functions 定義在`class`之內，`a + 1`可以通過編譯，但是`2 + b`不行  
因為像是`+`、`-`、`*`、`/`等需要兩個 operands 的 operator，以 member function 的方式撰寫  
那麼對於`a operator b`來說，`a`就必須要是`Rational`，`2 + b`就無法正確地讓`2`進行型別轉換到`Rational`  
所以為了這個功能，我們將這些 functions 定義在`class`之外，不讓它們成為 member functions  

還有一個可以注意到的地方，就是那四個 functions 都回傳`const Rational`  
這是為了避免使用者寫出`(a + b) = Rational(8,7)`這種語句，不過這不代表這種寫法是錯誤的  

除了四則運算，我們還進行了`<<`的 operator overloading，而`std::ostream`就是`std::cout`的型別  
其中，條件句會判斷分母的絕對值是否為`1`，是因為如果分母是`1`或是`-1`，那麼分子的值就可以直接顯示出來  
並依照狀況決定要不要將分子加上負號，目的只是讓其顯示起來較為美觀而已  

接著，我們可以來實作與比較相關的運算  

```C++
#include<cassert>
#include<cmath>
#include<vector>
#include<iostream>
#include<algorithm>

class Rational
{
public:
	Rational(const int n = 0, const int d = 1)
		:mNumerator(n), mDenominator(d)
	{
		// 如果 d 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
		assert(d != 0);
		Simplify();
	}

	int Numerator() const
	{
		return mNumerator;
	}

	int Denominator() const
	{
		return mDenominator;
	}

	void Numerator(const int num)
	{
		mNumerator = num;
		Simplify();
	}

	void Denominator(const int den)
	{
		// 如果 den 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
		assert(den != 0);
		mDenominator = den;
		Simplify();
	}

private:

	void Simplify()
	{
		auto factor = GCD(mNumerator, mDenominator);

		mNumerator /= factor;
		mDenominator /= factor;
	}

	static int GCD(int a, int b)
	{
		while (b != 0) 
		{
			a = a % b;
			std::swap(a, b);
		}

		return std::abs(a);
	}

	int mNumerator;
	int mDenominator;
};

const Rational operator+(const Rational& n)
{
	return n;
}

const Rational operator-(const Rational& n)
{
	return{ -n.Numerator(),n.Denominator() };
}

const Rational operator+(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Denominator() + lhs.Denominator()*rhs.Numerator(), lhs.Denominator()*rhs.Denominator() };
}

const Rational operator-(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Denominator() - lhs.Denominator()*rhs.Numerator(), lhs.Denominator()*rhs.Denominator() };
}

const Rational operator*(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Numerator(), lhs.Denominator()*rhs.Denominator() };
}

const Rational operator/(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Denominator(), lhs.Denominator()*rhs.Numerator() };
}

std::ostream & operator<<(std::ostream& o, const Rational& n)
{
	if (std::abs(n.Denominator()) == 1)
		o << n.Numerator()*n.Denominator();
	else
		o << n.Numerator() << " / " << n.Denominator();
	
	return o;
}

bool operator<(const Rational& lhs, const Rational& rhs)
{
	auto r = lhs - rhs;
	auto n = r.Numerator();
	auto d = r.Denominator();

	if (d > 0 && n < 0)
		return true;
	else if (d < 0 && n > 0)
		return true;
	else
		return false;
}

bool operator>(const Rational& lhs, const Rational& rhs)
{
	return rhs < lhs;
}

bool operator<=(const Rational& lhs, const Rational& rhs)
{
	return !(lhs > rhs);
}

bool operator>=(const Rational& lhs, const Rational& rhs)
{
	return !(rhs < lhs);
}

bool operator==(const Rational& lhs, const Rational& rhs)
{
	auto r = lhs - rhs;

	if (r.Numerator() == 0)
		return true;
	else
		return false;
}

bool operator!=(const Rational& lhs, const Rational& rhs)
{
	return !(rhs == lhs);
}

int main()
{
	Rational a{ 2,3 };
	Rational b{ -4,6 };

	std::cout << "--------------------------------" << std::endl;

	std::cout << Rational(2, 6) << std::endl;
	std::cout << Rational(-2, 6) << std::endl;
	std::cout << Rational(2, -6) << std::endl;
	std::cout << Rational(-2, -6) << std::endl;
	std::cout << Rational(0, -100) << std::endl;

	std::cout << "--------------------------------" << std::endl;

	std::cout << a + b << std::endl;
	std::cout << a - b << std::endl;
	std::cout << a * b << std::endl;
	std::cout << a / b << std::endl;

	std::cout << "--------------------------------" << std::endl;
	
	std::cout << a + 1 << std::endl;
	std::cout << a - 1 << std::endl;
	std::cout << a * 1 << std::endl;
	std::cout << a / 1 << std::endl;

	std::cout << "--------------------------------" << std::endl;
	
	std::cout << 2 + b << std::endl;
	std::cout << 2 - b << std::endl;
	std::cout << 2 * b << std::endl;
	std::cout << 2 / b << std::endl;
	
	std::cout << "--------------------------------" << std::endl;
	
	std::cout << (Rational(2, 6) < Rational(2, 6)) << std::endl;
	std::cout << (Rational(3, 6) < Rational(2, 6)) << std::endl;
	std::cout << (Rational(2, 6) < Rational(3, 6)) << std::endl;
	
	std::cout << "--------------------------------" << std::endl;
	
	std::cout << (Rational(3, -6) < Rational(-2, 6)) << std::endl;
	std::cout << (Rational(-2, 6) < Rational(3, -6)) << std::endl;
	std::cout << (Rational(3, -6) < Rational(2, -6)) << std::endl;
	std::cout << (Rational(-2, 6) < Rational(-3, 6)) << std::endl;
	std::cout << (Rational(-3, 6) < Rational(-3, 6)) << std::endl;
	
	std::cout << "--------------------------------" << std::endl;

	std::vector<Rational> r{ Rational(7, 8) ,  Rational(-3, 6) ,Rational(2, 6),Rational(0, -6) };
	std::sort(r.begin(), r.end());
	
	for (auto& i : r)
		std::cout << i << std::endl;

	std::cout << "--------------------------------" << std::endl;

	return 0;
}
```

可以看到我們時做了`<`、`>`、`<=`、`>=`、`==`、`!=`這六種比較  
但實際上需要花比較多心力去寫的就是`<`和`==`而已  
因為其他的 relational operators 可以透過邏輯的方式轉成這兩種形式  
甚至連`==`其實都可以被轉為`!( a < b || b < a )`  

關於`<`的部分，主要就是有分母為正與分母為負的可能  
所以要分兩個條件去判斷到底相減的結果是不是負數  

可以看到最底下，我們使用了 STL 裡面名為`vector`的 container  
而`std::sort(r.begin(), r.end())`則是對這個 container 裡面的所有元素進行排序  
因為我們提供了`bool operator<(const Rational& lhs, const Rational& rhs)`的定義  
所以在`std::sort`的第三個 argument 預設為`std::less`的狀況下，就會使用我們提供的`<` operator  
就好比是`qsort`要提供的 function pointer 一樣，只是因為 C++ 的 operator overloading  
讓這些事情看上去更為直覺  

此外，我們又定義了只有一個 operand 的`+`與`-`  
就是為了處理像是`(+a) - (-b)`的狀況  

最後，我們要處理的就是`+=`、`-=`、`*=`、`/=`  
這些和`=`一樣也都被稱為 assignment operators  
不過對於`class`來說，有種 special member functions 稱為 copy assignment operator  
這個請容筆者暫且先不在此介紹，先把最後的四種運算符號解決  

```C++
#include<cassert>
#include<cmath>
#include<vector>
#include<iostream>
#include<algorithm>

class Rational
{
public:
	Rational(const int n = 0, const int d = 1)
		:mNumerator(n), mDenominator(d)
	{
		// 如果 d 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
		assert(d != 0);
		Simplify();
	}

	int Numerator() const
	{
		return mNumerator;
	}

	int Denominator() const
	{
		return mDenominator;
	}

	void Numerator(const int num)
	{
		mNumerator = num;
		Simplify();
	}

	void Denominator(const int den)
	{
		// 如果 den 為 0，因為條件句為 false，會輸出錯誤訊息以後並結束程式
		assert(den != 0);
		mDenominator = den;
		Simplify();
	}

private:

	void Simplify()
	{
		auto factor = GCD(mNumerator, mDenominator);

		mNumerator /= factor;
		mDenominator /= factor;
	}

	static int GCD(int a, int b)
	{
		while (b != 0) 
		{
			a = a % b;
			std::swap(a, b);
		}

		return std::abs(a);
	}

	int mNumerator;
	int mDenominator;
};

const Rational operator+(const Rational& n)
{
	return n;
}

const Rational operator-(const Rational& n)
{
	return{ -n.Numerator(),n.Denominator() };
}

const Rational operator+(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Denominator() + lhs.Denominator()*rhs.Numerator(), lhs.Denominator()*rhs.Denominator() };
}

const Rational operator-(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Denominator() - lhs.Denominator()*rhs.Numerator(), lhs.Denominator()*rhs.Denominator() };
}

const Rational operator*(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Numerator(), lhs.Denominator()*rhs.Denominator() };
}

const Rational operator/(const Rational& lhs, const Rational& rhs)
{
	return{ lhs.Numerator()*rhs.Denominator(), lhs.Denominator()*rhs.Numerator() };
}

Rational& operator+=(Rational& lhs, const Rational& rhs)
{
	lhs = lhs + rhs;
	return lhs;
}

Rational& operator-=(Rational& lhs, const Rational& rhs)
{
	lhs = lhs - rhs;
	return lhs;
}

Rational& operator*=(Rational& lhs, const Rational& rhs)
{
	lhs = lhs * rhs;
	return lhs;
}

Rational& operator/=(Rational& lhs, const Rational& rhs)
{
	lhs = lhs / rhs;
	return lhs;
}

std::ostream & operator<<(std::ostream& o, const Rational& n)
{
	if (std::abs(n.Denominator()) == 1)
		o << n.Numerator()*n.Denominator();
	else
		o << n.Numerator() << " / " << n.Denominator();
	
	return o;
}

bool operator<(const Rational& lhs, const Rational& rhs)
{
	auto r = lhs - rhs;
	auto n = r.Numerator();
	auto d = r.Denominator();

	if (d > 0 && n < 0)
		return true;
	else if (d < 0 && n > 0)
		return true;
	else
		return false;
}

bool operator>(const Rational& lhs, const Rational& rhs)
{
	return rhs < lhs;
}

bool operator<=(const Rational& lhs, const Rational& rhs)
{
	return !(lhs > rhs);
}

bool operator>=(const Rational& lhs, const Rational& rhs)
{
	return !(rhs < lhs);
}

bool operator==(const Rational& lhs, const Rational& rhs)
{
	auto r = lhs - rhs;

	if (r.Numerator() == 0)
		return true;
	else
		return false;
}

bool operator!=(const Rational& lhs, const Rational& rhs)
{
	return !(rhs == lhs);
}

int main()
{
	Rational a{ 2,3 };
	Rational b{ -4,6 };

	std::cout << "--------------------------------" << std::endl;

	std::cout << Rational(2, 6) << std::endl;
	std::cout << Rational(-2, 6) << std::endl;
	std::cout << Rational(2, -6) << std::endl;
	std::cout << Rational(-2, -6) << std::endl;
	std::cout << Rational(0, -100) << std::endl;

	std::cout << "--------------------------------" << std::endl;

	std::cout << a + b << std::endl;
	std::cout << a - b << std::endl;
	std::cout << a * b << std::endl;
	std::cout << a / b << std::endl;

	std::cout << "--------------------------------" << std::endl;
	
	std::cout << a + 1 << std::endl;
	std::cout << a - 1 << std::endl;
	std::cout << a * 1 << std::endl;
	std::cout << a / 1 << std::endl;

	std::cout << "--------------------------------" << std::endl;
	
	std::cout << 2 + b << std::endl;
	std::cout << 2 - b << std::endl;
	std::cout << 2 * b << std::endl;
	std::cout << 2 / b << std::endl;
	
	std::cout << "--------------------------------" << std::endl;
	
	std::cout << (Rational(2, 6) < Rational(2, 6)) << std::endl;
	std::cout << (Rational(3, 6) < Rational(2, 6)) << std::endl;
	std::cout << (Rational(2, 6) < Rational(3, 6)) << std::endl;
	
	std::cout << "--------------------------------" << std::endl;
	
	std::cout << (Rational(3, -6) < Rational(-2, 6)) << std::endl;
	std::cout << (Rational(-2, 6) < Rational(3, -6)) << std::endl;
	std::cout << (Rational(3, -6) < Rational(2, -6)) << std::endl;
	std::cout << (Rational(-2, 6) < Rational(-3, 6)) << std::endl;
	std::cout << (Rational(-3, 6) < Rational(-3, 6)) << std::endl;
	
	std::cout << "--------------------------------" << std::endl;

	std::vector<Rational> r{ Rational(7, 8) ,  Rational(-3, 6) ,Rational(2, 6),Rational(0, -6) };
	std::sort(r.begin(), r.end());
	
	for (auto& i : r)
		std::cout << i << std::endl;

	std::cout << "--------------------------------" << std::endl;

	Rational c;
	Rational d = b;

	c = a; c += d;
	std::cout << c << std::endl;

	c = a; c -= d;
	std::cout << c << std::endl;

	c = a; c *= d;
	std::cout << c << std::endl;

	c = a; c /= d;
	std::cout << c << std::endl;

	std::cout << "--------------------------------" << std::endl;

	return 0;
}
```

要注意的是，以`+=`來說，是長這樣  
`Rational& operator+=(Rational& lhs, const Rational& rhs)`  
之所以`lhs`是期待接收一個 non-const reference，是因為`lhs`是要被賦值的對象  
如果是被`const`修飾就沒辦法對其進行賦值了，而回傳的型別是 non-const reference  
也是因為要支援類似`a = b = c`的語法  

到目前為止，我們算是實作完`Rational`了  
希望透過這樣的介紹，能讓讀者們能夠理解 C++ 到底多了的東西能幫助我們甚麼  
