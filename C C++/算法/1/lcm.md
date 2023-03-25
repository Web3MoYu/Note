```java
#include <stdio.h>
int gcd(int n, int m)
{
	if (m == 0)
		return n;
	else
		return gcd(m, n % m);
}

int lcm(int a, int b)
{
    return a / gcd(a, b) * b;
}

int main()
{
	int n, m;
	while(scanf("%d%d", &n, &m) != EOF)
	{
		printf("%d\n", lcm(n, m));
	}
}

```

