## Gcd

```java
#include <stdio.h>
int gcd_1(int n, int m);
int gcd_2(int n, int m);
int main()
{
	int n, m;
	while(scanf("%d%d", &n, &m) != EOF)
	{
		printf("%d\n", gcd_2(n, m));
	}
}

int gcd_1(int n, int m)
{
	if (m == 0)
		return n;
	else
		return gcd_1(m, n % m);
}

int gcd_2(int n, int m)
{
	int r;
	while(m > 0)
	{
		r = n % m;
		n = m;
		m = r;
	}
	return n;
}
```

