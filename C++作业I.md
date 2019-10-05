## 2.23

不能，条件不足无法得知指针是否有效

## 2.24

因为p是void*指针类型，可用于存放任意对象的地址；而lp的类型不是int，不合法

## 2.25

(a) ip是int型的指针，i是int型的变量，r是int型的引用

(b) i是int型的变量，ip是一个空指针

(c) ip是int型的指针，ip2是int型的变量

## 2.35

第一个auto j 类型为int

第二个auto &k 类型为const int &

第三个auto *p 类型为const int *

第四个auto j2 类型为const int

第五个auto &k2 类型为const int &

## 3.4

比较其是否相等并输出较大的那个字符串：

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{	
	string s1, s2;
	cin >> s1 >> s2;
	if (s1 != s2)
	{
		cout << (s1 >= s2 ? s1 : s2) << endl;
	}
	return 0;
}
```

比较其是否等长并输出长度较大的那个字符串：

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{	
	string s1, s2;
	cin >> s1 >> s2;
	if (s1.size() != s2.size())
	{
		cout << (s1.size() >= s2.size() ? s1 : s2) << endl;
	}
	return 0;
}
```

## 3.5

输出连接成的大字符串：

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
	string s, sum;
	while (getline(cin,s))
	{
		sum += s;
		cout << sum << endl;
	}
	return 0;
}
```

用空格把输入的多个字符串分隔开来：

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
	string s, sum;
	while (getline(cin,s))
	{
		sum += s + " ";
		cout << sum << endl;
	}
	return 0;
}
```

## 3.20

``` c++
#include <iostream>
#include <vector>
using namespace std;
int main()
{
	vector<int> ivec;
	for (int i = 0; i < 10 ; i++)
	{
		ivec.push_back(i);
	}
	int sum[10];
	for (int k = 0; k < 10 ; k++)
	{
		sum[k] = ivec[k] + ivec[k+1];
		cout << sum[k] << endl;
		k++;
	}
	return 0;
}
```

改写：

```c++
#include <iostream>
#include <vector>
using namespace std;
int main()
{
	vector<int> ivec;
	for (int i = 0; i < 10 ; i++)
	{
		ivec.push_back(i);
	}
	int sum[10];
	for (int k = 0; k < 5 ; k++)
	{
		sum[k] = ivec[k] + ivec[9-k];
		cout << sum[k] << endl;
	}
	return 0;
}
```

## 3.23

```c++
#include <iostream>
#include <vector>
using namespace std;
int main()
{
	vector<int> text;
	for (int i = 0; i < 10 ; i++)
        text.push_back(i);
	for (auto it = text.begin() ; it != text.end() ; it++)
	{
		*it = *it * 2;
		cout << *it << endl;
	}
	return 0;
}
```

## 6.10

```c++
#include <iostream>
#include <vector>
using namespace std;

void exchange(int &a, int &b)
{
    int t;
    t = a;
    a = b;
    b = t;
}

int main()
{
    int m, n;
    cin >> m >> n;
    exchange(m,n);
    cout << m << " " << n;
	return 0;
}
```

## 6.19

(a)  不合法，函数只有一个形参，但传入了两个实参

(b) 合法

(c) 合法

(d) 合法

## 6.39

(a) 非法声明，顶层const不影响传入函数的对象。一个拥有顶层const的形参无法和另一个没有顶层const的形参区分开来

(b) 非法声明，不允许两个函数除了返回类型外其他所有的要素都相同

(c) 合法，返回类型和形参类型不同

## 7.16

一个类可以包含0个或多个访问说明符，而且对于某个访问说明符能出现多少次也没有严格限定。每个访问说明符指定了接下来的成员的访问级别，其有效范围直到出现下一个访问说明符或者到达类的结尾为止。

构造函数和部分成员函数紧跟在public说明符之后；而数据成员和作为实现部分的函数则跟在private说明符后面。

## 7.27

```c++
#include <iostream>
#include <string>
using namespace std;

class Screen{
public:
    typedef std::string::size_type pos;
    Screen() = default; // 因为Screen有另一个构造函数，
                        // 所以本函数是必需的
    // cursor被其类内初始值初始化为0
    Screen(pos ht, pos wd, char c) : height(ht), width(wd), contents(ht * wd, c){}
    char get() const                           // 读取光标处的字符
        { return contents[cursor]; }           // 隐式内联
    inline char get(pos ht, pos wd) const;     // 显式内联
    Screen &move(pos r, pos c);                // 能在之后被设为内联
    Screen &set(char);
    Screen &set(pos, pos, char);
    Screen &display(std::ostream &os)
    {
        do_display(os);
        return *this;
    }
    const Screen &display(std::ostream &os) const
    {
        do_display(os);
        return *this;
    }
private:
    pos cursor = 0;
    pos height = 0, width = 0;
    std::string contents;
    void do_display(std::ostream &os) const { os << contents; }
};

inline Screen &Screen::move(pos r, pos c)
{
    pos row = r * width;
    cursor = row + c;
    return *this;
}
char Screen::get(pos r, pos c) const
{
    pos row = r * width;
    return contents[row + c];
}
inline Screen &Screen::set(char c)
{
    contents[cursor] = c;
    return *this;
}
inline Screen &Screen::set(pos r, pos col, char ch)
{
    contents[r*width + col] = ch;
    return *this;
}

int main()
{
    Screen s1(20,20,'F');
    cout << s1.get(2,3) << endl;

    Screen myScreen(5,5,'X');
    myScreen.move(4,0).set('#').display(cout);
    cout << "\n";
    myScreen.display(cout);
    cout << "\n";
    return 0;
}
```

## 7.49

(a) 合法

(b) 不合法，Sales_data&与Sales_data类型不可转换

(c) 不合法，const不对，因为combine是需要改变传入参数的

## 7.58

rate应该是一个const类型

vec不必在类内定义好大小