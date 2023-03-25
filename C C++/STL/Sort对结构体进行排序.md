# Sort对结构体进行排序

````c++
#include <bits/stdc++.h>
using namespace std;
struct stu
{
    int chinese, math;
    int sum;
    char name[20];
};

bool cmp1(stu x, stu y)//以math的值进行降序
{
    return x.math > y.math;
}
bool cmp2(stu x, stu y)//以math的值进行升序
{
    return x.math < y.math;
}
bool cmp3(stu x, stu y)//以chines的值进行降序
{
    return x.chinese > y.chinese;
}
bool cmp4(stu x, stu y)//以chines的值进行升序
{
    return x.chinese < y.chinese;
}
bool cmp5(stu x, stu y)//以sum的值进行升序
{
    return x.sum < y.sum;
}
bool cmp6(stu x, stu y)//以sum的值进行降序
{
    return x.sum > y.sum;
}

int main()
{
    stu student[100];
    int n;
    cin >> n;
    
    for (int i = 0; i < n; i++)
    {
        cin >> student[i].name >> student[i].chinese >> student[i].math;
        student[i].sum = student[i].chinese + student[i].math;
    }

    sort(student, student + n, cmp6);
    cout << endl;
    for (int i = 0; i < n; i++)
    {
        cout << student[i].name << " " <<  student[i].chinese << " " <<  student[i].math << endl;
    }
    return 0;
}
/*
3
a 90 20
b 30 70
c 90 90
*/
````

