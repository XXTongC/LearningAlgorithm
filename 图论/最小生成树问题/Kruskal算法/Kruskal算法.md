# Kruskal算法
时间复杂度：O(mlogm)     
![Kruskal算法的思想](./pic/Idea%20of%20Kruskal.png)
Kruskal算法的基本思想：    
**维护一个森林，这个森林是一个树的集合**
1. 初始时，我们有由n个节点（所有节点）单独组成的n棵树    
2. 每一轮循环检查一条边，如果边在不会构成回路且是相对最短边，那么我们选中这条边使两个数合并起来，这时数就减少了一棵   
3. 当只剩下一棵树的时候我们就结束循环    
4. 每一轮循环研究一条边，所以循环的次数不会超过图中边的数量

## 题一
![题一](./pic/Question1.png)

```cpp
#include <iostream>
#include <algorithm>

const int N = 100010;
int n, m;
int sum = 0;
int p[N];
struct node
{
	
	int a;
	int b;
	int w;
	bool operator<(const node& d) const
	{
		return w < d.w;
	}
}Nodes[2*N];
int find(int x)     //并查集+路径优化，用于储存各棵树
{
	if (p[x] != x) p[x] = find(p[x]);
	return p[x];
}

int main()
{
	int count = 1;
	std::cin >> n >> m;
	int copy = m;
	for(int i = 0;i<m;i++)
	{
		int a, b, c;
		std::cin >> a >> b >> c;
		if(a!=b)
		{
			Nodes[i] = { a,b,c };
		}
	}
	std::sort(Nodes, Nodes + copy);     //以边的长度进行排序
	for (int i = 1; i <= n; i++) p[i] = i;  //初始化森林

	for(int i =0;count!=n&&i<m;i++) //如果森林里只剩一棵树了可以提前结束，减少时间消耗
	{
		int a = Nodes[i].a, b = Nodes[i].b, w = Nodes[i].w;
		a = find(a), b = find(b);
		if(a!=b)
		{
			sum += w;
			count++;
			p[a] = b;
		}
	}
	if (count < n) std::cout << "impossible";
	else std::cout<<sum;
}
```
