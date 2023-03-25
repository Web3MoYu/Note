```java
#include <stdio.h>
#include <string.h>

void BubbleSort(int a[], int n);
void QuickSort(int a[], int left, int right);

int main()
{
    int n;
    int num[100] = {0};
    scanf("%d", &n);
    for (int i = 0; i < n; i++)
    {
        scanf("%d", &num[i]);
    }
    // BubbleSort(num, n);
    QuickSort(num, 0, n - 1);
    for (int i = 0; i < n; i++)
    {
        printf("%d ", num[i]);
    }
    return 0;
}


void BubbleSort(int a[], int n)
{
    int tmp = 0;
    // for (int i = 0; i < n; i++)
    // { 
    //     for (int j = i + 1; j < n; j++)
    //     {
    //         if (*(a + i) > *(a + j))
    //         {
    //             tmp = *(a + i);
    //             *(a + i) = *(a + j);
    //             *(a + j) = tmp;
    //         }
    //     }
    // }
    for (int i = 0; i < n; i++)
    { 
        for (int j = i + 1; j < n; j++)
        {
            if (a[i] > a[j])
            {
                tmp = a[i];
                a[i] = a[j];
                a[j] = tmp;
            }
        }
    }
}

void QuickSort(int a[], int left, int right)
{
    int i, j, t, temp;//temp为基准数
    if (left > right)
        return;
    temp = a[left];
    i = left;
    j = right;
    while (i != j)
    {
        while (a[j] >= temp && i < j)
            j--;
        while (a[i] <= temp && i < j)
            i++;
        if (i < j)
        {
            t = a[i];
            a[i] = a[j];
            a[j] = t;
        }
    }
    a[left] = a[i];
    a[i] = temp;
    QuickSort(a , left, i - 1);
    QuickSort(a , i + 1, right);
    return;
}
```

