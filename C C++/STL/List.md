# List链表

## 构造函数

```c++
#include <bits/stdc++.h>
using namespace std;

void printList(const list<int> &L)
{
    for (list<int>::const_iterator it = L.begin(); it != L.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    //创建list容器
    list<int> l1;

    //添加数据
    l1.push_back(10);
    l1.push_back(20);
    l1.push_back(30);
    l1.push_back(40);

    //遍历容器
    printList(l1);

    //区间方式构造
    list<int> l2(l1.begin(), l1.end());
    printList(l2);

    //拷贝构造
    list<int> l3(l2);
    printList(l3);

    //n个elem
    list<int> l4(10, 1000);
    printList(l4);
}

int main()
{
    test01();
    return 0;
}
```

## 赋值和交换

```c++
#include <bits/stdc++.h>
using namespace std;

void printList(const list<int> &L)
{
    for (list<int>::const_iterator it = L.begin(); it != L.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    //创建list容器
    list<int> l1;

    //添加数据
    l1.push_back(10);
    l1.push_back(20);
    l1.push_back(30);
    l1.push_back(40);

    printList(l1);
    list<int> l2;
    l2 = l1;//operator = 赋值
    printList(l2);

    list<int> l3;
    l3.assign(l2.begin(), l2.end());
    printList(l3);

    list<int> l4;
    l4.assign(10, 100);
    printList(l4);
}

//交换
void test02()
{
    list<int> l1;

    l1.push_back(10);
    l1.push_back(20);
    l1.push_back(30);
    l1.push_back(40);

    list<int> l2;
    l2.assign(10, 100);

    cout << "交换前" << endl;
    printList(l1);
    printList(l2);

    l1.swap(l2);
    cout << "交换后" << endl;
    printList(l1);
    printList(l2);
}
int main()
{
    test01();
    // test02();
    return 0;
}
```

## 大小操作

```c++
#include <bits/stdc++.h>
using namespace std;

void printList(const list<int> &L)
{
    for (list<int>::const_iterator it = L.begin(); it != L.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    list<int> l1;
    l1.push_back(10);
    l1.push_back(20);
    l1.push_back(30);
    l1.push_back(40);

    printList(l1);

    if (l1.empty())
    {
        cout << "l1为空" << endl;
    }
    else
    {
        cout << "l1不为空" << endl;
        cout << "l1的元素个数为:" << l1.size() << endl;
    }

    //重新指定大小
    l1.resize(10,10000);
    printList(l1);

    l1.resize(2);
    printList(l1);
}

int main()
{
    test01();
    return 0;
}
```

## 插入删除

```c++
push_back(elem);  		//在容器尾部加入一个元素
pop_back();				//删除容器中最后一个元素
push_front();			//在容器开头插入一个元素
pop_front();			//在容器开头删除第一个元素
insert(pos,elem);		//在pos位置插入elem元素的拷贝，返回新数据的位置
insert(pos,n,elem);		//在pos位置插入n个elem数据，无返回值
insert(pos,beg,end);	//在pos位置插入[beg,end)区间的数据，无返回值
clear();				//移除容器的所有数据
erase(beg,end);			//删除[beg,end)区间的数据，返回下一个数据的位置
erase(pos);				//删除pos位置的数据，返回下一个数据的位置
remove(elem);			//删除容器中所有与elem值匹配的元素
```

```c++
#include <bits/stdc++.h>
using namespace std;

void printList(const list<int> L)
{
    for (list<int>::const_iterator it = L.begin(); it != L.end(); it++)
        cout << *it << " ";
    cout << endl;
}
//list容器的插入和删除
void test01()
{
    list<int> L;

    //尾插
    L.push_back(10);
    L.push_back(20);
    L.push_back(30);

    //头插
    L.push_front(100);
    L.push_front(200);
    L.push_front(300);

    //300 200 100 10 20 30
    printList(L);

    //尾删
    L.pop_back();

    //300 200 100 10 20
    printList(L);

    //头删
    L.pop_front();

    //200 100 10 20
    printList(L);

    //insert插入
    L.insert(++L.begin(), 1000);

    //200 1000 100 10 20
    printList(L);

    //删除
    L.erase(++L.begin());
    printList(L);

    //移除
    L.push_back(10000);
    L.push_back(10000);
    L.push_back(10000);
    L.push_back(10000);
    printList(L);

    //删除全部的10000
    L.remove(10000);
    printList(L);

    L.clear();
    printList(L);
}

int main()
{
    test01();
    return 0;
}
```

## 数据存取

```c++
#include <bits/stdc++.h>
using namespace std;


void printList(const list<int> L)
{
    for (list<int>::const_iterator it = L.begin(); it != L.end(); it++)
        cout << *it << endl;
    cout << endl;
}
void test01()
{
    list<int> l1;
    l1.push_back(10);
    l1.push_back(20);
    l1.push_back(30);
    l1.push_back(40);


    //list容器不可以用中括号和at来访问
    cout << l1.front() << endl;
    cout << l1.back() << endl;
}

int main()
{
    test01();
    return 0;
}
```

## 反转和排序

```c++
#include <bits/stdc++.h>
using namespace std;

void printList(const list<int> L)
{
    for (list<int>::const_iterator it = L.begin(); it != L.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    //反转
    list<int> l1;

    l1.push_back(20);
    l1.push_back(10);
    l1.push_back(50);
    l1.push_back(40);
    l1.push_back(30);

    printList(l1);

    //反转操作
    l1.reverse();
    printList(l1);
}

bool cmp(int x, int y)
{
    return x > y;
}
//排序
void test02()
{
    list<int> l1;
    l1.push_back(20);
    l1.push_back(10);
    l1.push_back(50);
    l1.push_back(40);
    l1.push_back(30);

    printList(l1);

    //所有不支持随街访问的迭代器的容器，不可以用标准算法，但可以用内部的算法
    // sort(l1.begin(), l1.end());//报错 因为list容器不可以随机访问
    l1.sort(cmp);//默认从小到大
    printList(l1);
}

int main()
{
    // test01();
    test02();
    return 0;
}
```

## pair数组构造

```c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    //1
    pair<string, int> p("Tom", 20);
    cout << p.first << " " << p.second << endl;

    //2
    pair<string, int> p2 = make_pair("Jerry", 30);
    cout << p2.first << " " << p2.second << endl;
}

int main()
{
    test01();
    return 0;
}
```

## set容器排序(修改排序规则)

```c++
#include <bits/stdc++.h>
using namespace std;

class MyCompare
{
public:
    bool operator()(int v1, int v2)
    {
        return v1 > v2;
    }
};

void test01()
{
    set<int> s1;
    s1.insert(10);
    s1.insert(20);
    s1.insert(30);
    s1.insert(40);
    s1.insert(50);

    for (set<int>::iterator it = s1.begin(); it != s1.end(); it++)
        cout << *it << " ";
    cout << endl;

    //指定排序规则为从大到小
    set<int, MyCompare> s2; 
    s2.insert(10);
    s2.insert(20);
    s2.insert(30);
    s2.insert(40);
    s2.insert(50);

     for (set<int>::iterator it = s2.begin(); it != s2.end(); it++)
        cout << *it << " ";
    cout << endl;
}

int main()
{
    test01();
    return 0;
}
```

```c++
#include <bits/stdc++.h>
using namespace std;

class Person
{
public:
    Person(string name, int age)
    {
        this->m_Age = age;
        this->m_Name = name;
    }
    string m_Name;
    int m_Age;
};

class MyCompare
{
public:
    bool operator()(const Person&p1, const Person &p2)
    {
        return p1.m_Age > p2.m_Age;
    }
};

void test01()
{
    //自定义的数据类型，都会指定排序规则
    set<Person, MyCompare> s;
    Person p1("刘备", 24);
    Person p2("关羽", 28);
    Person p3("张飞", 25);
    Person p4("赵云", 21);

    s.insert(p1);
    s.insert(p2);
    s.insert(p3);
    s.insert(p4);

    for (set<Person, MyCompare>::iterator it = s.begin(); it != s.end(); it++)
        cout << "name:" << it->m_Name << " age:" << it->m_Age << endl;
}

int main()
{
    test01();
    return 0;
}
```

