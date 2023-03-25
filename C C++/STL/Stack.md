# Stack

## 概念

- 只有一个口，先进的后出，后进的先出
- 栈不允许有遍历行为
- 栈中进数据称为入栈push
- 栈中出数据成为出栈pop

## 接口

```c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    stack<int> s;

    //入栈
    s.push(10);
    s.push(20);
    s.push(30);
    s.push(40);
    cout << "栈的大小:" << s.size() << endl;

    //只要栈不为空，就查看栈顶，并且执行出栈
    while(!s.empty())
    {
        //查看栈顶的元素
        cout << "栈顶元素为：" << s.top() << endl;
        
        //出栈
        s.pop();
    }

    cout << "栈的大小:" << s.size() << endl;
}

int main()
{
    test01();
    return 0;
}
```

