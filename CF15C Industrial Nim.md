#### CF15C Industrial Nim

> There are $n$ stone quarries. Each quarry has $m_i$ dumpers, each of them having $x_i$, $x_i+1$, $\cdots$, $x_i+m_i-1$. Two people are playing a game Nim on it. On each turn, a player can select any dumper and remove any non-zero amount of stones from it. The player who cannot take a stone loses.
>
> Your task is to find which player will win.
>
> 有 $n$ 个圈，每个圈里面有 $m_i$ 个石堆，分别有 $x_i,x_i+1, \cdots, x_i+m_i-1$ 个。两个人在玩 Nim。每轮一个玩家能选择一个石堆，从里面拿走任何数量的石头。不能拿走石头的玩家输掉这个游戏。
>
> 你的任务是找到哪个玩家可以赢。
>
> $1\leq n\leq 10^5$, $1\leq x_i,m_i\leq 10^{16}$

Given the game Nim, we need to find the exclusive OR of all the number of stones in each dumper.  

$\oplus_{i=1}^{n}\oplus_{j=0}^{m_i-1}x_i+j $

If the result is $0$, the second player will win.

Otherwise, the result is not $0$, the first player will win.

It takes too many computations to get the answer, so we need to optimize it. 

We use $f(x)$ to represent $\oplus_{i=0}^{x}i$.

Then the equation because $\oplus_{i=1}^{n} f(x_i+m_i-1)\oplus f(x_i-1)$. 

For now, all we need is to calculate the $f(x)$ in $O(1)$. 

After observation, we find that the exclusive OR of every $4$ number from $0$ is $0$.

Therefore, we only need to look at the last $x\%4$ numbers in the interval.

Time Complexity: $O(n)$

Space Complexity: $O(n)$

因为是 Nim，所以要求出来 $\oplus_{i=1}^{n}\oplus_{j=0}^{m_i-1}x_i+j $.

如果这个结果为 $0$， 后手能赢。

如果这个结果不为 $0$，先手能赢。

我们肯定要优化则个过程。$f(i)$ 表示从 $0$ 到 $i$ 的异或结果。就可以得到一个新的式子，$\oplus_{i=1}^{n} f(x_i+m_i-1)\oplus f(x_i-1)$. 这里需要 $O(1)$ 地计算 $f(x)$. 

通过观察，我们发现从 $0$ 开始地每 $4$ 个数的异或结果是 $0$. 

因此，只需要看在这个区间最后 $x\%4$ 个数。

时间复杂度: $O(n)$

空间复杂度: $O(n)$

```C++
#include<bits/stdc++.h>
using namespace std;
#define ll long long 
const int N=1e5+10;
int n;
ll a[N],b[N];
ll get_xor(ll val,int d){
	if(val<0||((d-1>=0)&&(1ll<<(d-1))>val))return 0;
	if((!d))return ((val+1)/2)&1;
//	cerr<<"ok\n";
	++val;
	ll tmp=val-val/(1ll<<(d+1))*(1ll<<(d+1));
//	cerr<<tmp<<endl;
	if(tmp<=(1ll<<d))return 0;
	return tmp&1;
}
int main(){
	scanf("%d",&n);
	for(int i=1;i<=n;i++)scanf("%lld%lld",&a[i],&b[i]);
//	cerr<<get_xor(2,1)<<endl;
	ll ans=0;
	for(int i=1;i<=n;i++){
		for(int d=0;d<55;d++){
//			cerr<<a[i]-1<<" "<<d<<" "<<get_xor(a[i]-1,d)<<"\n";
//			cerr<<a[i]+b[i]-1<<" "<<d<<" "<<get_xor(a[i]+b[i]-1,d)<<endl;
			ll res1=get_xor(a[i]-1,d),res2=get_xor(a[i]+b[i]-1,d);
			ll res=res1==res2?0:1;
			ans^=(1ll<<d)*res;
//			cerr<<res1<<" "<<res2<<" "<<res<<" "<<((1ll<<d)*res)<<endl;
		}
	}
	cerr<<ans<<endl;
	printf(ans?"tolik":"bolik");
	return 0;
}
```

```C++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int N=1e5+10;
int n;
ll a[N],b[N];
int main(){
	scanf("%d",&n);
	for(int i=1;i<=n;i++){
		scanf("%lld%lld",&a[i],&b[i]);
		a[i]--;b[i]+=a[i];
	}
	ll ans=0;
	for(int i=1;i<=n;i++){
		while((a[i]&3)!=3){
			ans=ans^a[i];
			a[i]--;
		}
		while((b[i]&3)!=3){
			ans=ans^b[i];
			b[i]--;
		}
	}
	cerr<<ans<<endl;
	printf(ans?"tolik":"bolik");
	return 0;
}
```

