这道题是一道很好的dp。\
假设留下的序列是 $b$，首先有个4个性质：

- 最后剩下的是原序列 $a$ 的子序列。
- 对于 $b_1$，他在原序列中假设位置为 $x$，那么从 $a_1$ $a_x$ 的最小值必须是 $b_1$。
- 对于 $b_n$，他在原序列中假设位置是 $y$，那么从 $a_y$到 $a_n$ 的最小值必须是 $b_n$。
- 对于 $b_i$ 到 $b_{i+1}$，他们所对应原序列的位置之间的值的最小值必须是 $b_i$ 或 $b_{i+1}$。


所以，对于以上情况，我们可以 `dp`，设 $f_i$ 表示以 $i$ 为结尾的方案数有多少个，我们找到他左边第一个比他小的 $pos_i$ 位置，至于合法情况有两种。

1. 如果 $j\in[pos_i+1,i-1]$，那么这一个区间任意一个位置均可转移，用前缀和优化。
2. 如果 $j\in[1,pos_i]$，那么所有 $a_j>a_i$ 位置的 $j$ 都不合法，也就是说 $\min_{k=j}^ia_k=a_j$。由此可看，另一部分的贡献为 $f_{pos_i}+f_{pos_{pos_i}},...$，用 $g$ 数组去存，每一次更新即可。（这一块处理很妙，我没想到）

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=3e5+5,MOD=998244353;
int n,s[N],top,a[N],f[N],ans,mn=1e9;
int l[N],r[N],pre[N],g[N];
void file()
{
	#ifdef ONLINE_JUDGE
	freopen("count.in","r",stdin);
	freopen("count.out","w",stdout);
	#endif
}
int cal(int x,int y)
{
	return ((x+y)%MOD+MOD)%MOD;
}
int main()
{
	file();
	cin>>n;
	for(int i=1;i<=n;i++) cin>>a[i];
	s[top=0]=0;
	for(int i=1;i<=n;i++)
	{
		while(top&&a[s[top]]>a[i]) top--;
		l[i]=s[top];
		s[++top]=i;
	}
	s[top=0]=n+1;
	for(int i=n;i;i--)
	{
		while(top&&a[s[top]]>a[i]) top--;
		r[i]=s[top];
		s[++top]=i;
	}
	f[0]=pre[0]=1;
	for(int i=1;i<=n;i++)
	{
		if(!l[i]) f[i]=pre[i-1];
		else
		{
			f[i]=cal(pre[i-1],-pre[l[i]]);
			f[i]=cal(f[i],g[l[i]]);
		}
		pre[i]=cal(pre[i-1],f[i]);
		g[i]=cal(g[l[i]],f[i]);
	}
	for(int i=1;i<=n;i++) if(r[i]==n+1) ans=cal(ans,f[i]);
	cout<<ans<<"\n";
	return 0;
}
```
