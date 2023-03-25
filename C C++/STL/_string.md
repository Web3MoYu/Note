# String构造函数

```c++
#include <bits/stdc++.h>
using namespace std;
void test01()
{
    string s1;//默认构造
    
    const char *str = "hello world";
    string s2(str);//创建s2字符串，并用str给其初始化
    cout << s2 << endl;
    string s3(10, 'a');//创建s3字符串，并给其初始化为10个a
    cout << s3 << endl;
}

int main()
{
    test01();
    return 0;
}
```

# String赋值操作

```
#include <bits/stdc++.h>
using namespace std;
void test01()
{
    string str1, str2;
    str1 = "hello world";
    str2 = str1;
    cout << str2 << endl;

    string str3;
    str3.assign("hello c++");//将hello c++赋值给str3
    cout << str3 << endl;

    string str4;
    str4.assign("hello c++", 5);//将hello c++前五个字符即hello赋值给str4
    cout << str4 << endl;

    string str5;
    str5.assign(str4);//将str4赋值给str5
    cout << str5 << endl;

    string str6;
    str6.assign(10, 'w');//赋值给str6 10个w
    cout << str6 << endl;
}

int main()
{
    test01();
    return 0;
}
```

# String字符串拼接

```c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    string str1 = "I";
    str1 += " play";//str1 = I play
    cout << str1 << endl;

    str1 += ':';//str1 = I play:
    cout << str1 << endl;

    string str2 = "LOL DNF";
    str1 += str2;//str1 = I play:LOL DNF
    cout << str1 << endl;

    string str3 = "I";
    str3.append(" love ");//str3 = I love
    cout << str3 << endl;

    str3.append("game abcde", 4);//str3 = I love game
    cout << str3 << endl;

    // str3.append(str2);
    // cout << str3 << endl;//str3 = I love gameLOL DNF

    // str3.append(str2, 0, 3);//str3 = I love gameLOL 截取从0开始3个字符
    // cout << str3 << endl;

    str3.append(str2, 4, 3);//str3 = I love gameDNF从第4个位置截取3个
    cout << str3 << endl;
}

int main()
{
    test01();
    return 0;
}
```

# String查找和替换

```c++
#include <bits/stdc++.h>
using namespace std;

//查找
void test01()
{
    int pos;
    string str1 = "abcdefgde";
    pos = str1.find("de");//从头开始查找de返回第一个字母即d出现的位置,找不到返回-1
    if(pos == -1)
        cout << "Not found" << endl;
    else
        cout << pos << endl;
    pos = str1.rfind("de");//从后往前查找
    cout << pos << endl;
}

//替换
void test02()
{
    string str1 = "abcdefg";

    str1.replace(1, 3, "1111");//从1这个位置及b开始三个字符换成1111
    cout << str1 << endl;//a1111efg
}
int main()
{
    // test01();
    test02();
    return 0;
}
```



# String比较

```c++
#include <bits/stdc++.h>
using namespace std;
void test01()
{
    string str1 = "hello";
    string str2 = "xello";
    if (str1.compare(str2) == 0)
        cout << "str1 == str2" << endl;
    else if (str1.compare(str2) > 0)
        cout << "str1 > str2" << endl;
    else
        cout << "str1 < str2" << endl;

}

int main()
{
    test01();
    return 0;

}
```

# String单个字符存取

```c++
#include <bits/stdc++.h>
using namespace std;
void test01()
{
    string str = "hello";
    // cout << str << endl;
    //通过[]访问单个字符
    for (int i = 0; i < str.size(); i++)
    {
        cout << str[i] << " ";
    }
    cout << endl;
    //通过at方式访问单个字符
    for (int i = 0; i < str.size(); i++)
    {
        cout << str.at(i) << " ";
    }
    cout << endl;

    //修改
    str[0] = 'x';
    str.at(1) = 't';
    cout << str << endl;
}

int main()
{
    test01();
    return 0;
}
```

# String插入和删除

```c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    string str = "hello";
    //插入
    str.insert(1, "111"/*str*/);

    //删除
    str.erase(1, 3);//从1开始删除3个字符
    cout << str << endl;

}

int main()
{
    test01();
    return 0;
}
```

# String获取子串

````c++
#include <bits/stdc++.h>
using namespace std;

void test01()
{
    string str = "abcdef";
    string subStr = str.substr(1, 3);//获取从1开始的三个字符即bcd
    cout << subStr << endl;
}

void test02()
{
    string email = "lsh3040114965@gmail.com";
    string username, mail;
    username = email.substr(0, email.find('@'));
    mail = email.substr(email.find('@') + 1, email.length() - email.find('@'));
    cout << "username = " << username << endl << "mail = " << mail << endl;
}


int main()
{
    // test01();
    test02();
    return 0;
}
````































