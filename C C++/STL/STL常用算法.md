# STL常用算法

## 常用遍历算法

### for_earch

- 遍历容器
- for_earch(beg, end,func);

```c++
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;

//普通函数
void print01(int val)
{
    cout << val << " ";
}

//仿函数
class print02
{
public:
    void operator()(int val)
    {
        cout << val << " ";
    }
};

int main()
{
    vector<int> v;
    for (int i = 0; i < 10; i++)
        v.push_back(i);

    for_each(v.begin(), v.end(), print01);
    cout << endl;
    for_each(v.begin(), v.end(), print02());
    cout << endl;

    return 0;
}
```

### transform

- 搬运容器到另一个容器中
- transform(iterator beg1, iterator end1, iterator beg2, _func);

```c++
#include <bits/stdc++.h>
using namespace std;

class Transform
{
public:
    int operator()(int v1)
    {
        return v1;
    }
};

class MyPrint
{
public:
    void operator()(int val)
    {
        cout << val + 100 << " ";
    }
};

int main()
{
    vector<int> v;
    for (int i = 0; i < 10; i++)
        v.push_back(i);

    vector<int> vTarget;//目标容器

    vTarget.resize(v.size());//目标容器  需要提前开辟空间

    transform(v.begin(), v.end(), vTarget.begin(), Transform());
    for_each(vTarget.begin(), vTarget.end(), MyPrint());
    return 0;
}
```

## 常用查找算法

find							查找元素

find_if						按条件查找

adjacent_find			查找相邻重复元素

binary_search			二分查找法

count						统计元素个数

count_if					按条件统计元素个数

### find

- 查找指定元素，找到返回指定元素的迭代器，否则返回end()

```c++
#include <bits/stdc++.h>
using namespace std;

class Person{
public:
    Person(string name, int age)
    {
        this->m_Age = age;
        this->m_Name = name;
    }

    //重载== 底层find知道如何对person数据类型
    bool operator==(const Person &p)
    {
        if(this->m_Name == p.m_Name && this->m_Age == p.m_Age)
            return true;
        else
            return false;
    }
    string m_Name;
    int m_Age;
};

int main()
{
    vector<int> v;
    for (int i = 0; i < 10; i++)
        v.push_back(i);
    
    //查找容器中是否有5这个数字
    vector<int>::iterator it = find(v.begin(), v.end(), 50);
    if (it == v.end())
        cout << "Not found" << endl;
    else
        cout << *it << endl;

    vector<Person> p;
    Person p1("aaa", 10);
    Person p2("bbb", 20);
    Person p3("ccc", 30);
    Person p4("ddd", 40);

    p.push_back(p1);
    p.push_back(p2);
    p.push_back(p3);
    p.push_back(p4);

    vector<Person>::iterator pos = find(p.begin(), p.end(), p2);
    if (pos == p.end())
        cout << "Not found" << endl;
    else
        cout << pos->m_Name << " " << pos->m_Age << endl;
    return 0;
}
```

### find_if

- 按条件查找

find_if(beg, end, pred);

按值查找元素，找到返回指定位置的迭代器，找不到返回结束迭代器位置

pred 函数或者谓词（返回bool类型的仿函数）

```c++
#include <bits/stdc++.h>
using namespace std;

class GreaterFive{
public:
    bool operator ()(int val)
    {
        return val > 5;
    }
};

//查找算法find_if
//1查找内置数据类型
void test01()
{
    vector<int> v;
    for (int i = 0; i < 10; i++)
        v.push_back(i);
    vector<int>::iterator it = find_if(v.begin(), v.end(), GreaterFive());
    if (it == v.end())
        cout << "Not found" << endl;
    else
        cout << *it << endl;
}
//2查找自定义数据类型
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

//找大于20的人
class GreaterTwenty{
public:
    bool operator()(Person &p)
    {
        return p.m_Age > 20;
    }
};
void test02()
{
    vector<Person> v;
    Person p1("aaa", 10);
    Person p2("bbb", 20);
    Person p3("ccc", 30);
    Person p4("ddd", 40);

    v.push_back(p1);
    v.push_back(p2);
    v.push_back(p3);
    v.push_back(p4);

    vector<Person>::iterator it = find_if(v.begin(), v.end(), GreaterTwenty());
    if (it == v.end())
        cout << "Not found" << endl;
    else
        cout << it->m_Name << " " << it->m_Age << endl;
}

int main()
{
    // test01();
    test02();
    return 0;
}
```

### adjacent_find

- 查找相邻重复元素

adjacent_find(beg,end);

找到相邻重复的元素则返回第一个元素的位置，否则返回end();

```c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    vector<int> v;
    v.push_back(1);
    v.push_back(2);
    v.push_back(2);
    v.push_back(3);
    v.push_back(1);
    vector<int>::iterator it = adjacent_find(v.begin(), v.end());
    if (it == v.end())
        cout << "Not found" << endl;
    else
        cout << *it << endl;
}

int main()
{
    test01();
    return 0;
}
```

### binary_search

- 查找元素是否存在，存在返回true否则返回false

- ```c++
  bool binary_search(beg,end,value);
  ```

- 注意因为底层逻辑是二分查找所以数据要有序

```c++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    vector<int> v;
    for (int i = 0; i < 10; i++)
        v.push_back(i);
    if (binary_search(v.begin(), v.end(), 6))
        cout << "found" << endl;
    else
        cout << "Not found" << endl;
    return 0;
}
```

### count

- 统计该元素的个数

- ```c++
  count(beg,end,value);
  ```

````c++
#include <bits/stdc++.h>
using namespace std;

//内置
void test01()
{
    vector<int> v;
    v.push_back(10);
    v.push_back(40);
    v.push_back(30);
    v.push_back(20);
    v.push_back(40);
    v.push_back(40);

    int num = count(v.begin(), v.end(), 40);
    cout << num << endl;

}

//自定义
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
    bool operator==(const Person &x)//要const否则报错
    {
        return this->m_Age == x.m_Age;
    }

};
void test02()
{
    vector<Person> p;
    Person p1("刘备", 35);
    Person p2("关羽", 35);
    Person p3("张飞", 35);
    Person p4("赵云", 30);
    Person p5("曹操", 40);

    Person pos("诸葛亮", 35);
    p.push_back(p1);
    p.push_back(p2);
    p.push_back(p3);
    p.push_back(p4);
    p.push_back(p5);

    int num = count(p.begin(), p.end(), pos);
    cout << num << endl;
}
int main()
{
    // test01();
    test02();
    return 0;
}
````

### count_if

- ```c++
  cout_if(beg, end, _pred);
  ```

```c++
#include <bits/stdc++.h>
using namespace std;


//统计内置数据类型
class Greater20
{
public: 
    bool operator()(int val)
    {
        return val > 20;
    }
};

void test01()
{
    vector<int> v;
    v.push_back(10);
    v.push_back(40);
    v.push_back(30);
    v.push_back(20);
    v.push_back(40);
    v.push_back(20);

    int num = count_if(v.begin(), v.end(), Greater20());
    cout << num << endl;
}

//统计自定义数据类型
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

class AgeGreater20
{
public:
    bool operator()(const Person &p)
    {
        return p.m_Age > 20;
    }
};

void test02()
{
    vector<Person> v;
    Person p1("刘备", 35);
    Person p2("关羽", 35);
    Person p3("张飞", 35);
    Person p4("赵云", 40);
    Person p5("曹操", 20);

    v.push_back(p1);
    v.push_back(p2);
    v.push_back(p3);
    v.push_back(p4);
    v.push_back(p5);
    
    //统计大于20的人数
    int num = count_if(v.begin(), v.end(), AgeGreater20());
    cout << num << endl;
}

int main()
{
    // test01();
    test02();
    return 0;
}
```

## 常用排序算法

- sort								对容器内元素进行排序
- random_shuffle          洗牌，指定范围内的元素随机调整次序
- merge                           容器元素合并，并储存到另一个容器中
- reverse                          反转指定范围的元素

### sort

```c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    vector<int> v;
    v.push_back(10);
    v.push_back(30);
    v.push_back(50);
    v.push_back(20);
    v.push_back(40);

    sort(v.begin(), v.end());
    for (int i = 0; i < v.size(); i++)
        cout << v[i] << " ";
    cout << endl;
    //改为降序
    sort(v.begin(), v.end(), greater<int>());
    for (int i = 0; i < v.size(); i++)
        cout << v[i] << " ";
}

int main()
{
    test01();
    return 0;
}
```

### random_shuffle

- random_shuffle(beg,end);将[beg,end)这个区间的值进行打乱

```c++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    srand((unsigned int)time(NULL));
    vector<int> v;
    for (int i = 0; i < 10; i++)
        v.push_back(i);

    for (int i = 0; i < v.size(); i++)
        cout << v[i] << " ";
    cout << endl;

    //打乱
    random_shuffle(v.begin(), v.end());
    for (int i = 0; i < v.size(); i++)
        cout << v[i] << " ";
    cout << endl;
    return 0;
}
```

### merge

- 容器合并并储存到另一个容器中

- ```c++
  merge(beg1,end1,beg2,end2,dest);
  /*
  beg1 容器1开始迭代器
  end1 容器1结束迭代器
  beg2 容器2开始迭代器
  end2 容器2结束迭代器
  dest 目标容器开始迭代器
  */
  ```

- 两个容器必须是有序的

  ```c++
  #include <bits/stdc++.h>
  using namespace std;
  
  void test01()
  {
      vector<int> v1, v2;
  
      for (int i = 0; i < 10; i++)
      {
          v1.push_back(i);
          v2.push_back(i + 1);
      }
  
      //目标容器
      vector<int> v;
      v.resize(v1.size() + v2.size());
      merge(v1.begin(), v1.end(), v2.begin(), v2.end(), v.begin());
      for (int i = 0; i < v.size(); i++)
          cout << v[i] << " ";
      cout << endl;
  }
  
  int main()
  {
      test01();
      return 0;
  }reverse
  ```
  
  
  
  
  
### 将容器内元素进行反转

  ````c++
  #include <bits/stdc++.h>
  using namespace std;
  
  int main()
  {
      vector<int> v;
      v.push_back(10);
      v.push_back(30);
      v.push_back(50);
      v.push_back(40);
      v.push_back(20);
  
      for (int i = 0; i < v.size(); i++)
          cout << v[i] << " ";
      cout << endl;
  
      reverse(v.begin(), v.end());
      for (int i = 0; i < v.size(); i++)
          cout << v[i] << " ";
      cout << endl;
      return 0;
  }
  ````

  ## 常用拷贝和替换算法

- ````c++
  copy			//容器内指定范围的元素拷贝到另一个容器中
  replace			//将容器内指定范围的旧元素修改为新元素
  replace_if		//容器内指定范围满足条件的元素，替换为新元素
  swap			//互换两个容器的元素
  ````

  ```c++
  #include <bits/stdc++.h>
  using namespace std;
  
  void test01()
  {
      vector<int> v1, v2;
      for (int i = 0; i < 10; i++)
          v1.push_back(i);
      v2.resize(v1.size());
      copy(v1.begin(), v1.end(), v2.begin());
      for (int i = 0; i < v2.size(); i++)
          cout << v2[i] << " ";
      cout << endl;
  }
  
  int main()
  {
      test01();
      return 0;
  }
  ```

### replace

- 将容器内指定范围的就元素改为新元素

  ```c++
  replace(beg,end,oldvalue, newvalue)
  ```

```c++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    vector<int> v;
    v.push_back(20);
    v.push_back(30);
    v.push_back(50);
    v.push_back(30);
    v.push_back(40);
    v.push_back(20);
    v.push_back(10);
    v.push_back(20);

    for (int i = 0; i < v.size(); i++)
        printf("%5d", v[i]);
    replace(v.begin(), v.end(), 20, 2000);
    
    cout << endl;
    for (int i = 0; i < v.size(); i++)
        printf("%5d", v[i]);
    return 0;
}
```

### replace_if

- ````
  replace_if(beg,end,_pred,newvalue);
  ````

```c++
#include <bits/stdc++.h>
using namespace std;

class Greater30
{
public:
    bool operator()(int val)
    {
        return val >= 30;
    }
};

int main()
{
    vector<int> v;
    v.push_back(10);
    v.push_back(40);
    v.push_back(20);
    v.push_back(40);
    v.push_back(30);
    v.push_back(50);
    v.push_back(20);
    v.push_back(30);

    //将大于等于30的换成3000
    replace_if(v.begin(), v.end(), Greater30(), 3000);
    for (int i = 0; i < v.size(); i++)
        cout << v[i] << " ";
    cout << endl;
    return 0;
}
```

## 常用算术生成算法

- accumulate     计算容器元素累计总和
- fill                      向容器中添加元素

### accumulate

````c++
accumulate(beg,end,value);
//计算容器元素累计总和
//beg起始迭代器
//end结束迭代器
//value起始值
````

```c++
#include <bits/stdc++.h>
// #include <numeric>
using namespace std;

int main()
{
    vector<int> v;
    for (int i = 0; i <= 100; i++)
        v.push_back(i);
    int sum = accumulate(v.begin(), v.end(), 0);
    cout << sum;
    return 0;
}
```

### fill

fill(beg,end,value);

## 常用集合算法

```c++
set_intersection		//求两个容器的交集
set_union				//求两个容器的并集
set_difference			//求两个容器的差集
```

### set_intersection

````c++
set_intersection(beg1, end1, beg2, end2, dest);
/*
求两个集合的交集
两个容器必须是有序序列
beg1 容器1开始迭代器
end1 容器1结束迭代器
beg2 容器2开始迭代器
end2 容器2结束迭代器
dest 目标容器开始迭代器
*/
````

```c++
#include <bits/stdc++.h>
using namespace std;

void print(int val)
{
    cout << val << " ";
}

int main()
{
    vector<int> v1, v2, v;
    for (int i = 0; i < 10; i++)
    {
        v1.push_back(i);
        v2.push_back(i + 5);
    }
    //目标容器需要提前开辟空间
    v.resize(min(v1.size(), v2.size()));

    //获取交集
 	//itEnd返回最后结束的迭代器	
    vector<int>::iterator itEnd = set_intersection(v1.begin(), v1.end(), v2.begin(), v2.end(), v.begin());

    for_each(v.begin(), itEnd, print);
    return 0;
}
```

### set_union

```c++
set_union(beg1, end1, beg2, end2, dest);
/*求两个集合的并集
两个容器必须是有序序列
beg1 容器1开始迭代器
end1 容器1结束迭代器
beg2 容器2开始迭代器
end2 容器2结束迭代器
dest 目标容器开始迭代器
*/
```

```c++
#include <bits/stdc++.h>
using namespace std;

void print(int val)
{
    cout << val << " ";
}

int main()
{
    vector<int> v1, v2, v;
    for (int i = 0; i < 10; i++)
    {
        v1.push_back(i);
        v2.push_back(i + 5);
    }

    //提前开辟空间
    v.resize(v1.size() + v2.size());
    //获取并集
    //itEnd返回最后结束的迭代器
    vector<int>::iterator itEnd = set_union(v1.begin(), v1.end(), v2.begin(), v2.end(), v.begin());

    for_each(v.begin(), itEnd, print);
    return 0;
}
```

### set_difference

```c++
set_difference(beg1, end1, beg2, end2, dest);
/*求两个集合的差集(1在1和2的交集中没有的元素)
两个容器必须是有序序列
beg1 容器1开始迭代器
end1 容器1结束迭代器
beg2 容器2开始迭代器
end2 容器2结束迭代器
dest 目标容器开始迭代器
*/
```

```c++
#include <bits/stdc++.h>
using namespace std;

void print(int val)
{
    cout << val << " ";
}

int main()
{
    vector<int> v1, v2, v;
    for (int i = 0; i < 10; i++)
    {
        v1.push_back(i);            //0 1 2 3 4 5 6 7 8 9
        v2.push_back(i + 5);        //          5 6 7 8 9 10 11 12 13 14
    }

    //扩大容器v的大小
    v.resize(max(v1.size(), v2.size()));

    vector<int>::iterator itEnd;
    itEnd = set_difference(v1.begin(), v1.end(), v2.begin(), v2.end(), v.begin());//v1在v1和v2交集中没有的元素即0 1 2 3 4
    for_each(v.begin(), itEnd, print);
    cout << endl;

    itEnd = set_difference(v2.begin(), v2.end(), v1.begin(), v1.end(), v.begin());//v2在v1和v2交集中没有的元素即10 11 12 13 14
    for_each(v.begin(), itEnd, print);
    return 0;
}
```

