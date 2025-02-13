### CF630R Game

> A game is played on a $n\times n$ board. In each turn, the player needs to paint an empty grid that has no common sides with previously painted grids. Corners are allowed. 
>
> The player who cannot paint a grid loses.
>
> Print out which player wins the game (the first or the second).
>
> 在 $n\times n$ 一个矩阵上玩游戏。每个玩家一轮可以在一个空白格子上画圈圈。这个空白格子不能和其他已经被画了圈圈的格子共享变，可以共享角。不能画的那个玩家输掉比赛。
>
> $1\leq n\leq 10^{18}$

Thinks of mirroring to the middle point. If player $1$ paints a grid, then player $2$ points a grid mirrored to it with the middle point. In a grid with a length of an odd number, the first player places it in the middle point of the board, then whatever the second player does, the first player just does the mirror to it. Then, it is always the first player who paints the last grid. 

Conversely, when $n$ is even, the second player can always mirror the behavior of the first one. Then the second player always wins the game. 

$n$ is large, pay attention to the type of $n$ , int or long long.

Time Complexity: $O(1)$

Space Complexity: $O(1)$

镜像思考：

如果 $n$ 是奇数，玩家 $1$ 直接在中心点放一个就可以了，之后玩家 $1$ 镜像操作每一个玩家 $2$ 操作的。

如果 $n$ 是偶数，玩家 $2$ 直接镜像操作每一个玩家 $1$ 操作的。

$n$ 很大，注意 $n$ 是 long long 类别。

时间复杂度: $O(1)$

空间复杂度: $O(1)$

```C++
#include<bits/stdc++.h>
using namespace std;
long long n; 
int main(){
	scanf("%lld",&n);
	printf("%d\n",n&1?1:2);
	return 0;
}
```

