# Deque基本概念

##### 功能：

- 双端数组，可以对头端进行插入删除操作

##### deque和vector的区别：

- vector对于头部的插入删除效率低，数据量越大，效率越低
- deque相对而言，对头部的插入删除速度比vector快
- vector访问元素时比deque快，这和两者内部实现有关 ![1](G:\C笔记\笔记\document\1.png)



# Deque构造函数

```c++
#include <bits/stdc++.h>
using namespace std;

void printDeque(const deque<int> d)
{
    for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    deque<int> d1;
    for (int i = 0; i < 10; i++)
        d1.push_back(i);
    printDeque(d1);

    deque<int> d2(d1.begin(), d1.end());//区间赋值
    printDeque(d2);

    deque<int> d3(10, 100);//将10个100赋值给d3
    printDeque(d3);

    deque<int> d4(d3);//将d3赋值给d4
    printDeque(d4);
}

int main()
{
    test01();
    return 0;
}
```

# Deque赋值操作

```c++
#include <bits/stdc++.h>
using namespace std;

void printDeque(const deque<int> d)
{
    for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    deque<int> d1;
    for (int i = 0; i < 10; i++)
        d1.push_back(i);
    printDeque(d1);

    //"="赋值
    deque<int> d2;
    d2 = d1;//将d1赋值给d2
    printDeque(d2);

    //assign
    deque<int> d3;
    d3.assign(d1.begin(), d1.end());//将d1的begin到d2的end的值赋值给d3
    printDeque(d3);

    deque<int> d4;
    d4.assign(10, 100);//将10个100赋值给d4
    printDeque(d4);

}
int main()
{
    test01();
    return 0;
}
```

# Deque大小

```c++
/*
empty();                判断容器是否为空
size();                 返回容器中元素的个数
resize(int num);        重新指定容器的长度，如果变长度，则以默认值填充
                        若变短，则末尾超过的删除
resize(int num,elem);   以elem填充
*/
#include <bits/stdc++.h>
using namespace std;

void printDeque(const deque<int> d)
{
    for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    deque<int> d1;
    for (int i = 0; i < 10; i++)
        d1.push_back(i);
    printDeque(d1);

    if (d1.empty())
        cout << "d1为空" << endl;
    else
    {
        cout << "d1不为空" << endl;
        cout << "大小:" << d1.size() << endl;
    }

    //重新指定大小
    // d1.resize(15);
    d1.resize(15,1);//重新指定大小并用1填充
    printDeque(d1);

    d1.resize(5);//重新指定为5超出部分删除
    printDeque(d1);
}

int main()
{
    test01();
    return 0;
}
```

# Deque插入和删除

```c++
插入
push_back(elem);  //在容器尾部添加一个数据
push_front(elem);//在容器头部插入一个数据
pop_back();		//删除容器最后一个数据
pop_front(); 	//删除容器第一个数据

指定位置操作
insert(pos, elem); //在pos位置插入一个elem元素的的拷贝，返回新数据的位置
insert(pos, n, elem);//在pos位置插入n个elem数据，无返回值
insert(pos, beg, end);//在pos位置插入[beg,end)区间的数据，无返回值
clear();			//清空容器的所有位置
erase(beg, end);	//删除[beg, end)区间的数据， 返回下一个数据的位置
erase(pos);			//删除pos位置的数据，返回下一个数据的位置
```

```c++
#include <bits/stdc++.h>
using namespace std;

void printDeque(const deque<int> d)
{
    for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    deque<int> d1;

    //尾插
    d1.push_back(10);
    d1.push_back(20);

    //头插
    d1.push_front(100);
    d1.push_front(200);

    //200 100 10 20
    printDeque(d1);

    //尾删除
    d1.pop_back();
    //200 100 10
    printDeque(d1);

    //头删
    d1.pop_front();
    //100 10
    printDeque(d1);
}

void test02()
{
    deque<int> d1;
    d1.push_back(10);
    d1.push_back(20);
    d1.push_front(100);
    d1.push_front(200);

    //200 100 10 20
    printDeque(d1);

    //insert插入
    d1.insert(d1.begin(), 1000);
    //1000 200 100 10 20
    printDeque(d1);

    d1.insert(d1.begin(), 2, 10000);
    //10000 10000 1000 200 100 10 20
    printDeque(d1);

    //按照区间进行插入
    deque<int> d2;
    d2.push_back(1);
    d2.push_back(2);
    d2.push_back(3);

    d1.insert(d1.begin(), d2.begin(), d2.end());//在d1的begin位置插入d2.begin到d2.end区间中的数据
    printDeque(d1);
}

void test03()
{
    deque<int> d1;
    d1.push_back(10);
    d1.push_back(20);
    d1.push_front(100);
    d1.push_front(200);

    //删除
    d1.erase(++d1.begin());//删除第二个数据
    //200 10 20
    printDeque(d1);

    //按照区间的方式删除
    // d1.erase(d1.begin(), d1.end());
    //清空
    d1.clear();
    printDeque(d1);
}
int main()
{
    // test01();
    // test02();
    test03();
    return 0;
}
```

# Deque容器数据获取

```c++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    deque<int> d;
    d.push_back(10);
    d.push_back(20);
    d.push_back(30);
    d.push_front(100);
    d.push_front(200);
    d.push_front(300);

    //通过[]方式访问
    for (int i = 0; i < d.size(); i++)
        cout << d[i] << " ";
    cout << endl;

    //通过at方式访问元素
    for (int i = 0; i < d.size(); i++)
        cout << d.at(i) << " ";
    cout << endl;

    cout << "第一个元素:" << d.front() << endl;
    cout << "最后一个元素：" << d.back() << endl;
    return 0;
}
```

# Deque排序操作

```c++
#include <bits/stdc++.h>
using namespace std;

void printDeque(deque<int> d)
{
    for (int i = 0; i < d.size(); i++)
        cout << d[i] << " ";
    cout << endl;
}

bool cmp(int x, int y)//降序
{
    return x > y;
}
void test01()
{
    deque<int> d;
    d.push_back(10);
    d.push_back(20);
    d.push_back(30);
    d.push_front(100);
    d.push_front(200);
    d.push_front(300);

    //300 200 100 10 20 30
    printDeque(d);

    //排序
    sort(d.begin(), d.end());
    printDeque(d);
}

int main()
{
    test01();
    return 0;
}
```

