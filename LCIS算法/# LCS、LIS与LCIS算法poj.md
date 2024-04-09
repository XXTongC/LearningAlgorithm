# LCS、LIS与LCIS算法poj.2127
## 一、概论
题目要求：给予两个数组，寻找它们之间最大的公共递增子序列，并且输出其长度与序列组成。    
分析：其涉及了LCS（最长公共子序列）、LIS（最长递增子序列）的内容，选择算法方法为动态规划。    
## 二、设计与实现 
以下三种算法均采用动态规划，并且使用五步法来推出程序设计流程。    
动态规划含义：动态规划的英文为Dynamic Programming，简称DP，如果某一问题有很多重叠子问题，使用动态规划将会十分有效。在动态规划中的每一个状态一定是由上一个状态推导出来的。   
五步法(图5)：   
1.确定dp数组（dp table）及下标含义。   
2.确定递推公式。   
3.初始化dp数组。   
4.确定遍历顺序。   
5.举例推导dp数组。   

图5 五步法的流程图


### ①首先对LIS算法进行分析。
leetcode.300   
1.dp[i]代表i及i之前最长的递增子序列   
2.dp[i]等于arr[i]>arr[k](i>k)时最大的dp[k]加上一，即    
if(arr[i]>arr[k]) dp[i]=std::max(dp[i],dp[k]+1)因为这里的dp[i]是在变化的，所以说其实dp[i]与dp[k]+1的比较是在寻找i之前最大的递增子序列长度    
3.因为只有一个系列，单个元素也可以视为一个递增子序列，所以dp全部初始化为1    
4.因为子序列是有顺序的，我们选择从前到后遍历，内层循环也一样   
5.举例推导过程略过    
写出算法核心代码：
```cpp
for (int i = 1; i < size; i++)
	{
		for (int j = 0; j < i; j++)
		{
			if (nums[i] > nums[j])
			{
				dp[i] = std::max(dp[i], dp[j] + 1);
				result = result < dp[i] ? dp[i] : result;
			}
		}
}
```
因为不是最终题目，只给出分析与核心代码，不给出测试与总结。    
### ②对LCS算法进行分析
leetcode.1143   
1.dp[i][j]指的是长度为[0,i-1]的Text1字符串与长度为[0,j-1]的Text2字符串的最长公共子序列。   
2.如果Text[i-1]与Text[j-1]相等，那么dp[i][j]=dp[i-1][j-1]+1,    
如果不相等，那么dp[i][j]=std::max(dp[i-1][j],dp[i][j-1])。     
3.因为两者之间会出现完全不重复的情况，所以dp初始值为0。    
4.遍历顺序均为递增。    
5.举例推导过程略过    
写出算法核心代码：
```cpp
	for (i = 1; i <= nums1.size(); i++)
	{
		for (j = 1; j <= nums2.size(); j++)
		{
			if (nums1[i - 1] == nums2[j - 1])
			{
				dp[i][j] = dp[i - 1][j - 1] + 1;
			}
			else
			{
				dp[i][j] = std::max(dp[i][j - 1], dp[i - 1][j]);
			}
		}
	}
```
因为当我们的i与j分别等于两个字符串的长度时，就指的是Text1字符串与Text2字符串的最长公共子序列，所以答案输出dp[Text1.size()][Text2.size()]即可 。     
### ③最后我们通过LCS与LIS的学习来推导LCIS的算法   

1. dp[i][j]是以[0,i-1]的arr1与[0,j-1]的arr2且以arr2[j]结尾构成的最长公共递增子序列   

2. 当arr1[i-1]=arr2[j-1]时，如果arr2[j-1]>arr2[k-1](j>k)则dp[i][j]=std::max(arr[i][k],arr[i-1][k]+1)；当arr[i-1]!=arr2[j-1]时，dp[i][j]=dp[i-1][j]（这里需要特别注意的是我们的dp[i][j]对应的是arr1[i-1]与arr2[j-1]）。   
3. 两者之间可能会出现没有公共数据的情况，所以可以之间初始化为0    
4. 遍历顺序依然是递增。    

此处可以写出核心算法：
```cpp
		for (i = 1; i <= nums1.size(); i++)
		{
			for (j = 1; j <= nums2.size(); j++)       
			{
				if (nums2[j-1] == nums1[i-1])
				{
					int Max = 0;
					for (int k = 1; k < j; k++)
						if (nums2[j-1] > nums2[k-1])
							Max = std::max(Max, dp[i - 1][k]);
					dp[i][j] = Max + 1;
				}
				else
				{
					dp[i][j] = dp[i - 1][j];
				}
				result = result < dp[i][j] ? dp[i][j] : result;
			}
		}
```
5.举例推导过程(图6)：   
arr1={1 ,2 ,8 ,0 ,2 ,9 }   
arr2={2 ,0 ,3 ,2 ,9 ,1 }   
   
图6 举例推导得到dp图    
通过举例推导，我们发现一些规律，比如其中最大的值只会出现在dp[arr1.size()][j]中，而后我们可以选择优化算法，每次都只维护dp的最大值，通过列的变化来得出最大值，达到时间复杂度从O(n^3)--->O(n^2)的优化，更新核心算法：     
```CPP
		for (i = 1; i <= nums1.size(); i++)
		{
			int Max = 0;
			for (j = 1; j <= nums2.size(); j++)
			{
				if(nums1[i-1]!=nums2[j-1])
					dp[j] = dp[j]; //a[i] != b[j]
				if (nums1[i-1] > nums2[j-1]) 
					Max = max(Max, dp[j]);
				if (nums1[i-1] == nums2[j-1]) 
					dp[j] = Max + 1;
				result = result < dp[j] ? dp[j] : result;
			}
		}
```
但是我们注意到题目要求我们还需要输出最长的那个数组的各个元素。这时候我们使用结构体，也就是说我们的额外记录dp数组中每一个dp[i]所构成的那几个元素。我们可以通过构造结构体来实现这个功能，并且在递推过程中，元素组成也遵循dp数组的递推规律，所以以上代码更新如下：   
```cpp
    for (int i = 1; i <= n; i++)
    {
        Node Max;
        for (int j = 1; j <= m; j++)
        {
            if (b[i] > a[j] && dp[j].val > Max.val) Max = dp[j];
            if (b[i] == a[j])
            {
                dp[j].val = Max.val + 1;
                dp[j].v = Max.v;
                dp[j].v.push_back(b[i]);
            }
        }
}
```
最后，我们实现整个程序的书写：
```cpp
#include <iostream>
#include <vector>
using namespace std;
class Solution2 {
public:
	struct Node
	{
		int val = 0;
		std::vector<int> v;
	};
	void findLength(vector<int>& nums1, vector<int>& nums2) {

		Node dp[505];
		int i, j;
		int m = nums1.size() - 1;
		int result = 0;

		for (i = 1; i <= nums1.size() - 1; i++)
		{
			Node Max;
			for (j = 1; j <= nums2.size() - 1; j++)
			{
				if (nums1[i] > nums2[j] && (Max.val < dp[j].val))
					Max = dp[j];
				if (nums1[i] == nums2[j])
				{
					dp[j].val = Max.val + 1;
					dp[j].v = Max.v;
					dp[j].v.push_back(nums1[i]);
				}
				result = result < dp[j].val ? dp[j].val : result;
			}
		}
		Node Max = dp[1];
		for (int i = 2; i <= m; i++)
		{
			if (dp[i].val > Max.val)
				Max = dp[i];
		}
		cout << Max.val << endl;
		for (int i = 0; i < Max.v.size(); i++)
			cout << Max.v[i] << " ";
		cout << endl;
		return;
	}
};
int main()
{
	Solution2 a;
	std::vector<int> nums1(1,0);
	std::vector<int> nums2(1,0);
	int m, n,i,temp;
	std::cin >> m;
	for (i = 1; i <= m; i++)
	{
		std::cin >> temp;
		nums1.push_back(temp);
	}
	std::cin >> n;
	for (i = 1; i <= n; i++)
	{
		std::cin >> temp;
		nums2.push_back(temp);
	}
	a.findLength(nums1, nums2);
}
最后我们注意一下主函数中数组的输入，我们将两个数组的第一个元素都空了出来，这是为了方便算法在执行的时候方便下标的一一对应。
示例模拟(图7 第一个数代表val,后面的则是数组)：

图7 示例模拟

三、系统测试
poj测试用例（图8 poj2027测试输出）：
5
1 4 2 5 -12
4
-12 1 2 4
标准答案：
2
1 4

图8 poj2027测试输出
答案不同是因为只需要输出一种，显然，我输出的是另一种。
四、评价与结论
具体实验中遇到如下几个问题：
1.算法的选择
此题的算法可以结合多种思想，如“分治”和“贪心”等，同时最后实现逻辑上的优化也是重要的一部，如上述内容就通过实例分析得出从O(n^3)--->O(n^2)的优化算法。
2.此题还涉及到了元素地址或元素本身的转存
这是一个复杂的问题，通过查询，互联网上给出的方法很多，如“二维数组地址记录”、“dp数组的倒推”等方法，但是在个人对LCIS算法过程的分析中捕捉到了最长子序列与外层循环数组nums1[i]的关系，发现了可以通过状态变化时同时记录每一个dp[j]对应的其中一个最长子序列（Max取值方式不同决定了取到同长度的不同子序列）。再通过网络的帮助，成功实现了这个算法。