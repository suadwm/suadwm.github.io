### CF1426E Rock, Paper, Scissors

> A and B are playing "Rock, Paper, Scissors". 
>
> Alice decided to show rock $a_1$ times, show scissors $a_2$ times and show paper $a_3$ times. Bob decided to show rock $b_1$ times, show scissors $b_2$ times and show paper $b_3$ times.  
>
> Your task is to find two numbers:
>
> 1. the minimum number of rounds Alice can win;
> 2. the maximum number of rounds Alice can win.
>
> A 和 B 在玩石头剪刀布。
>
> A 出了石头 $a_1$ 次，剪刀 $a_2$ 次，布 $a_3$ 次。B 出了石头 $b_1$ 次，剪刀 $a_2$ 次，布 $b_3$ 次。
>
> $1\leq n\leq10^9, 0\leq a_i\leq n, a_1+a_2+a_3=n,0\leq b_i\leq n,b_1+b_2+b_3=n$

#### sol1

Use network flow to build a graph. In the graph, we have start node $0$ and end node $7$, then we have nodes $1,2,3$ as the nodes for Alice's rock, scissors and paper, nodes $4,5,6$ as the nodes for Bob's rock, scissors and paper. 

Then we add edges $0\to 1$ with flow $a_1$, $0\to 2$ with flow $a_2$, $0\to 3$ with flow $a_3$, $4\to 7$ with flow $b_1$, $5\to 7$ with flow $b_2$, $6\to 7$ with flow $b_3$. 

For the situation of maximum wins, we need to add edges of winning conditions -- $1\to 5$ with flow $n$, $2\to 6$ with flow $n$, and $3\to 4$ with flow $n$.

Therefore, the flow of the graph is the maximum wins A can have.

Similarly, for the minimum, we just need to change the edges between A and B. Instead, we add edges $1\to 4$ with flow $n$, $1\to 6$ with flow $n$, $2\to 4$ with flow $n$, $2\to 5$ with flow $n$, $3\to 5$ with flow $n$, $3\to 6$ with flow $n$. 

The resulting flow is the minimum wins A can have.

网络流建图。考虑 $0$ 作为起始点，$7$ 作为终点。$1,2,3$ 作为 A 的石头剪刀布点，从$0$ 到这些点连边，流量为$a_1,a_2,a_3$，$4,5,6$ 作为 B 的，流量为$b_1,b_2,b_3$。然后对于胜利的情况连边，流量为$n$.

相似的，如果想要求最少，就对失败的情况连边，求最大流，然后用 $n-$最大流就可以啦。

```C++
#include<bits/stdc++.h>
#define pb push_back
using namespace std;
const int N=10;
const int inf=1e9+10;
struct edge{int to,cap,rev;};
vector<edge>g[N];int itr[N];
void init(){
	for(int i=0;i<N;i++)g[i].clear();
	memset(itr,0,sizeof itr);
} 
inline void ae(int u,int v,int w){
    g[u].pb((edge){v,w,(int)g[v].size()});
    g[v].pb((edge){u,0,(int)g[u].size()-1});
}
queue<int>q;int lv[N];
bool bfs(int s,int t){
    memset(lv,-1,sizeof(lv));
    lv[s]=0;q.push(s);
    while(!q.empty()){
        int x=q.front();q.pop();
        for(auto e:g[x]){
            int to=e.to,cap=e.cap;
            if(lv[to]==-1&&cap){
                lv[to]=lv[x]+1;
                q.push(to);
            }
        }
    }
    // for(int i=1;i<=n;i++)cerr<<lv[i]<<" ";cerr<<endl;
    return lv[t]!=-1;
}
int dfs(int x,int t,int flow){
    // cerr<<x<<" "<<t<<" "<<flow<<endl;
    if(x==t)return flow;
    for(int&i=itr[x];i<(int)g[x].size();i++){
        edge&e=g[x][i];
        if(lv[e.to]>lv[x]&&e.cap){
            int d=dfs(e.to,t,min(e.cap,flow));
            if(d){
                e.cap-=d;
                g[e.to][e.rev].cap+=d;
                return d;
            }
        }
    }
    return 0;
}
int get_flow(int s,int t){
    int res=0;
    while(bfs(s,t)){
        memset(itr,0,sizeof(itr));
        int d;
        while(d=dfs(s,t,inf))res+=d;
        res+=d;
    }
    return res;
}
int n,a1,a2,a3,b1,b2,b3;
int main(){
	ios::sync_with_stdio(0);
	cin>>n>>a1>>a2>>a3>>b1>>b2>>b3;
	ae(0,1,a1);ae(0,2,a2);ae(0,3,a3);
	ae(4,7,b1);ae(5,7,b2);ae(6,7,b3);
	ae(1,5,inf);ae(2,6,inf);ae(3,4,inf);
	int ma=get_flow(0,7);
	init();
	ae(0,1,a1);ae(0,2,a2);ae(0,3,a3);
	ae(4,7,b1);ae(5,7,b2);ae(6,7,b3);
	ae(1,4,inf);ae(1,6,inf);
	ae(2,4,inf);ae(2,5,inf);
	ae(3,5,inf);ae(3,6,inf);
	int mi=n-get_flow(0,7);
	cout<<mi<<" "<<ma<<endl;
	return 0;
}

```



#### sol2 

Use greedy. If you understand the construction of network flow, you would quickly know how to maximum wins -- $\min(a_1,b_2)+\min(a_2,b_3)+\min(a_3,b_1)$. 

The minimum wins is a more difficult condition. Because for every condition(rock, scissors, paper), we have two situations not to win -- a draw or a loss. Together, it forms $6$ choices. Then we can use a permutation with $O(6!)$ to consider the priority of each choices. Then we can find out the maximum loss and draw for A -- which is also the minimum wins for A.

贪心，启动! 如果知道网络流建图，我们就知道了，最大化赢的情况，一共就三种情况，可以直接用 $\min(a_1,b_2)+\min(a_2,b_3)+\min(a_3,b_1)$ 。

最少的情况就要复杂一些了，因为对于石头剪刀布每种来说，不赢的情况有两种，平局和输。所以我们很难去考虑对于每种情况分配多少个。但是我们可以枚举 $6!$ 种情况，考虑优先级。然后选出所有情况里，可以造成不赢的情况最多的那个。

```C++
#include<bits/stdc++.h>
#define pii pair<int,int>
#define pb push_back
#define mp make_pair
#define fi first
#define se second
using namespace std;
int n;
int a1,a2,a3,b1,b2,b3;
vector<pii>ord;
int main(){
	ios::sync_with_stdio(0);
	cin>>n>>a1>>a2>>a3>>b1>>b2>>b3;
	ord.pb(mp(0,0));ord.pb(mp(0,2));
	ord.pb(mp(1,0));ord.pb(mp(1,1));
	ord.pb(mp(2,1));ord.pb(mp(2,2));
	sort(ord.begin(),ord.end());
	int ans=0;
	do{
		vector<int>va,vb;
		va.pb(a1);va.pb(a2);va.pb(a3);
		vb.pb(b1);vb.pb(b2);vb.pb(b3);
		int res=0;
		for(auto p:ord){
			int i=p.fi,j=p.se,tmp=min(va[i],vb[j]);
			res+=tmp;
			va[i]-=tmp;
			vb[j]-=tmp;
		}
		ans=max(ans,res);
	}while(next_permutation(ord.begin(),ord.end()));
	cout<<n-ans<<" "<<min(a1,b2)+min(a2,b3)+min(a3,b1)<<endl;
	return 0;
}

```



