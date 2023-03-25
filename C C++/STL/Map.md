# Map

## 简介

- map中所有元素都是pair

- pair中的第一个元素为key（键值），起到索引作用，第二个元素为value（实值）
- 所有元素都会根据元素的键值自动排序

### 本质

- map/multimap属于**关联式容器**，底层结构使用二叉树实现。

### 优点

- 可以根据key值快速找到value的值

map和multimap区别：

- map不允许容器中有重复key值元素
- multimap允许容器中有重复key值元素

## 构造和赋值

```c++
#include <map>
#include <iostream>
using namespace std;

void printMap(map<int,int> &m)
{
    for (map<int, int>::iterator it = m.begin(); it != m.end(); it++)
        cout << it->first << " " << it->second << endl;
    cout << endl;
}

void test01()
{
    //创建map容器
    map<int, int> m;

    m.insert(pair <int, int>(1, 10));
    m.insert(pair <int, int>(2, 10));
    m.insert(pair <int, int>(3, 10));
    m.insert(pair <int, int>(4, 10));

    printMap(m);
    //拷贝构造
    map<int,int>m2(m);
    printMap(m2);

    map<int, int> m3;
    m3 = m2;
    printMap(m3);
}

int main()
{
    test01();
    return 0;
}
```

## 大小和交换

```c++
#include <bits/stdc++.h>
using namespace std;

void printMap(map<int,int> &m)
{
    for (map<int, int>::iterator it = m.begin(); it != m.end(); it++)
        cout << it->first << " " << it->second << endl;
    cout << endl;
}

void test01()
{
    map<int, int> m;
    m.insert(pair<int, int>(1, 10));
    m.insert(pair<int, int>(2, 20));
    m.insert(pair<int, int>(3, 30));


    if(m.empty())
        cout << "为空" << endl;
    else
    {
        cout << "不为空" << endl;
        cout << "大小为" << m.size() << endl;
    }
}

void test02()
{
    map<int, int> m;
    m.insert(pair<int, int>(1, 10));
    m.insert(pair<int, int>(2, 20));
    m.insert(pair<int, int>(3, 30));

    map<int, int> m1;
    m1.insert(pair<int, int>(4, 100));
    m1.insert(pair<int, int>(5, 200));
    m1.insert(pair<int, int>(6, 300));

    printMap(m);
    printMap(m1);
    m.swap(m1);
    printMap(m);
    printMap(m1);
}

int main()
{
    // test01();
    test02();
    return 0;
}
```

## 插入和删除

```c++
#include <iostream>
#include <map>
using namespace std;

void printMap(map<int,int> &m)
{
    for (map<int, int>::iterator it = m.begin(); it != m.end(); it++)
        cout << it->first << " " << it->second << endl;
    cout << endl;
}

void test01()
{
    map<int, int> m;
    //插入
    //1
    m.insert(pair<int, int>(1, 10));
    //2
    m.insert(make_pair(2, 20));
    //3
    m.insert(map<int, int>::value_type(3, 30));
    //4
    m[4] = 40;
    printMap(m);

    //删除
    m.erase(m.begin());
    printMap(m);

    m.erase(3);//按照key来删除
    printMap(m);

    //清空
    //m.erase(m.begin(), m.end());
    m.clear();
    printMap(m);
}

int main()
{
    test01();
    return 0;
}
```

## 查找和统计

```c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    //查找
    map<int, int> m;
    m.insert(make_pair(1, 10));
    m.insert(make_pair(2, 20));
    m.insert(make_pair(3, 30));

    map<int,int>::iterator pos = m.find(3);

    if (pos != m.end())
    {
        cout << "found: " << pos->first << " " << pos->second << endl;
    }
    else
    {
        cout << "Not Found" << endl;
    }

    //统计
    //map不允许插入重复key元素，count统计而言结果要么是0要么是1
    //multimap的count可能大于1
    int n = m.count(4);
    cout << n << endl;
}

int main()
{
    test01();
    return 0;
}
```

## 排序

```c++
#include <bits/stdc++.h>
using namespace std;

class MyCompare
{
public:
    bool operator()(int x, int y)
    {
        return x > y;
    }
};

void test01()
{
    map<int, int, MyCompare> m;

    m.insert(make_pair(1, 10));
    m.insert(make_pair(2, 20));
    m.insert(make_pair(3, 30));
    m.insert(make_pair(4, 40));
    m.insert(make_pair(5, 50));

    for (map<int, int, MyCompare>::iterator it = m.begin(); it != m.end(); it++)
        cout << it->first << " " << it->second << endl;
}

int main()
{
    test01();
    return 0;
}
```

