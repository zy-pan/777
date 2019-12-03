## 9.11

vector<int> vec;    // 0
vector<int> vec(10);    // 0
vector<int> vec(10,1);  // 1
vector<int> vec{1,2,3,4,5}; // 1,2,3,4,5
vector<int> vec(other_vec); // same as other_vec
vector<int> vec(other_vec.begin(), other_vec.end());  // same as other_vec

## 9.20

```c++
#include<iostream>
#include<fstream>
#include<sstream>
#include<string>
#include<vector>
#include<list>
#include<deque>
using namespace std;

int main(int argc, char**argv)
{
    list<int> list1(5,7);
    deque<int> deque1;
    deque<int> deque2;
    list<int>::iterator it1 = list1.begin();
    for (it1; it1 != list1.end(); it1++)
    {
        if ((*it1)%2 == 0)
        {
        deque1.push_back(*it1);
        }
        else
        {
            deque2.push_back(*it1);
        }
    }

    deque<int>::iterator it2 = deque1.begin();
    deque<int>::iterator it3 = deque2.begin();
    cout << "偶数为：";
    for (it2; it2 != deque1.end(); it2++)
    {
        cout << *it2 << " ";
    }
    cout << endl;
    cout << "奇数为：";
    for (it3; it3 != deque2.end(); it3++)
    {
        cout << *it3 << " ";
    }

    return 0;
}
```

## 9.29

resize(100)会将其大小改为100个元素的大小，添加新元素并初始化，之后使用resize(10)会将之后的90个元素舍弃。

## 9.43

```c++
#include<iostream>
#include<fstream>
#include<sstream>
#include<string>
#include<vector>
#include<forward_list>
using namespace std;

void func(string &s, string &oldVal, string &newVal)
{
    int _size = oldVal.size();
    string::iterator it1 = s.begin();
    string::iterator it2 = newVal.begin();
    string::iterator it3 = newVal.end();

    for (it1; it1 <= (s.end()-oldVal.size()+1); ++it1)
    {
        if((s.substr(it1-s.begin(),_size) == oldVal))
        {
            it1 = s.erase(it1,it1+_size);
            it1 = s.insert(it1, it2, it3);
            advance(it1,_size);
        }
    }
}

int main(int argc, char**argv)
{
    string s = "abcdefg";
    string oldval = "c";
    string newval = "c++";
    func(s, oldval, newval);
    cout << s << endl;
    return 0;
}
```

## 9.52

```c++
#include<iostream>
#include<stack>
#include<fstream>
#include<string>
using namespace std;
int main()
{
    int flag = 0;
    string line = "abc(d(e)f)(g)";
    stack<char> stack1;

    for (auto s : line)
    {
        stack1.push(s);
        if (s == '(')
            ++flag;
        if (s == ')' && flag)
        {
            while (stack1.top()!= '(')
            {
                stack1.pop();
            }
            stack1.pop();
            --flag;
            stack1.push('+');  // 用+代替原括号内的内容
        }
    }

    string s1;
    while (!stack1.empty())
    {
        s1.insert(s1.begin(), stack1.top());
        stack1.pop();
    }
    for (auto s : s1)
    cout << s;
    return 0;
}
```

## 10.3

```c++
#include<iostream>
#include<string>
#include<vector>
#include<algorithm>
#include<numeric>
using namespace std;

int main(int argc, char**argv)
{
    int a[10] = {1,2,2,3,3,4,5,6,7,7};
    vector<int> vec(a,a+10);
    cout << "元素之和为：" << accumulate(vec.begin(),vec.end(),0);
    return 0;
}
```

## 10.15

```c++
[a](int &b){ cout << a + b; }
```

## 10.34

```c++
#include<iostream>
#include<iterator>
#include<vector>
using namespace std;

int main()
{
    vector<int> vec = {1,2,3,4,5,6,7};
    for (auto iter = vec.rbegin(); iter != vec.rend(); ++iter)
        cout << *iter << " ";
    return 0;
}
```

## 10.42

```c++
#include<iostream>
#include<iterator>
#include<list>
using namespace std;

int main(int argc, char**argv)
{
    string a[10] = {"fox","jumps","over","quick","red","red","slow","the","the","turtle"};
    list<string> list1(a,a+10);
    list1.sort();
    list1.unique();
    for (auto it1 = list1.begin(); it1 != list1.end(); ++it1)
{
        cout << *it1 << " ";
    }
    return 0;
}
```

## 11.17

参考P358页，关于插入迭代器的讲解，第三个back_inserter()需要使用push_back()，而set不能够使用push_back()

## 11.38

```c++
void word_counting()
{
    unordered_map<string, size_t> word_count;
    string word;
    while (cin >> word)
        ++word_count[word];
    for (const auto &w : word_count)
        cout << w.first << " occurs " << w.second
             << ((w.second > 1) ? " times" : " time") << endl;
}
```

```c++
void word_transform(ifstream &ifs_map, ifstream &ifs_content)
{
    unordered_map<string, string> trans_map;
    for (string key, value; ifs_map >> key && getline(ifs_map, value);)
        if (value.size() > 1)
            trans_map[key] =
                value.substr(1).substr(0, value.find_last_not_of(' '));

    for (string text, word; getline(ifs_content, text); cout << endl)
        for (istringstream iss(text); iss >> word;)
        {
            auto map_it = trans_map.find(word);
            cout << (map_it == trans_map.cend() ? word : map_it->second) << " ";
        }
}
```

## 13.12

析构函数执行三次：accum，item1，item2

## 13.18

ex13_18.h

```c++
#ifndef CP5_ex13_18_h
#define CP5_ex13_18_h

#include <string>
using std::string;
 
class Employee {
public:
    Employee();
    Employee(const string &name);

    const int id() const { return id_; }

private:
    string name_;
    int id_;
    static int s_increment;
};

#endif
```

ex13_18.cpp

```c++
#include "ex13_18.h"

int Employee::s_increment = 0;

Employee::Employee()
{
    id_ = s_increment++;
}

Employee::Employee(const string &name)
{
    id_ = s_increment++;
    name_ = name;
}
```

## 13.46

(a)：f()为函数的返回值，临时值，属于右值，&&

(b)：vi[0]为变量，属于左值，&

(c)：r1为变量，属于左值，&

(d)：右侧为表达式，属于右值，&&

## 13.49

StrVec

```c++
StrVec::StrVec(StrVec&& s) NOEXCEPT : elements(s.elements),
                                      first_free(s.first_free),
                                      cap(s.cap)
{
    s.elements = s.first_free = s.cap = nullptr;
}
 
StrVec& StrVec::operator=(StrVec&& rhs) NOEXCEPT
{
    if (this != &rhs) {
        free();
        elements = rhs.elements;
        first_free = rhs.first_free;
        cap = rhs.cap;
        rhs.elements = rhs.first_free = rhs.cap = nullptr;
    }
    return *this;
}
```

String

```c++
String& String::operator=(const String& rhs)
{
    auto newstr = alloc_n_copy(rhs.elements, rhs.end);
    free();
    elements = newstr.first;
    end = newstr.second;
    return *this;
}
 
String::String(String&& s) NOEXCEPT : elements(s.elements), end(s.end)
{
    s.elements = s.end = nullptr;
}
 
String& String::operator=(String&& rhs) NOEXCEPT
{
    if (this != &rhs) {
        free();
        elements = rhs.elements;
        end = rhs.end;
        rhs.elements = rhs.end = nullptr;
    }
    return *this;
}
```

Message

```c++
Folder::Folder(Folder&& f)
{
    move_Messages(&f);
}
 
Folder& Folder::operator=(Folder&& f)
{
    if (this != &f) {
        remove_from_Messages();
        move_Messages(&f);
    }
    return *this;
}
```

## 13.58

```c++
#include <vector>
#include <iostream>
#include <algorithm>
 
using std::vector;
using std::sort;
 
class Foo {
public:
    Foo sorted()&&;
    Foo sorted() const&;
 
private:
    vector<int> data;
};
 
Foo Foo::sorted() &&
{
    sort(data.begin(), data.end());
    std::cout << "&&" << std::endl; // debug
    return *this;
}
 
Foo Foo::sorted() const &
{
    //    Foo ret(*this);
    //    sort(ret.data.begin(), ret.data.end());
    //    return ret;
 
    std::cout << "const &" << std::endl; // debug
 
    //    Foo ret(*this);
    //    ret.sorted();     // Exercise 13.56
    //    return ret;
 
    return Foo(*this).sorted(); // Exercise 13.57
}
 
int main()
{
    Foo().sorted(); // call "&&"
    Foo f;
    f.sorted(); // call "const &"
}
```

## 14.3

(a)：应用了C++内置版本的==，比较两个指针

(b)：应用了string版本的==

(c)：应用了vector版本的==

(d)：应用了string版本的==

## 14.20

```c++
class Sales_data
{
    friend Sales_data operator+(const Sales_data &lhs, const Sales_data &rhs);
public:
    Sales_data& operator+=(const Sales_data &rhs);
};
Sales_data operator+(const Sales_data &lhs, const Sales_data &rhs)
{
    Sales_data sum = lhs;
    sum += rhs;
    return sum;
}
Sales_data& Sales_data::operator+=(const Sales_data &rhs)
{
    units_sold += rhs.units_sold;
    revenue += rhs.revenue;
    return *this;
}
```

## 14.38

```c++
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class StrLenIs
{
public:
    StrLenIs(int len) : len(len) { }
    bool operator()(const string &str) { return str.length() == len; }
private:
    int len;
};

void readStr(istream &is, vector<string> &vec)
{
    string word;
    while (is >> word)
    {
        vec.push_back(word);
    }
}

int main()
{
    vector<string> vec;
    readStr(cin, vec);
    const int minLen = 1;
    const int maxLen = 10;
    for (int i = minLen; i <= maxLen; ++i)
    {
        StrLenIs slenIs(i);
        cout << "len: " << i << ", cnt: " << count_if(vec.begin(), vec.end(), slenIs) << endl;
    }
    return 0;
}
```

## 14.52

```c++
struct longDouble {
    longDouble operator+(const SmallInt&);
};
longDouble operator+(longDouble&, double);
SmallInt si;
longDouble ld;
ld = si + ld;
ld = ld + si;
```

## 15.13

```c++
base::print(os);
```

## 15.16

```c++
class limit_quote : public Disc_quote
{
public:
    limit_quote();//默认构造函数
    limit_quote(const string&book, double price, size_t qty,double disc):Disc_quote(book,price,qty,disc){}//构造函数,直接调用基类的构造函数
    double net_price(size_t n) const
    {
        if (n > quantity)
        {
            return quantity*(1-_discount)*price+(n-quantity)*price;
        } 
        else
        {
            return quantity*(1-_discount)*price;
        }
    }
};
```

## 15.30

Basket.h

```c++
class Basket  
{  
    static bool compare(const std::shared_ptr<Quote> rhs, const std::shared_ptr<Quote> lhs) {  
        return rhs->isbn() < lhs->isbn();  
    }
    std::multiset<std::shared_ptr<Quote>, decltype(compare)*> items{ compare }; 
public:  
    void add_item(const std::shared_ptr<Quote> &sale) {  
        items.insert(sale);  
    }  
    void add_item(const Quote &q)  
    {  
        items.insert(std::shared_ptr<Quote>(q.clone()));  
    }  
    void add_item(Quote &&q)  
    {  
        items.insert(std::shared_ptr<Quote>(std::move(q).clone()));  
    }  
    double total_receipt(std::ostream &os)const;  
}; 
```

Basket.cpp

```c++
double Basket::total_receipt(std::ostream &os)const  
{  
    double sum = 0.0;                                        
    for (auto iter = items.cbegin(); iter != items.cend(); iter = items.upper_bound(*iter))  
    {  
        sum += print_total(os, **iter, items.count(*iter)); 
    }  
    os << "Total Sale: " << sum << std::endl;  
    return sum;  
}
```
