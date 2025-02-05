<body>
    <h2>CF653D Delivery Bears Tutorial</h2>
    <p>There are <code>n</code> cities, <code>m</code> roads, and <code>k</code> bears on a map. Each edge is from <code>a_i</code> to <code>b_i</code> with weight <code>w_i</code>.</p>
    <p>Every bear must carry the same number of weight, marked as <code>d</code>. Each bear must choose a path from 1 to <code>n</code>.</p>
    <p>Let <code>p_i</code> be the number of bears who pass through the <code>i</code>-th edge.</p>
    <p>We need to find a maximum <code>d</code> for that <code>∀ i∈[1,m], p_i × d ≤ w_i</code>.</p>
    <p>Print <code>max(xd)</code>.</p>
    <h3>Solution</h3>
    <p>We find that <code>d</code> has a maximum value, but for every <code>d' ≤ d</code>, <code>d' × x</code> is a valid solution. We can use binary search to find out <code>d'</code>.</p>
    <p>For a specific <code>d'</code>, we can find out how many bears can walk on every edge by calculating <code>⌊w_i / d'⌋</code>. Then we can build a network flow graph to solve it in order to check whether the result equals to <code>k</code>.</p>
    <pre><code>
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
    return lv[t]!=-1;
}
int dfs(int x,int t,int flow){
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
    init();
    for(int i=0;i<m;i++){
        int u=ve[i].start,v=ve[i].end;
        ll wei=(ll)((ve[i].num+1e-6)/w);
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
    </code></pre>
</body>
