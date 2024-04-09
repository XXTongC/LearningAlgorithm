# Dijkstra求最短路（正权边）
![题一](./pic/Question1.png)
此题需要首先理解图论的Dijkstra算法后再来理解代码，这里直接贴代码
```cpp
//朴素版dijkstra算法，使用邻接矩阵
#include <cstring>
#include <iostream>
#include <algorithm>

const int N = 510;  //题目500点却有10w的边，说明是稠密图，所以使用邻接矩阵
int n, m;
int g[N][N];    //邻接矩阵，记录相邻点的distance值
int dist[N];    //记录点到起点的距离
bool st[N];		//该节点是否已经确定最短路径


int dijkstra()
{
	memset(dist, 0x3f, sizeof dist);
	dist[1] = 0;

	for(int i = 0;i<=n-1;i++)       //迭代到n-1是因为要确定起点到所有节点的最短路径
	{
		int t = -1;
		for (int j = 1; j <= n; j++)		//这个for循环就是模拟的优先队列
			if (!st[j] && (t == -1 || dist[t] > dist[j]))
				t = j;
		st[t] = true;   //此时我们的t指向已经确定最短距离节点之后距离起点最近的节点
		for (int j = 1; j <= n; j++)
			dist[j] = std::min(dist[j], dist[t] + g[t][j]);     //更新最短距离，检查是否存在经过节点t到j的距离能大于其他已确定路径的最短距离
	}
	if (dist[n] == 0x3f3f3f3f) return -1;
	else return dist[n];
}
int main()
{
	std::cin >> n >> m;

	memset(g, 0x3f,sizeof g);
	while(m--)
	{
		int a, b, c;
		std::cin >> a >> b >> c;
		g[a][b] = std::min(c, g[a][b]);
	}
	std::cout << dijkstra();
}
```

使用堆优化的算法：

```cpp
//使用堆优化的代码
#include <iostream>
#include <queue>
#include <utility>
#include <cstring>
int  n, m;
const int N = 1000010;
int h[N], e[N], next[N],idx,w[N];
int dist[N];
using PII = std::pair<int, int >;
bool st[N];
void add(int a,int b,int c)
{
	e[idx] = b, w[idx] = c, next[idx] = h[a], h[a] = idx++;
}

int dijkstra()
{
	memset(dist, 0x3f, sizeof dist);
	dist[1] = 0;

	std::priority_queue<PII, std::vector<PII>, std::greater<PII>> heap;		//距离，编号

	heap.push({0,1});
	while(heap.size())
	{
		auto t = heap.top();
		heap.pop();

		int ver = t.second, distance = t.first;
		if(ver == n) return dist[n];
		if(st[ver]) continue;
		st[ver] = true;
	
		for(int  i =h[ver];i!=-1;i = next[i])
		{
			int j = e[i];
			if (dist[j] > (dist[ver] + w[i]))
			{
				dist[j] = distance + w[i];
				heap.push({ dist[j],j });
			}
		}
	}
	if (dist[n] == 0x3f3f3f3f) return -1;
	else return dist[n];
}

int main()
{
	memset(h, -1, sizeof h);
	
	std::cin >> n >> m;
	while(m--)
	{
		int a, b, c;
		std::cin >> a >> b >> c;
		add(a, b, c);
	}
	std::cout << dijkstra();
}

```