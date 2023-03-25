```java
#include <stdio.h>
#include <stdbool.h>
#define MAX_N 10010

int prime[MAX_N];
bool isPrime[MAX_N + 1];
int PrimeNum[MAX_N];
int main()
{
    int p = 0;
    int n;
    n = MAX_N;
    for(int i = 0; i <= n; i++)
        isPrime[i] = true;
    isPrime[0] = isPrime[1] = false;//0 1不是素数
    for(int i = 2; i <= n; i++)//从2开始
    {
        if(isPrime[i])//如果是素数就进入循环
        {
            prime[p++] = i;//i是素数
            PrimeNum[i] = p;//记录是第几个素数
            for(int j = 2 * i; j <= n; j += i)//2的倍数全都不是素数
            {
                isPrime[j] = false;
            }
        }
    }
    printf("s");
    return 0;
}
```

