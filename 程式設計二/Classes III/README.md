# Classes III

## Derived Classes, Abstract Classes

這份講義想要解釋 base class (superclass) 和 derived classes (subclasses)。

我們從 "Stroustrup, Bjarne (2013-07-10). The C++ Programming Language (4th Edition)" 書中，節錄關於這個主題需要知道的幾個重點。

##### 關於 virtual function
* 用來達到 run-time polymorphic type 的效果 (要搭配 pointers 或 references)
* 作者的話: "By default, a function that overrides a virtual function itself becomes virtual. We can, but do not have to, repeat virtual in a derived class. I don’t recommend repeating virtual. If you want to be explicit, use override."
* Compiler 通常是利用所謂的 virtual function table (`vtbl`) 來記錄指標，透過指標指向對應的 virtual functions。
* 作者的話: "If a class has a virtual function, it needs a virtual destructor."

##### 關於 pure virtual function 和 abstract class
* 一個 class 只要包含了任何 pure virtual function，就是一個 abstract class。Abstract class 不能產生物件，只能當作其他 classes 的 interface。

##### 關於 access control
一個 class 的 member 可以分成 `private` `protected` `public`
* 如果是 `private`、則只有同一個 class 的其他 member functions，或是該 class 的 friends 才能使用。
* 如果是 `protected`：則只有同一個 class 的其他 member functions，或是該 class 的 friends，或是 derived classes 的 member functions 才能使用。
* 如果是 `public`: 大家都可以用。
* 繼承的時候，在 class 名稱後面可以用  `: public base_class_name` 、`: protected base_class_name` 、 `:private base_class_name` 的形式，來指定繼承而來的 public functions，要放在自己的哪個區域。
* 詳細的整理可參考 https://www.programiz.com/cpp-programming/public-protected-private-inheritance

底下的例子涵蓋上述概念。

```C++
#include <iostream>
#include <vector>

namespace shapes
{
using namespace std;
struct Point
{
	int x;
	int y;
	Point(int a, int b) : x{a}, y{b} { }
};

class Shape
{
public:
	virtual Point center() const =0; // pure virtual function
	virtual void shift(Point dxy) =0;
	virtual void rotate(int angle) =0;
	virtual void draw() =0;
	virtual ~Shape() {}
};

class Rectangle;
class Circle : public Shape
{
public:
	Circle(Point p, int rr) : c{p}, r{rr} { }
	Point center() const override
	{
		return c;
	}
	void shift(Point dxy) override
	{
		c.x += dxy.x;
		c.y += dxy.y;
	}
	void rotate(int ) override { }
	void draw() override
	{
		cout << c.x << " " << c.y << " " << r << "\n";
	}
	friend bool bounding(Rectangle &rec, Circle &cir);
private:
	Point c;
	int r;
};

class Rectangle : public Shape
{
public:
	Rectangle(Point p1, int wid, int hei) : ul{p1}, w{wid}, h{hei} { }
	Point center() const override
	{
		return
			Point(ul.x+w/2, ul.y+h/2);
	}
	void shift(Point dxy) override
	{
		ul.x += dxy.x;
		ul.y += dxy.y;
	}
	void rotate(int ) override { }
	void draw() override
	{
		cout << ul.x << " " << ul.y << " : ";
		cout << w << " " << h << "\n";
	}
	friend bool bounding(Rectangle &rec, Circle &cir);
private:
	Point ul;
	int w, h;
};

void move_all(vector<Shape*>& v, Point to)
{
	for (auto p : v)
		p->shift(to);
}
void rotate_all(vector<Shape*>& v, int angle)
{
	for (auto p : v)
		p->rotate(angle);
}

void draw_all(vector<Shape*>& v)
{
	for (auto p : v)
		p->draw();
}

bool bounding(Rectangle &rec, Circle &cir)
{
	return  (cir.c.x - cir.r)>=(rec.ul.x) && (cir.c.x+cir.r)<=(rec.ul.x+rec.w)
			&& (cir.c.y - cir.r)>=(rec.ul.y) && (cir.c.y+cir.r)<=(rec.ul.y+rec.h);
}
} // namespace shapes

int main()
{
	using namespace shapes;
	//Shape s; // no objects of an abstract class can be created.
	Circle c1 {Point{3,2},3};
	Circle c2 {Point{2,5},4};
	Circle c3 {Point{-2,-3},5};
	Rectangle r1 {Point{-3,-1},10,10};
	vector<Shape*> cp {&c1, &c2, &c3, &r1};

	cout << "Three circles:\n";
	draw_all(cp);
	cout << "Shifted by (5, 10)\n";
	move_all(cp, Point(5,10));
	draw_all(cp);

	cout.setf(ios::boolalpha);        // set format for bool
	cout << bounding(r1, c1) << endl; // print true or false instead of 1 or 0
	cout << bounding(r1, c2) << endl;
	cout << bounding(r1, c3) << endl;
}
```

---

## 自己定 List

這個範例的程式碼稍微多一點，可能要分兩次講解。 
用到了 template、smart pointers、lambda functions。

實作 List 常用的功能，例如 map、fold (reduce)、filter 等等。

```C++
#include <iostream>
#include <memory>
#include <cstdlib>

using namespace std;
template<typename T>
class List {
private:
    struct Node
    {
        Node(T v, const shared_ptr<const Node> & tail) : val{v}, next{tail} {}
        T val;
        shared_ptr<const Node> next;
    };
    shared_ptr<const Node> head;
    explicit List (const shared_ptr<const Node> & nodes) : head{nodes} { }
public:
    List() { } //head{nullptr} done by shared_ptr
    List(T val, const List & tail): head{make_shared<Node>(val, tail.head)} { }

    bool isEmpty() const { return (head == nullptr); }

    T car() const {
        if (isEmpty()) exit(EXIT_FAILURE);
        return head->val;
    }
    List cdr() const {
        if (isEmpty()) exit(EXIT_FAILURE);
        return List(head->next);
    }

    void show() const {
        if (isEmpty()) std::cout << ".\n";
        else {
            std::cout << car() << "->";
            cdr().show();
        }
    }
};

template <typename T>
List<T> cons(T val, List<T> tail)
{
    return List<T>{val, tail};
}
template<typename S, typename T, typename F>
List<S> mapl(F f, List<T> lst)
{
    if (lst.isEmpty())
        return List<S>();
    else
        return List<S>(f(lst.car()), mapl<S>(f, lst.cdr()));
}
template<typename T, typename P>
List<T> filter(P p, List<T> lst)
{
    if (lst.isEmpty())
        return List<T>();
    if (p(lst.car()))
        return List<T>(lst.car(), filter(p, lst.cdr()));
    else
        return filter(p, lst.cdr());
}
template<typename S, typename T, typename F>
S foldr(F f, S accumul, List<T> lst)
{
    if (lst.isEmpty())
        return accumul;
    else
        return f(lst.car(), foldr(f, accumul, lst.cdr()));
}
template<typename S, typename T, typename F>
S foldl(F f, S accumul, List<T> lst)
{
    if (lst.isEmpty())
        return accumul;
    else
        return foldl(f, f(accumul, lst.car()), lst.cdr());
}
template<typename T, typename F>
void foreach(F f, List<T> lst)
{
    if (!lst.isEmpty()) {
        f(lst.car());
        foreach(f, lst.cdr());
    }
}
template<class T>
void print(List<T> lst)
{
    foreach([](T v)
    {
        cout << v << "->";
    }, lst);
    cout << ".";
}
template<typename T>
List<T> concat(const List<T> a, const List<T> b)
{
    if (a.isEmpty())
        return b;
    return List<T>(a.car(), concat(a.cdr(), b));
}
template<typename T>
T findmax(const List<T>  a)
{
    return foldl([](T acc, T x){return (acc>x) ? acc : x;}, a.car(), a);
}
List<int> qqsort(const List<int> a)
{

    if (a.isEmpty()) return a;

    int piv = a.car();
    List<int> le = qqsort(filter<int>([=](int x){return piv>x;}, a.cdr()));
    List<int> ri = qqsort(filter<int>([=](int x){return piv<=x;}, a.cdr()));
    return concat<int>(le, cons(piv, ri));
}

int main()
{
    List<int> l;
    List<int> n = cons(4, cons(5, cons(6, cons(7, cons<int>(8, l)))));
    l.show();
    n.show();
    auto m0 = mapl<double>([](int i){return i*0.5;} , n); // List<double>
    m0.show();
    auto m = mapl<int>([](int i){return i;} , n); // List<int>
    m.show();
    m = filter<int>([](int i){ return i%2==0;}, n);
    m.show();
    cout << foldl([](int acc, int i){return acc+i;}, 0, m) << endl;
    print(m); // or print<int>(m);
    cout << endl;
    List<List<int>> ll;
    auto mm = cons(m, cons(n, ll)); // List<List<int>>
    foreach([](List<int> l) { cout << "["; print(l); cout << "]" << "->";}, mm);
    cout << endl;
    auto g = concat(n, m); //List<int>
    g.show();
    cout << findmax(g) << endl;

    List<int> ns = cons(9, cons(10, cons(6, cons(5, cons(4, cons(8, cons(7, l)))))));
    cout << "Before: ";
    ns.show();
    ns = qqsort(ns);
    cout << "After: ";
    ns.show();
}
```

延伸閱讀：C++17 fold expression
https://baptiste-wicht.com/posts/2015/05/cpp17-fold-expressions.html

延伸閱讀：關於 explicit，copy-initialization 和 direct-initialization 的說明
http://en.cppreference.com/w/cpp/language/explicit

延伸閱讀：關於 lambda function
http://en.cppreference.com/w/cpp/language/lambda
