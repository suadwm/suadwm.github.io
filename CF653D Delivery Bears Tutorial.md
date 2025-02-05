#### CF653D Delivery Bears Tutorial

> There are $n$ cities, $m$ roads, and $k$ bears on a map. Each edge is from $a_i$ to $b_i$ with weight $w_i$. 
>
> Every bear must carry the same number of weight, marked as $d$. Each bear must choose a path from $1$ to $n$. 
>
> Let $p_i$ be the number of bears who pass through the $i$ - the edge.
>
> We need to find a maximum $d$ for that $\forall i\in[1,m],\ p_i\times d\leq w_i$.
>
> Print $\max(xd)$.
>
> $\frac{|a-b|}{\max(1,b)}\leq 10^{-6}$
>
> Niwel 有 $k$ 只熊，一个 $n$ 个点 $m$ 条边的图，每条边有一个最大运输重量 $w_i$ 。每只熊都负责运输同样重量的货物，重量记为 $d$。
>
> 你要为每只熊选择一条从节点 $1$ 到节点 $n$ 的路径。记 $p_i$ 为有多少只熊经过第 $i$ 条边。
>
> 使得以下条件成立：$\forall i\in[1,m],\ p_i\times d\leq w_i$.
>
> $\frac{|a-b|}{\max(1,b)}\leq 10^{-6}$
>
> $2\leq n\leq 50,\ 1\leq m\leq 500,\ 1\leq k \leq 10^5,\ 1\leq w_i\leq 10^5$

​	

​	We find that $d$ has a maximum value, but for every $d'\leq d,$ $d'x$ is a valid solution.  We can use binary research to find out $d'$. 

​	For a specific, $d'$,  we can find out how many bears can walk on every edge by calculating $\lfloor\frac{w_i}{d'}\rfloor$. Then we can build a network flow graph to solve it in order to check whether the result equals to $k$.

​	发现 $d$ 具有单调性，所以可以用二分。对于每个 $d'$ ，我们网络流建图，边的权值为  $\lfloor\frac{w_i}{d'}\rfloor$. 因此我们对于每个图跑一下，检查结果是否为 $k$. 

````C++
using namespace std;
#define ll long long
#define pb push_back
const int N=52;
const int inf=1e9;
const double eps=1e-9;
int n,m,k;
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
class Edge{public:int start,end,num;};
vector<Edge>ve;
bool chk(double w){
//	cerr<<fixed<<setprecision(10)<<"w="<<w<<endl;
	init();
	for(int i=0;i<m;i++){
		int u=ve[i].start,v=ve[i].end;
		ll wei=(ll)((ve[i].num+1e-6)/w);
//		cerr<<u+1<<" "<<v+1<<" "<<wei<<endl;	
		if(wei>k)wei=k;
		ae(u,v,wei);
	}
	return get_flow(0,n-1)>=k;
}
int main(){
	ios::sync_with_stdio(0);
	cin>>n>>m>>k;
	for(int i=0;i<m;i++){
		int u,v,w;
		cin>>u>>v>>w;
		u--;v--;
		ve.pb((Edge){u,v,w});
	}
	double low=0,high=1000000;
	while(abs(low-high)>eps){
		double mid=(low+high)/2.0;
		if(chk(mid))low=mid;
		else high=mid;
	}
	cout<<setprecision(10)<<low*k<<endl;
	return 0;
}
````

