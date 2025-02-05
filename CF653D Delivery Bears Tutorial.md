<h4 class="code-line" data-line-start=0 data-line-end=1 ><a id="CF653D_Delivery_Bears_Tutorial_0"></a>CF653D Delivery Bears Tutorial</h4>
<blockquote>
<p class="has-line-data" data-line-start="2" data-line-end="3">There are $n$ cities, $m$ roads, and $k$ bears on a map. Each edge is from $a_i$ to $b_i$ with weight $w_i$.</p>
<p class="has-line-data" data-line-start="4" data-line-end="5">Every bear must carry the same number of weight, marked as $d$. Each bear must choose a path from $1$ to $n$.</p>
<p class="has-line-data" data-line-start="6" data-line-end="7">Let $p_i$ be the number of bears who pass through the $i$ - the edge.</p>
<p class="has-line-data" data-line-start="8" data-line-end="9">We need to find a maximum $d$ for that $\forall i\in[1,m],\ p_i\times d\leq w_i$.</p>
<p class="has-line-data" data-line-start="10" data-line-end="11">Print $\max(xd)$.</p>
<p class="has-line-data" data-line-start="12" data-line-end="13">$\frac{|a-b|}{\max(1,b)}\leq 10^{-6}$</p>
<p class="has-line-data" data-line-start="14" data-line-end="15">Niwel 有 $k$ 只熊，一个 $n$ 个点 $m$ 条边的图，每条边有一个最大运输重量 $w_i$ 。每只熊都负责运输同样重量的货物，重量记为 $d$。</p>
<p class="has-line-data" data-line-start="16" data-line-end="17">你要为每只熊选择一条从节点 $1$ 到节点 $n$ 的路径。记 $p_i$ 为有多少只熊经过第 $i$ 条边。</p>
<p class="has-line-data" data-line-start="18" data-line-end="19">使得以下条件成立：$\forall i\in[1,m],\ p_i\times d\leq w_i$.</p>
<p class="has-line-data" data-line-start="20" data-line-end="21">$\frac{|a-b|}{\max(1,b)}\leq 10^{-6}$</p>
<p class="has-line-data" data-line-start="22" data-line-end="23">$2\leq n\leq 50,\ 1\leq m\leq 500,\ 1\leq k \leq 10^5,\ 1\leq w_i\leq 10^5$</p>
</blockquote>
<p class="has-line-data" data-line-start="24" data-line-end="25">​</p>
<p class="has-line-data" data-line-start="26" data-line-end="27">​   We find that $d$ has a maximum value, but for every $d’\leq d,$ $d’x$ is a valid solution.  We can use binary research to find out $d’$.</p>
<p class="has-line-data" data-line-start="28" data-line-end="29">​   For a specific, $d’$,  we can find out how many bears can walk on every edge by calculating $\lfloor\frac{w_i}{d’}\rfloor$. Then we can build a network flow graph to solve it in order to check whether the result equals to $k$.</p>
<p class="has-line-data" data-line-start="30" data-line-end="31">​   发现 $d$ 具有单调性，所以可以用二分。对于每个 $d’$ ，我们网络流建图，边的权值为  $\lfloor\frac{w_i}{d’}\rfloor$. 因此我们对于每个图跑一下，检查结果是否为 $k$.</p>
<pre><code class="has-line-data" data-line-start="33" data-line-end="126" class="language-C++"><span class="hljs-keyword">using</span> <span class="hljs-keyword">namespace</span> <span class="hljs-built_in">std</span>;
<span class="hljs-preprocessor">#<span class="hljs-keyword">define</span> ll long long</span>
<span class="hljs-preprocessor">#<span class="hljs-keyword">define</span> pb push_back</span>
<span class="hljs-keyword">const</span> <span class="hljs-keyword">int</span> N=<span class="hljs-number">52</span>;
<span class="hljs-keyword">const</span> <span class="hljs-keyword">int</span> inf=<span class="hljs-number">1e9</span>;
<span class="hljs-keyword">const</span> <span class="hljs-keyword">double</span> eps=<span class="hljs-number">1e-9</span>;
<span class="hljs-keyword">int</span> n,m,k;
<span class="hljs-keyword">struct</span> edge{<span class="hljs-keyword">int</span> to,cap,rev;};
<span class="hljs-built_in">vector</span>&lt;edge&gt;g[N];<span class="hljs-keyword">int</span> itr[N];
<span class="hljs-function"><span class="hljs-keyword">void</span> <span class="hljs-title">init</span><span class="hljs-params">()</span></span>{
    <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i=<span class="hljs-number">0</span>;i&lt;N;i++)g[i].clear();
    <span class="hljs-built_in">memset</span>(itr,<span class="hljs-number">0</span>,<span class="hljs-keyword">sizeof</span> itr);
} 
<span class="hljs-function"><span class="hljs-keyword">inline</span> <span class="hljs-keyword">void</span> <span class="hljs-title">ae</span><span class="hljs-params">(<span class="hljs-keyword">int</span> u,<span class="hljs-keyword">int</span> v,<span class="hljs-keyword">int</span> w)</span></span>{
    g[u].pb((edge){v,w,(<span class="hljs-keyword">int</span>)g[v].size()});
    g[v].pb((edge){u,<span class="hljs-number">0</span>,(<span class="hljs-keyword">int</span>)g[u].size()-<span class="hljs-number">1</span>});
}
<span class="hljs-built_in">queue</span>&lt;<span class="hljs-keyword">int</span>&gt;q;<span class="hljs-keyword">int</span> lv[N];
<span class="hljs-function"><span class="hljs-keyword">bool</span> <span class="hljs-title">bfs</span><span class="hljs-params">(<span class="hljs-keyword">int</span> s,<span class="hljs-keyword">int</span> t)</span></span>{
    <span class="hljs-built_in">memset</span>(lv,-<span class="hljs-number">1</span>,<span class="hljs-keyword">sizeof</span>(lv));
    lv[s]=<span class="hljs-number">0</span>;q.push(s);
    <span class="hljs-keyword">while</span>(!q.empty()){
        <span class="hljs-keyword">int</span> x=q.front();q.pop();
        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">auto</span> e:g[x]){
            <span class="hljs-keyword">int</span> to=e.to,cap=e.cap;
            <span class="hljs-keyword">if</span>(lv[to]==-<span class="hljs-number">1</span>&amp;&amp;cap){
                lv[to]=lv[x]+<span class="hljs-number">1</span>;
                q.push(to);
            }
        }
    }
    <span class="hljs-comment">// for(int i=1;i&lt;=n;i++)cerr&lt;&lt;lv[i]&lt;&lt;" ";cerr&lt;&lt;endl;</span>
    <span class="hljs-keyword">return</span> lv[t]!=-<span class="hljs-number">1</span>;
}
<span class="hljs-function"><span class="hljs-keyword">int</span> <span class="hljs-title">dfs</span><span class="hljs-params">(<span class="hljs-keyword">int</span> x,<span class="hljs-keyword">int</span> t,<span class="hljs-keyword">int</span> flow)</span></span>{
    <span class="hljs-comment">// cerr&lt;&lt;x&lt;&lt;" "&lt;&lt;t&lt;&lt;" "&lt;&lt;flow&lt;&lt;endl;</span>
    <span class="hljs-keyword">if</span>(x==t)<span class="hljs-keyword">return</span> flow;
    <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span>&amp;i=itr[x];i&lt;(<span class="hljs-keyword">int</span>)g[x].size();i++){
        edge&amp;e=g[x][i];
        <span class="hljs-keyword">if</span>(lv[e.to]&gt;lv[x]&amp;&amp;e.cap){
            <span class="hljs-keyword">int</span> d=dfs(e.to,t,min(e.cap,flow));
            <span class="hljs-keyword">if</span>(d){
                e.cap-=d;
                g[e.to][e.rev].cap+=d;
                <span class="hljs-keyword">return</span> d;
            }
        }
    }
    <span class="hljs-keyword">return</span> <span class="hljs-number">0</span>;
}
<span class="hljs-function"><span class="hljs-keyword">int</span> <span class="hljs-title">get_flow</span><span class="hljs-params">(<span class="hljs-keyword">int</span> s,<span class="hljs-keyword">int</span> t)</span></span>{
    <span class="hljs-keyword">int</span> res=<span class="hljs-number">0</span>;
    <span class="hljs-keyword">while</span>(bfs(s,t)){
        <span class="hljs-built_in">memset</span>(itr,<span class="hljs-number">0</span>,<span class="hljs-keyword">sizeof</span>(itr));
        <span class="hljs-keyword">int</span> d;
        <span class="hljs-keyword">while</span>(d=dfs(s,t,inf))res+=d;
        res+=d;
    }
    <span class="hljs-keyword">return</span> res;
}
<span class="hljs-keyword">class</span> Edge{<span class="hljs-keyword">public</span>:<span class="hljs-keyword">int</span> start,end,num;};
<span class="hljs-built_in">vector</span>&lt;Edge&gt;ve;
<span class="hljs-function"><span class="hljs-keyword">bool</span> <span class="hljs-title">chk</span><span class="hljs-params">(<span class="hljs-keyword">double</span> w)</span></span>{
<span class="hljs-comment">//  cerr&lt;&lt;fixed&lt;&lt;setprecision(10)&lt;&lt;"w="&lt;&lt;w&lt;&lt;endl;</span>
    init();
    <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i=<span class="hljs-number">0</span>;i&lt;m;i++){
        <span class="hljs-keyword">int</span> u=ve[i].start,v=ve[i].end;
        ll wei=(ll)((ve[i].num+<span class="hljs-number">1e-6</span>)/w);
<span class="hljs-comment">//      cerr&lt;&lt;u+1&lt;&lt;" "&lt;&lt;v+1&lt;&lt;" "&lt;&lt;wei&lt;&lt;endl;    </span>
        <span class="hljs-keyword">if</span>(wei&gt;k)wei=k;
        ae(u,v,wei);
    }
    <span class="hljs-keyword">return</span> get_flow(<span class="hljs-number">0</span>,n-<span class="hljs-number">1</span>)&gt;=k;
}
<span class="hljs-function"><span class="hljs-keyword">int</span> <span class="hljs-title">main</span><span class="hljs-params">()</span></span>{
    ios::sync_with_stdio(<span class="hljs-number">0</span>);
    <span class="hljs-built_in">cin</span>&gt;&gt;n&gt;&gt;m&gt;&gt;k;
    <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i=<span class="hljs-number">0</span>;i&lt;m;i++){
        <span class="hljs-keyword">int</span> u,v,w;
        <span class="hljs-built_in">cin</span>&gt;&gt;u&gt;&gt;v&gt;&gt;w;
        u--;v--;
        ve.pb((Edge){u,v,w});
    }
    <span class="hljs-keyword">double</span> low=<span class="hljs-number">0</span>,high=<span class="hljs-number">1000000</span>;
    <span class="hljs-keyword">while</span>(<span class="hljs-built_in">abs</span>(low-high)&gt;eps){
        <span class="hljs-keyword">double</span> mid=(low+high)/<span class="hljs-number">2.0</span>;
        <span class="hljs-keyword">if</span>(chk(mid))low=mid;
        <span class="hljs-keyword">else</span> high=mid;
    }
    <span class="hljs-built_in">cout</span>&lt;&lt;setprecision(<span class="hljs-number">10</span>)&lt;&lt;low*k&lt;&lt;endl;
    <span class="hljs-keyword">return</span> <span class="hljs-number">0</span>;
}
</code></pre>
