# Set/multiset容器

## 简介

- 所有元素都会在插入时自动排序

### 本质

- set/multiset属于关联式容器，底层结构是二叉树

### set和multiset的区别

- set不允许容器中有重复的元素
- multiset允许容器中有重复的元素

## Set构造和赋值

```c++
#include <bits/stdc++.h>
using namespace std;

void printSet(multiset<int> &s)
{
    for (multiset<int>::iterator it = s.begin(); it != s.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    multiset<int> s1;

    //插入数据只有insert数据
    s1.insert(10);
    s1.insert(40);
    s1.insert(30);
    s1.insert(20);
    s1.insert(30);

    //set容器在插入时自动排序
    //set容器不允许插入重复值
    printSet(s1);

    //拷贝构造
    multiset<int> s2(s1);
    printSet(s2);


    //赋值
    multiset<int> s3;
    s3 = s2;
    printSet(s3);
}

int main()
{
    test01();
    return 0;
}
```

## set大小和交换

```c++
#include <bits/stdc++.h>
using namespace std;

void printSet(set<int> s)
{
    for (set<int>::iterator it = s.begin(); it != s.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    set<int> s1;

    //插入数据
    s1.insert(10);
    s1.insert(30);
    s1.insert(20);
    s1.insert(40);

    printSet(s1);

    //判断是否为空
    if (s1.empty())
    {
        cout << "为空" << endl;
    }
    else
    {
        cout << "不为空" << endl;
        cout << "大小为:" << s1.size() << endl;
    }
}

void test02()
{
    set<int> s1, s2;
    s1.insert(10);
    s1.insert(30);
    s1.insert(20);
    s1.insert(40);

    s2.insert(100);
    s2.insert(200);
    s2.insert(300);
    s2.insert(400);

    printSet(s1);
    printSet(s2);
    s1.swap(s2);
    printSet(s1);
    printSet(s2);
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
#include <bits/stdc++.h>
using namespace std;

void printSet(set<int> s)
{
    for (set<int>::iterator it = s.begin(); it != s.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    set<int> s1;

    s1.insert(30);
    s1.insert(10);
    s1.insert(20);
    s1.insert(40);

    printSet(s1);

    //删除
    s1.erase(s1.begin());
    printSet(s1);

    //删除重载版本
    s1.erase(30);
    printSet(s1);

    //清空
    // s1.erase(s1.begin(), s1.end());
    s1.clear();
    printSet(s1);
}

int main()
{
    test01();
    return 0;
}
```

## 查找和统计

```c++
find(key);//查找key是否存在，若存在，返回改键的元素的迭代器，如不存在返回set.end()
count(key);//统计key元素的个数
```

```c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    //查找
    set<int> s1;
    s1.insert(10);
    s1.insert(40);
    s1.insert(20);
    s1.insert(300);

    //查找
    set<int>::iterator pos = s1.find(30);
    if(pos != s1.end())
        cout << "找到元素" << *pos << endl;
    else
        cout << "未找到" << endl;
}

void test02()
{
    set<int> s1;
    s1.insert(10);
    s1.insert(40);
    s1.insert(20);
    s1.insert(30);
    s1.insert(30);
    s1.insert(30);

    int num = s1.count(30);

    //对于set而言统计的结果要么是0要么是1
    cout << num << endl;
}

int main()
{
    // test01();
    test02();
    return 0;
}
```

## set和mutiset

```c++
#include <bits/stdc++.h>
using namespace std;

int test01()
{
    set<int> s;

    pair<set<int>::iterator, bool> ret = s.insert(10);
    if(ret.second)
        cout << "succeed" << endl;
    else
        cout << "failed" << endl;

    ret = s.insert(10);
    if(ret.second)
        cout << "succeed" << endl;
    else
        cout << "failed" << endl;

    //允许插入重复的值
    multiset<int> ms;
    ms.insert(10);
    ms.insert(10);

    for (multiset<int>::iterator it = ms.begin(); it != ms.end(); it++)
        cout << *it << endl;
    cout << endl;
}

int main()
{
    test01();
    return 0;
}
```

