## 16.11

ListItem使用时必须加上<>模版参数列表

```c++
void insert(ListItem<elemType> *ptr, elemType value);
ListItem<elemType> *front, *end;
```

## 16.12

```c++
#ifndef STRBLOB_H_
#define STRBLOB_H_

#include <string>
#include <initializer_list>
#include <memory>
#include <vector>
#include <stdexcept>

template <typename T>
class ConstStrBlobPtr;

template <typename T>
class StrBlob
{
public:
    friend class ConstStrBlobPtr<T>;

    typedef typename std::vector<T>::size_type size_type;

    StrBlob();
    StrBlob(std::initializer_list<T> il);
    size_type size() const { return data->size(); }
    bool empty() const { return data->empty(); }
    void push_back(const T &t) { data->push_back(t); }
    void pop_back();
    T& front();
    T& back();
    const T& front() const;
    const T& back() const;
    ConstStrBlobPtr<T> begin();
    ConstStrBlobPtr<T> end();
private:
    std::shared_ptr<std::vector<T>> data;
    void check(size_type i, const T &msg) const;
};

template <typename T>
class ConstStrBlobPtr
{
public:
    ConstStrBlobPtr<T>() : curr(0){};
    ConstStrBlobPtr<T>(const StrBlob<T> &a, size_t sz = 0) : wptr(a.data), curr(sz) {}
    T& deref() const;
    ConstStrBlobPtr<T>& incr();
private:
    std::shared_ptr<std::vector<T>> check(std::size_t, const T&) const;
    std::weak_ptr<std::vector<T>> wptr;
    std::size_t curr;
};

template <typename T>
std::shared_ptr<std::vector<T>> ConstStrBlobPtr<T>::check(std::size_t i, const T &msg) const
{
    auto ret = wptr.lock();
    if(!ret)
        throw std::runtime_error("unbound ConstStrBlobPtr<T>");
    if(i >= ret->size())
        throw std::out_of_range(msg);
    return ret;
}

template <typename T>
T& ConstStrBlobPtr<T>::deref() const
{
    auto p = check(curr, "dereference past end");
    return (*p)[curr];
}

template <typename T>
ConstStrBlobPtr<T>& ConstStrBlobPtr<T>::incr()
{
    check(curr, "increment past end of ConstStrBlobPtr<T>");
    ++curr;
    return *this;
}

template <typename T>
StrBlob<T>::StrBlob() : data(std::make_shared<std::vector<T>>()){}

template <typename T>
StrBlob<T>::StrBlob(std::initializer_list<T> il) : data(std::make_shared<std::vector<T>>(il)){}

template <typename T>
void StrBlob<T>::check(size_type i, const T &msg) const
{
    if(i >= data->size())
        throw std::out_of_range(msg);
}

template <typename T>
T & StrBlob<T>::front()
{
    check(0, "front on empty StrBlob");
    return data->front();
}

template <typename T>
T & StrBlob<T>::back()
{
    check(0, "back on empty StrBlob");
    return data->back();
}

template <typename T>
const T& StrBlob<T>::front() const
{
    check(0, "front on empty StrBlob");
    return data->front();
}

template <typename T>
const T& StrBlob<T>::back() const
{
    check(0, "back on empty StrBlob");
    return data->back();
}

template <typename T>
void StrBlob<T>::pop_back()
{
    check(0, "pop_back on empty StrBlob");
    data->pop_back();
}

template <typename T>
ConstStrBlobPtr<T> StrBlob<T>::begin() { return ConstStrBlobPtr<T>(*this); }

template <typename T>
ConstStrBlobPtr<T> StrBlob<T>::end()
{
    auto ret = ConstStrBlobPtr<T>(*this, data->size());
    return ret;
}

#endif
```

## 16.19

```c++
#include <iostream>
#include <vector>

template <typename Container>
std::ostream& print(const Container& c, std::ostream& os = std::cout)
{
    using size_type = typename Container::size_type;
    for (size_type i = 0; i != c.size(); ++i) {
        os << c.at(i) << " ";
    }
    return os;
}

int main()
{
    std::vector<int> vec{2, 4, 6, 8, 7, 5, 3, 1};
    print(vec) << "\n";
}
```

## 16.41

```c++
template <typename T1, typename T2>
auto sum(T1 a, T2 b) -> decltype(a + b) {
    return a + b;
}
```

## 16.62

Sales_data.h

```c++
#ifndef SALES_DATA_H_
#define SALES_DATA_H_

#include <string>

struct Sales_data;

std::istream &operator>>(std::istream &is, Sales_data &item);
std::ostream &operator<<(std::ostream &os, const Sales_data &item);
Sales_data operator+(const Sales_data &lhs, const Sales_data &rhs);

struct Sales_data
{
friend std::istream& operator>>(std::istream&, Sales_data&);
friend std::ostream& operator<<(std::ostream&, const Sales_data&);
friend Sales_data operator+(const Sales_data&, const Sales_data&);
friend bool operator==(const Sales_data&, const Sales_data&);
friend class std::hash<Sales_data>;
public:
    Sales_data(const std::string &s, unsigned n, double p) : bookNo(s), units_sold(n), revenue(p*n){std::cout << "Sales_data(const std::string &s, unsigned n, double p)" << std::endl;}
    Sales_data() : Sales_data("", 0, 0){std::cout << "Sales_data() : Sales_data(\"\", 0, 0)" << std::endl;}
    Sales_data(const std::string &s) : Sales_data(s, 0, 0){std::cout << "Sales_data(const std::string &s) : Sales_data" << std::endl;}
    Sales_data(std::istream &is) : Sales_data(){/*read(is, *this);*/ is >> *this; std::cout << "Sales_data(std::istream &is) : Sales_data()" << std::endl;}
    std::string isbn() const {return bookNo;}
    Sales_data& operator=(const std::string&);
    Sales_data& operator+=(const Sales_data&);
    Sales_data& operator-=(const Sales_data&);
private:
    inline double avg_price() const;

    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
};

inline double Sales_data::avg_price() const
{
    if(units_sold)
        return revenue / units_sold;
    else
        return 0;
}

Sales_data& Sales_data::operator=(const std::string &s)
{
    *this = Sales_data(s);
    return *this;
}

Sales_data& Sales_data::operator+=(const Sales_data &rhs)
{
    units_sold += rhs.units_sold;
    revenue += rhs.revenue;

    return *this;
}

Sales_data& Sales_data::operator-=(const Sales_data &rhs)
{
    units_sold -= rhs.units_sold;
    revenue -= rhs.revenue;

    return *this;
}

std::istream &operator>>(std::istream &is, Sales_data &item)
{
    double price = 0;

    is >> item.bookNo >> item.units_sold >> price;
    if(is)
        item.revenue = price * item.units_sold;
    else
        item = Sales_data();

    return is;
}

std::ostream &operator<<(std::ostream &os, const Sales_data &item)
{
    os << item.isbn() << " " << item.units_sold << " " << item.revenue << " " << item.avg_price();

    return os;
}

Sales_data operator+(const Sales_data &lhs, const Sales_data &rhs)
{
    Sales_data sum = lhs;
    sum += rhs;

    return sum;
}

bool operator==(const Sales_data &lhs, const Sales_data &rhs)
{
    return lhs.isbn() == rhs.isbn() && 
        lhs.units_sold == rhs.units_sold && 
        lhs.revenue == rhs.revenue;
}

#endif
```

```c++
#include <iostream>
#include <string>
#include "Sales_data.h"
#include <unordered_set>

namespace std
{
    template <>
    struct hash<Sales_data>
    {
        typedef size_t result_type;
        typedef Sales_data argument_type;
        size_t operator()(const Sales_data &s) const;
    };
    size_t hash<Sales_data>::operator()(const Sales_data &s) const
    {
        return hash<std::string>()(s.bookNo) ^ hash<unsigned>()(s.units_sold) ^ hash<double>()(s.revenue);
    }
}

int main()
{
    Sales_data sales_data1("001-01", 1, 100);
    Sales_data sales_data2;
    Sales_data sales_data3("001-02");
    std::cout << std::hex << std::hash<std::string>()("001-01") << std::endl;
    std::cout << std::hex << std::hash<unsigned>()(1) << std::endl;
    std::cout << std::hex << std::hash<double>()(100) << std::endl;

    std::unordered_multiset<Sales_data> SDset;
    SDset.emplace(sales_data1);
    SDset.emplace("001-03", 1, 200);
    SDset.emplace(sales_data3);

    for(const auto &item : SDset)
        std::cout << "the hash code of " << item.isbn() <<":\n0x" << std::hex << std::hash<Sales_data>()(item) << "\n";

    return 0;
}
```

## 12.1

b1,b2都包含有4个元素

## 12.10

正确

## 12.15

```c++
#include <string>
#include <memory>
#include <iostream>

struct destination
{
    std::string des;
    destination(std::string des_) : des(des_){}
};

struct connection
{
    std::string conn;
    connection(std::string conn_) : conn(conn_){}
};

connection connect(destination *des_)
{
    std::cout << "connect to " << des_->des << std::endl;
    return connection (des_->des);
}

void disconnect(connection conn_)
{
    std::cout << "disconnect " << conn_.conn << std::endl;
}

void end_connection(connection *p){ disconnect(*p); }

void f(destination &d)
{
    connection c = connect(&d);
    std::shared_ptr<connection> p(&c, [](connection *p){ disconnect(*p); });
    std::cout << "connecting now(" << p.use_count() << ")" << std::endl;
}

int main()
{
    destination des("aaa");
    f(des);

    return 0;
}
```

## 12.17

（a）不合法，ix不是new返回的指针；

（b）不合法，同（a）

（c）合法，unique_ptr必须采用直接初始化；

（d）不合法，同（a）

（e）合法；

（f）不合法，必须使用new返回的指针进行初始化，赋值和拷贝的操作也不包含get()方法。

## 12.18

多个shared_ptr可以指向同一个对象，直接赋值即可，无需release成员。

## 12.19

```c++
class StrBlob
{
public:
    friend class StrBlobPtr;
    StrBlobPtr begin();
    StrBlobPtr end();
    StrBlob();
    StrBlob(initializer_list<string> il):data(make_shared<vector<string>>(il)){}
    StrBlob(string il):data(make_shared<vector<string>> (il)){}
    typedef vector<string>::size_type size_type;
    
    size_type size() const
    {
        return data->size();
    }
    bool empty()
    {
        return data->empty();
    }
    void pushback(const string &s)
    {
        data->push_back(s);
    }
    string& front()
    {
        check(0,"front on empty StrBlob");
        return data->front();
    }
    string& back()
    {
        check(0,"back on empty StrBlob");
        return data->back();
    }
    void popback()
    {
        check(0,"pop_back on empty StrBlob");
        data->pop_back();
    }

private:
    shared_ptr<vector<string>> data;
    void check(size_type i,const string &msg) const
    {
        if (i > data->size())
        {
            throw out_of_range(msg);
        }
    }
};
 
class StrBlobPtr
{
public:
    StrBlobPtr():curr(0){}
    StrBlobPtr(StrBlob &a, size_t sz = 0):wptr(a.data),curr(sz){}
    string& deref() const
    {
        auto p =check(curr,"deference past end");
        return (*p)[curr];
    }
    StrBlobPtr& incr()
    {
        auto p =check(curr,"deference past end");
        ++curr;
        return *this;
    }
private:
    shared_ptr<vector<string>> check(size_t i,const string& msg) const
    {
        auto ret = wptr.lock();
        if(!ret)
        {
            throw runtime_error("未绑定");
        }
        if (i >= ret->size())
        {
            throw out_of_range(msg);
        }
        return ret;
    }
    weak_ptr<vector<string>> wptr;
    size_t curr;
};

StrBlobPtr StrBlob::begin()
{
    return StrBlobPtr(*this);
}
StrBlobPtr StrBlob::end()
{
    return StrBlobPtr(*this, data->size());
}
```

## 12.30

TextQuery_ex27.h

```c++
#ifndef TEXTQUERY_H_
#define TEXTQUERY_H_

#include <string>
#include <vector>
#include <map>
#include <fstream>
#include <sstream>
#include <set>
#include <memory>
#include <iostream>
#include <algorithm>
#include <iterator>

class QueryResult;

class TextQuery
{
public:
    using line_no = std::vector<std::string>::size_type;
    TextQuery(std::ifstream&);
    QueryResult query(const std::string&) const;
private:
    std::shared_ptr<std::vector<std::string>> file;
    std::map<std::string, std::shared_ptr<std::set<line_no>>> wm;
};

class QueryResult
{
    friend std::ostream& print(std::ostream&, const QueryResult&);
public:
    QueryResult(std::string s, std::shared_ptr<std::set<TextQuery::line_no>> p, std::shared_ptr<std::vector<std::string>> f) : sought(s), lines(p), file(f) { }
private:
    std::string sought;
    std::shared_ptr<std::set<TextQuery::line_no>> lines;
    std::shared_ptr<std::vector<std::string>> file;
};

TextQuery::TextQuery(std::ifstream &ifs) : file(new std::vector<std::string>)
{
    std::string text;

    while(std::getline(ifs, text))
    {
        file->push_back(text);
        int n = file->size() - 1;
        std::istringstream line(text);
        std::string text;
        while(line >> text)
        {
            std::string word;
            std::copy_if(text.begin(), text.end(), std::back_inserter(word), isalpha);
            auto &lines = wm[word];
            if(!lines)
                lines.reset(new std::set<line_no>);
            lines->insert(n);
        }
    }
}

QueryResult TextQuery::query(const std::string &sought) const
{
    static std::shared_ptr<std::set<TextQuery::line_no>> nodata(new std::set<TextQuery::line_no>);
    auto loc = wm.find(sought);
    if(loc == wm.end())
        return QueryResult(sought, nodata, file);
    else
        return QueryResult(sought, loc->second, file);
}

std::ostream &print(std::ostream &os, const QueryResult &qr)
{
    os << qr.sought << " occurs " << qr.lines->size() << " " /*<< make_plural(qr.lines->size(), "time", "s")*/ << std::endl;
    for(auto num : *qr.lines)
        os << "\t(line " << num + 1 << ") " << *(qr.file->begin() + num) << std::endl;
    return os;
}

#endif
```

ex27.cpp

```c++
#include <iostream>
#include <string>
#include "TextQuery_ex27.h"

void runQueries(std::ifstream &infile)
{
    TextQuery tq(infile);
    while (true) {
        std::cout << "enter word to look for, or q to quit: ";
        std::string s;
        if (!(std::cin >> s) || s == "q") break;
        print(std::cout, tq.query(s)) << std::endl;
    }
}

int main()
{
    std::ifstream file("storyDataFile");
    runQueries(file);
}
```
