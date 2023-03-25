# Vector存放内置数据类型

容器 :**vector**

算法:for_earch

迭代器:verctor<int>::iterator

示例:

````c++
#include <bits/stdc++.h>
using namespace std;

void myPrint(int val)
{
    cout << val << endl;
}

int main()
{
    //创建vector容器，并且通过模板参数指定容器中存放的数据的类型
    vector<int> v;
    for (int i = 1; i <= 5; i++)
        v.push_back(i);//尾插入
    //通过迭代器访问容器中的是数据
    vector<int>::iterator itBegin = v.begin();//起始迭代器，指向容器中的第一个元素
    vector<int>::iterator itEnd = v.end();//结束迭代器  指向容器中的最后一个元素的下一个位置

    //第一种遍历方式
    while(itBegin != itEnd)
    {
        cout << *itBegin << endl;
        itBegin++;
    }
    //第二种遍历方式
    for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
        cout << *it << endl;
    //第三种遍历方式 需要算法algorithm头文件
    for_each(v.begin(), v.end(), myPrint);
}
````

# Vector存放自定义数据类型

````c++
#include <bits/stdc++.h>
using namespace std;
/*   
(*).value是先取引用在取值表示当前不是指针, value1->value2这表明value1为指针指向value2这个数据
(*value1)->value2表面value1为二级指针，先取引用编程(*value1)[一级指针]再通过->指向value2等价于
(**value1).value2就是先将二级指针value1变成一个数据在通过'.'来调用value2
*/
class Person
{
    public:
        Person(string name, int age)
        {
            this->m_Name = name;
            this->m_Age = age;
        }
    string m_Name;
    int m_Age;
};

//自定义数据类型
void test01()
{
    vector<Person> v;
    Person p1("aaa", 10);
    Person p2("bbb", 20);
    Person p3("ccc", 30);
    Person p4("ddd", 40);
    Person p5("eee", 50);

    //向容器中添加数据
    v.push_back(p1);
    v.push_back(p2);
    v.push_back(p3);
    v.push_back(p4);
    v.push_back(p5);

    //遍历容器中的数据
    for (vector<Person>::iterator it = v.begin(); it != v.end(); it++)
    {
       // cout << "姓名：" << (*it).m_Name << " 年龄：" << (*it).m_Age << endl;
        cout << "姓名：" << it->m_Name << " 年龄：" << it->m_Age << endl;
    }
}

//存放自定义数据类型 指针
void test02()
{
    vector<Person*> v;
    Person p1("aaa", 10);
    Person p2("bbb", 20);
    Person p3("ccc", 30);
    Person p4("ddd", 40);
    Person p5("eee", 50);

    //向容器中添加数据
    v.push_back(&p1);
    v.push_back(&p2);
    v.push_back(&p3);
    v.push_back(&p4);
    v.push_back(&p5);

    //遍历容器
    //*it就是vector<Person *>中<Persion *>里面的数据类型Person*
    for (vector<Person *>::iterator it = v.begin(); it != v.end(); it++)
    {
        // cout << "姓名：" << (*it)->m_Name << " 年龄：" << (*it)->m_Age << endl;
        cout << "::姓名：" << (**it).m_Name << " 年龄：" << (**it).m_Age << endl;
    }
}
int main()
{
    // test01();
    test02();
    return 0;
}
````

# Vector容器嵌套容器

```c++
#include <bits/stdc++.h>
using namespace std;

//容器嵌套
void test01()
{
    vector<vector<int>> v;

    //创建小容器
    vector<int> v1;
    vector<int> v2;
    vector<int> v3;
    vector<int> v4;

    //向小容器中添加数据
    for (int i = 0; i < 4; i++)
    {
        v1.push_back(i + 1);
        v2.push_back(i + 2);
        v3.push_back(i + 3);
        v4.push_back(i + 4);
    }

    //将小容器插入到大的容器中
    v.push_back(v1);
    v.push_back(v2);
    v.push_back(v3);
    v.push_back(v4);

    //通过大容器，把所有的数据遍历一边
    for (vector<vector<int>>::iterator it = v.begin(); it != v.end(); it++)
    {
        //(*i)----容器vector<int>
        for (vector<int>::iterator vit = (*it).begin(); vit != (*it).end(); vit++)
        {
            cout << *vit << " ";
        }
        cout << endl;
    }
}

int main()
{
    test01();
    return 0;
}
```

# Vector容器构造

```c++
#include <bits/stdc++.h>
using namespace std;

void printVector(vector<int>v)
{
    for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
        cout << *it << " ";
    cout << endl;
}

//vector容器构造
void test01()
{
    vector<int> v1;
    for (int i = 0; i < 10; i++)
        v1.push_back(i);
    printVector(v1);

    //通过区间方式进行构造
    vector<int> v2(v1.begin(), v1.end());//通过区间将v1的数据传递进去
    printVector(v2);

    //n个elem方式构造
    vector<int> v3(10, 100);//将v3初始化为10个100
    printVector(v3);
    
    //拷贝构造
    vector<int> v4(v3);//用v3将v4初始化
    printVector(v4);
}

int main()
{
    test01();
    return 0;
}
```

# Vectot赋值操作

```c++
#include <bits/stdc++.h>
using namespace std;

void printVector(vector<int>v)
{
    for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    vector<int> v1;
    for (int i = 0; i < 10; i++)
        v1.push_back(i);
    printVector(v1);

    //赋值
    vector<int> v2;
    v2 = v1;
    printVector(v2);

    //assign
    vector<int> v3;
    v3.assign(v1.begin(), v1.end());
    printVector(v3);

    //n个elem方式赋值
    vector<int> v4;
    v4.assign(10, 100);
    printVector(v4);
}


int main()
{
    test01();
    return 0;
}
```

# Vector容量和大小

```c++
/*
empty();                判断容器是否为空
capacity();             判断容器的容量
size();                 返回容器中元素的个数
resize(int num);        重新指定容器的长度，如果变长度，则以默认值填充
                        若变短，则末尾超过的删除
resize(int num,elem);   以elem填充
*/
#include <bits/stdc++.h>
using namespace std;

void printVector(vector<int>v)
{
    for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    vector<int> v1;
    for (int i = 0; i < 10; i++)
        v1.push_back(i);
    printVector(v1);

    if (v1.empty())//为真代表容器为空
        cout << "v1为空" << endl;
    else
    {
        cout << "v1不为空" << endl;
        cout << "v1的容量:" << v1.capacity() << endl;
        cout << "v1的大小:" << v1.size() << endl;
    }

    //重新指定大小
    v1.resize(15, 100);
    printVector(v1);

    v1.resize(5);//如果重新指定的比原来的短了，超出的部分被删除
    printVector(v1);
    cout << v1.capacity() << " " << v1.size() << endl;
}

int main()
{
    test01();
    return 0;
}
```

# Vector插入和删除

````c++
#include <bits/stdc++.h>
using namespace std;

void printVector(vector<int> v)
{
    for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    vector<int> v1;
    v1.push_back(10);
    v1.push_back(20);
    v1.push_back(30);
    v1.push_back(40);
    v1.push_back(50);

    printVector(v1);

    v1.pop_back();//尾删即50被删除
    printVector(v1);

    //插入
    v1.insert(v1.begin(), 100);//在v1.begin()位置处插入100
    printVector(v1);

    v1.insert(v1.begin(), 2, 1000);//在v1.begin()位置插入2个1000
    printVector(v1);

    //删除
    v1.erase(v1.begin());//删除v1.begin()处的数据
    printVector(v1);

    //清空
    // v1.erase(v1.begin(), v1.end());//删除一个区间的数
    v1.clear();//清空v1
    printVector(v1);
}

int main()
{
    test01();
    return 0;
}
````

# Vector数据获取

```c++
#include <bits/stdc++.h>
using namespace std;
void test01()
{
    vector<int> v1;
    for (int i = 0; i < 10; i++)
        v1.push_back(i);

    //利用[]来访问容器v1的元素
    for (int i = 0; i < v1.size(); i++)
        cout << v1[i] << " ";
    cout << endl;

    //利用at来访问容器v1的元素
    for (int i = 0; i < v1.size(); i++)
        cout << v1.at(i) << " ";
    cout << endl;

    //获取容器v1的第一个元素
    cout << v1.front() << endl;

    //获取容器v1的最后一个元素
    cout << v1.back() << endl;
}

int main()
{
    test01();
    return 0;
}
```

# Vector容器互换

```c++
#include <bits/stdc++.h>
using namespace std;

void printVector(vector<int> v)
{
    for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
        cout << *it << " ";
    cout << endl;
}

void test01()
{
    vector<int> v1;
    for (int i = 0; i < 10; i++)
        v1.push_back(i);
    printVector(v1);

    vector<int> v2;
    for (int i = 10; i > 0; i--)
        v2.push_back(i);
    printVector(v2);

    v1.swap(v2);//将v1容器的值和v2进行交换
    printVector(v1);
    printVector(v2);
}


//实际用途
//巧用swap可以收缩内存空间
void test02()
{
    vector<int> v;
    for (int i = 0; i < 100000; i++)
        v.push_back(i);
    cout << "容量:" << v.capacity() << endl;
    cout << "大小:" << v.size() << endl;

    v.resize(3);
    cout << "容量:" << v.capacity() << endl;
    cout << "大小:" << v.size() << endl;

    //用swap收缩内存
    vector<int> (v).swap(v);
    /*
    vector<int> (v) 匿名对象
    .swap(v) 容器交换
    */
    cout << "容量:" << v.capacity() << endl;
    cout << "大小:" << v.size() << endl;

}
int main()
{
    // test01();
    test02();
    return 0;
}
```

# Vector预留空间

```c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    vector<int> v;
    int cnt = 0; //统计开辟内存的次数
    int *p = NULL;

    //利用reserve预留100002空间
    v.reserve(100002);//直接开辟100002空间那么cnt就开辟了一次
    for (int i = 0; i < 100000; i++)
    {
        v.push_back(i);
        if (p != &v[0])
        {
            p = &v[0];
            cnt++;
        }
    }
    cout << cnt << endl;
}


int main()
{
    test01();
    return 0;
}
```

