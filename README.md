# [leetcode analysis](#leetcode-analysis)
<br/>
<br/>

<h1 id="0">TABEL OF CONTENTS</h1>  

- ## 动态规划专题 Dynamic Programing
	1. [线性DP](#1)
		- [300.  最长上升子序列 LIS](#1.1)
		- [1143. 最长公共子序列 LCS](#1.2)
        - [120.  三角形最小路径和](#1.3)  
        - [53.   最大子序和](#1.4)   
        - [152.  乘积最大子数组](#1.5)  
        - [887.  鸡蛋掉落](#1.6) 
        - [354.  俄罗斯套娃信封问题](#1.7) 
        - [198.  打家劫舍](#1.8) 
		- [213.  打家劫舍 Ⅱ](#1.9)  
		- [121.  买卖股票的最佳时机](#1.10)  
		- [122.  买卖股票的最佳时机Ⅱ](#1.11)  
		- [123.  买卖股票的最佳时机Ⅲ](#1.12)  
		- [188.  买卖股票的最佳时机Ⅳ](#1.13)   
		- [309.  最佳买卖股票时机含冷冻期](#1.14)   
		- [714.  买卖股票的最佳时机含手续费](#1.15)   
		- [72.   编辑距离](#1.16)   
		- [44.   通配符匹配](#1.17)   
		- [10.   正则表达式匹配](#1.18)   
		


	2. [区间DP](#2)  
		- [516.  最长回文子序列](#2.1)  
		- [730.  统计不同回文子字符串](#2.2)  
		- [1039. 多边形三角剖分的最低得分](#2.3)      
		- [664.  奇怪的打印机](#2.4)        
		- [312.  戳气球](#2.5)     
		

	3. [背包DP](#3)  
	  
	  
	
	4. [树形DP](#4)
		- [337. 打家劫舍 Ⅲ](#4.1)  
		

   
   

  <br/>
  <br/>
  <br/>
  <br/>
  
  
<h1 id="1"> 线性DP </h1> [回到目录](#0)  
  <br/>
  
<h1 id="1.1"> LeetCode 300 </h1>  [回到目录](#0)  
## 1 [最长上升子序列 longest increasing subsequence](https://leetcode-cn.com/problems/longest-increasing-subsequence)

## 1.1 题目描述
给定一个无序的整数数组，找到其中最长上升子序列的长度。

#### 示例:
```
输入: [10,9,2,5,3,7,101,18]
输出: 4 
解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
```

#### 说明:
* 可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。  
* 你算法的时间复杂度应该为 O(n2)。    

**进阶:** 你能将算法的时间复杂度降低到 O(n log n) 吗? 

## 1.2 解题思路
看完题目描述，我还以为是要找的这个子序列是连续元素组成，直到看到示例，才发现这个子序列可以不连续。如果要求的子序列是连续的话，这题就简单多了，直接遍历一遍，比较相邻元素的大小即可。  
那么对于可以不连续的子序列来说，问题好像没那么简单了。  
首先能想到的最直接的方法，就是暴力穷举的方式，但是复杂度比较高，这里就不具体说了。  
下面看下动态规划解法。  

### 1.2.1 动态规划
我们再来分析下示例 [10,9,2,5,3,7,101,18] ，其实针对每个元素来说，它及之前元素组成的数组中的最长上升子序列都是确定了的，也就是说具有最优子结构，这样我们就可以从后往前逐步计算出整个数组的最长上升子序列。  
分两步走：  
1 状态的定义：定义DP[i]，表示第i个元素及其前面元素组成的子数组(下标0-i)中，最长的上升子序列的长度。
2 状态转移方程：在第i个元素之前的所有子数组中，对于j(j<i)，第j个元素及之前的元素组成的子数组中上升子序列长度就是DP[j]，那么对于nums[j]<nums[i]时,则DP[i]=max{DP[j]+1}，否则DP[i]不变。

这里做了两重循环，所以复杂度为O(N²)，空间复杂度也是O(N²)。

c++代码实现如下：  
```c++
int lengthOfLIS(vector<int>& nums) {
    int vec_len = nums.size();
    if(vec_len < 2) //数组长度为0，返回0；数组长度为1，则最长上升子序列就是1
        return vec_len;

    int max_len = 1;
    vector<int> DP(vec_len, 1); //存放第i个元素及之前的，所有上升子序列的长度，初始都是1

    for(int i = 1; i < vec_len; i++){ //逐个元素，计算其前面所有元素组成的数组中，最长的上升子序列长度
        for(int j = 0; j < i; j++){
            if(nums[j] < nums[i]){ //因为是按递增来算长度
                DP[i] = std::max(DP[i], DP[j] + 1); //第i个元素及其前面元素中，最长子序列长度就是
                                                          //取其前面元素中最大的+1与当前最大的长度中最大的
            }                 
            //else if(nums[j] >= nums[i]){} 这里就不是上升序列了，所以长度不变
        }
        max_len = std::max(max_len, DP[i]);
    }

    return max_len;
}
```   


### 1.2.2 改进版
这个是我看到题解中的解法，很巧妙的解法，它把上面动态规划的解法中，第二重循环做了优化。它是维护了一个数组，用来暂存了可能成为最长上升子序列的所有元素，最后这个数组的长度就是要求的最长上升子序列的长度，具体做法如下：
逐个的取出原数组nums中元素，往临时数组tmp_lis中放，若该元素a大于tmp_lis中所有元素，则追加到tmp_lis最后；若不是，则找出tmp_lis中大于该元素a的最小的元素b，然后用a替换tmp_lis中的这个元素b，直到nums数组遍历完毕。

我们来看下，对于示例数组：
[10,9,2,5,3,7,101,18]
它是逐个的遍历数组元素，往另一个数组中放，假设这个数组为tmp_lis[]。  

取第一个元素10：  
tmp_lis[] = {10}  
取第二个元素9，因为9小于10，则用9替换10：  
tmp_lis[] = {9}  
取第三个元素2，因为2  
tmp_lis[] = {2}  
取第四个元素5，因为5>2,所以追加到tmp_lis中   
tmp_lis[] = {2,5}  
取第五个元素3，因为2<3<5,所以用3替换5  
tmp_lis[] = {2,3}  
取第六个元素7，因为7>3>2,所以直接追加到tmp_lis中  
tmp_lis[] = {2,3,7}  
取第七个元素101，因为101>7>3>2,所以直接追加到tmp_lis中  
tmp_lis[] = {2,3,7,101}  
取第八个元素18，因为18<101,18>7,所以用18替换101  
tmp_lis[] = {2,3,7,18}  
所以最终最长上升子序列的长度就是tmp_lis的长度，为4。  

对于查找tmp_lis中比nums[i]大的最小元素，我们采用二分法查找，所以这层循环的复杂度是O(logN)，加上外层的O(N)的复杂度，最终算法复杂度就是O(NlogN)。  
**注意:** tmp_lis最后并非就是最长上升子序列，它只是暂存了曾经是最长上升子序列的数组，可以自行以[6,7,8,9,1,2,3]为例自行验算下。

c++代码实现如下：  
```c++
int lengthOfLIS(vector<int>& nums) {
    vector<int> tmp_lis;
    for(int i = 0; i < nums.size(); i++){
        auto it = std::lower_bound(tmp_lis.begin(), tmp_lis.end(), nums[i]);/二分查找
        if(it != tmp_lis.end())
            *it = nums[i];
        else
            tmp_lis.push_back(nums[i]);//因为tmp_lis已经有序，所以找不到比*it大的数据的时候，则*it比tmp_lis中所有元素都大，直接追加
    }

    return tmp_lis.size();
}
```   
  
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.2">LeetCode 1143</h1>  [回到目录](#0)  
## 2 [最长公共子序列 longest common subsequence](https://leetcode-cn.com/problems/longest-common-subsequence/)

## 2.1 题目描述
给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长公共子序列。

一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。

若这两个字符串没有公共子序列，则返回 0。

#### 示例 1:
```
输入：text1 = "abcde", text2 = "ace" 
输出：3  
解释：最长公共子序列是 "ace"，它的长度为 3。
```

#### 示例 2:
```
输入：text1 = "abc", text2 = "def"
输出：0
解释：两个字符串没有公共子序列，返回 0。
```

#### 提示:
* 1 <= text1.length <= 1000  
* 1 <= text2.length <= 1000
* 输入的字符串只含有小写英文字符。    


## 2.2 解题思路
首先最直观的方法还是可以暴力穷举法，找出text1、text2的所有子序列，然后逐个判断是否匹配，最后找到最长的匹配的子序列即得到其长度。这种方法复杂度比较高，不去看了。

再来仔细分析下，看下如下示例，text1="bcdefg",text2="cbfdg",建立二维表如下，当行列对应的字符相同时，对应的值置1。  

  
|  | b | c | d | e |f|g|
|---|---|--|--|--|--|--|
| c |   | 1 |||||
| b |1 | || |||
|f|||||1||
|d|||1||||
|g||||||1||  
  
可以看出，将这个二维表遍历，当我们只向下或向右交替着遍历(为了保持字符在原串中的顺序)时，遇到值为1的就取出对应字符，最终组成的字符串就是公共子序列(bdg,bfg、cdg,cfg)，故而就能得到最长公共子序列了。之后对这个二维数组按上述方法找出1最多的就是答案了，此解法还有很大优化空间，比如下面的DP解法。

还有就是动态规划的解法了，我们深入分析题目之后，会发现本题跟LeetCode 300题的最长上升子序列问题有点类似，到两个字符串的某一个字符的时候，其实其最长公共子序列的长度是确定的，存在最优子结构，可以从前面的字符串逐步往后推导出最后的最优解，其实也是对上面那个解法的优化。下面就来重点看下动态规划解法。 

## 2.3 解决方案
对于动态规划，首先还是两步走：  
1 定义状态DP[i][j]，表示字符串text1[0..i]跟text2[0...j]之间拥有的最长公共子序列的长度。  
2 状态转移方程：  
可以有三种状态到达DP[i][j]，分别为DP[i-1][j-1]、DP[i-1][j]、DP[i][j-1]，对于这三个前序状态：  
如果text1[i]==text2[j]的话，DP[i][j]=DP[i-1][j-1]+1，其它情况则，DP[i][j]取的应该是DP[i-1][j]、DP[i][j-1]中最大的。  

下面我们看下具体代码实现。

### 2.3.1 递归法
根据我们上面动态规划里面的递推公式，其实可以用递归方法解决，代码如下：
```c++
int lcs(string &t1, string &t2, int len1, int len2)
{
    if (len1 == 0 || len2 == 0)
        return 0;

    if (t1[len1-1] == t2[len2-1])
        return lcs(t1, t2, len1-1, len2-1) + 1;
    else
        return max(lcs(t1, t2, len1, len2-1), lcs(t1, t2, len1-1, len2));
}
int longestCommonSubsequence(string text1, string text2) {
    return lcs(text1, text2, text1.size(), text2.size());
}
```


### 2.3.2 动态规划
上面递归法中存在大量重叠计算问题，时间及空间复杂度比较高，下面动态规划解法优化了上面求解过程，代码如下：
```c++
int longestCommonSubsequence(string text1, string text2) {
    if(text1.empty() || text2.empty())
        return 0;
    
    int len1 = text1.size();
    int len2 = text2.size();
    vector<vector<int>> DP(len1+1, vector<int>(len2+1, 0)); //二维数组初始化为0

    for(int i = 1; i <= len1; i++){
        for(int j = 1; j <= len2; j++){
            if(text1[i-1] == text2[j-1])
                DP[i][j] = DP[i-1][j-1] + 1;
            else
                DP[i][j] = max(DP[i-1][j], DP[i][j-1]);
        }
    } 

    return DP[len1][len2];
}
```   
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.3">LeetCode 120</h1>  [回到目录](#0)  
## 3 [三角形最小路径和 triangle](https://leetcode-cn.com/problems/triangle/)   
## 3.1 题目描述
给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

#### 示例:
```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```
自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。

#### 说明:
如果你可以只使用 O(n) 的额外空间（n 为三角形的总行数）来解决这个问题，那么你的算法会很加分。

## 3.2 解题思路
1、暴力枚举法，找出所有路径并计算出他们的和，找出最小的即可，这样的话复杂度就是O(2的N次方)。

2、可以尝试用贪心法，本题示例好像是没问题，但是我们仔细想想就感觉不对了，看下面示例：  
```
[
      [2],
    [4, 8],
   [2, 3, 1],
  [70,60,15,6]
]
```  
按照贪心的思路，就是2+4+2+60=68，显然不对，为什么呢？因为本题中我们前面选的元素位置，就决定了后面我们只能选哪些数据。故贪心法不适合。  
  
3、动态规划解法，对于动态规划解法一般都是从后往前推的思路，跟第一种回溯法思路反过来。下面我们就来重点分析下动态规划。  


  
## 3.3 解决方案  
  
### 3.3.1 动态规划
DP状态定义：DP[i][j]表示从底端往上到第i行第j列时，走过的路径和的最小值。  
状态转移方程：DP[i][j]=min(DP[i+1][j],DP[i+1][j+1])+triagle[i][j];  
因为是至底向上找，所以初始值就是最后一行的元素的值，即DP[m-1][j]=triangle[m-1][j]，最后路径和的最小值就是在DP[0][0]中。  
该算法复杂度为O(m*n)，空间复杂度也是O(m*n)。

c++代码实现如下：  

```c++
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        if(triangle.empty())
            return 0;

        int m = triangle.size();
    	if(m == 1)
    		return triangle[0][0];

    	int n = triangle[m-1].size();
    	vector<vector<int>> DP(m, vector<int>(n,0));

    	DP[m-1] = triangle[m-1]; //初始化最后一行

    	for (int i = m-2; i >= 0; i--){ //从倒数第二行开始
    		for (int j = 0; j < triangle[i].size(); j++){
    			DP[i][j] = min(DP[i+1][j], DP[i+1][j+1]) + triangle[i][j];
    		}
    	}

    	return DP[0][0];
    }
};
```
上面代码可以改进下，只用一个一维数组存放DP状态，因为其实在计算过程中，始终只使用了其中的一层状态，完全可以复用的，代码如下：
```c++
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int m = triangle.size();
    	vector<int> DP(m, 0);

    	DP = triangle[m-1]; //初始化最后一行

    	for (int i = m-2; i >= 0; i--){ //从倒数第二行开始
    		for (int j = 0; j < triangle[i].size(); j++){
    			DP[j] = min(DP[j], DP[j+1]) + triangle[i][j];
    		}
    	}

    	return DP[0];
    }
};
```

我把上面c++代码改成了c代码之后，提交leetcode,执行用时降低了1/3，可见还是c代码效率高啊
```c
int min(int x, int y){
    return (x<y)?x:y;
}

int minimumTotal(int** triangle, int triangleSize, int* triangleColSize){
    if(triangle == NULL || *triangle == NULL || triangleSize == 0 || *triangleColSize == NULL)
        return 0;

    if(triangleSize == 1)
        return triangle[0][0];
    int *DP = (int*)malloc(triangleColSize[triangleSize-1]*sizeof(int));
    for(int i = 0; i < triangleColSize[triangleSize-1]; i++)
        DP[i] = triangle[triangleSize-1][i];

    for (int i = triangleSize-2; i >= 0; i--){ //从倒数第二行开始
        for (int j = 0; j < triangleColSize[i]; j++){
            DP[j] = min(DP[j], DP[j+1]) + triangle[i][j];
        }
    }

    int min_len = DP[0];
    free(DP);
    return min_len;
}
```  

### 3.3.2  其它解法  

题解中看到一种巧妙的解法，因为本题要求的是每个数下面一层，只能取相邻的俩数，所以我们可以跟动态规划相同思路，从底往上，相邻的两个数取最小的，加到上面一层，逐层这样操作，最后最顶端的元素triangel[0][0]的值，即为路径和的最小值。  
c++代码如下：  
```c++
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
    	if(triangle.empty())
    		return 0;

    	for (int i = triangle.size()-1; i > 0; i--){ 
    		for (int j = 0; j < triangle[i].size()-1; j++){
    			triangle[i-1][j] = min(triangle[i][j], triangle[i][j+1]) + triangle[i-1][j];
    		}
    	}

    	return triangle[0][0];
    }
};
```  
  
  <br/>
  <br/>
  <br/>
  
  
***
<h1 id="1.4"> LeetCode 53 </h1>  [回到目录](#0)  
## 4 [最大子序和 maximum subarray](https://leetcode-cn.com/problems/maximum-subarray/)

## 4.1 题目描述
给定一个整数数组`nums`，找到一个具有最大和的连续子数组(子数组最少包含一个元素)，返回其最大和。

#### 示例:
```
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```  

**进阶:** 如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的分治法求解  

## 4.2 解题思路
① 暴力枚举法，找出所有连续子序列并计算出他们的和，找出最大的即可，这样的话复杂度就是O(2的N次方)。  
② 动态规划解法，做过了前面几道题，这题就相对简单点了。  
下面看下动态规划解法。  
  
  

## 4.3 解决方案  

### 4.3.1 动态规划  
分两步走：  
1 状态的定义：定义DP[i]，表示第i个元素及其前面元素组成的子数组(下标0-i)中，连续子序列的和。  
2 状态转移方程：DP[i]=max(DP[i], DP[i-1])+nums[i],初始DP[0] = nums[0]。  

我们可以用一个临时变量存储到i处时当前最大的连续子序列和，遍历完数组后，临时变量的值就是要求的值，这里只需要一层循环，故复杂度为O(N).  


c++代码如下：
```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if(nums.empty())
    		return 0;

    	int len = nums.size();
    	vector<int> DP(len);
    	DP[0] = nums[0];
    	int res = DP[0];

    	for(int i = 1; i < len; i++){
    		DP[i] = max(DP[i], DP[i-1]) + nums[i];
    		res = max(res, DP[i]);
    	}
    	
    	return res; 
    }
};
```
对应的c代码如下：
```c
int max(int x, int y){
    return (x>y)?x:y;
}

int maxSubArray(int* nums, int numsSize){
	if(nums == NULL || numsSize <= 0)
		return 0;
    
    int *DP = (int*)malloc(numsSize*(sizeof(int)));
    memset(DP, 0, numsSize*(sizeof(int)));
	DP[0] = nums[0];

	int res = DP[0];
	for(int i = 1; i < numsSize; i++){
		DP[i] = max(DP[i], DP[i-1]) + nums[i];
		res = max(res, DP[i]);
	}
    free(DP);
	return res;
}
```

### 4.3.2 其它解法
看到题解中有个方法挺好，这里也说下，他的思路是这样的：
- 对数组进行遍历，当前最大连续子序列和为`sum`，结果为`ans`.  
- 如果`sum > 0`，则说明`sum`对结果有增益效果，则`sum`保留并加上当前遍历数字.  
- 如果`sum <= 0`，则说明`sum`对结果无增益效果，需要舍弃，则`sum`直接更新为当前遍历数字.  
- 每次比较`sum`和`ans`的大小，将最大值置为`ans`，遍历结束返回结果.  
这样只需额外几个变量即可，也节省了内存。  

我把它写成c代码如下：
```c
int max(int x, int y){
    return (x>y)?x:y;
}

int maxSubArray(int* nums, int numsSize){
	if(nums == NULL || numsSize <= 0)
		return 0;
    
    int res = nums[0];
    int sum = 0;
    for(int i = 0; i < numsSize; i++) {
        if(sum > 0)
            sum += nums[i];
        else
            sum = nums[i];

        res = max(res, sum);
    }
    return res;
}
```
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.5"> LeetCode 152 </h1>  [回到目录](#0)  
## 5 [乘积最大子数组 maximum product subarray](https://leetcode-cn.com/problems/maximum-product-subarray/)  


## 5.1 题目描述
给你一个整数数组`nums`，请你找出数组中乘积最大的连续子数组(该子数组中至少包含一个数字)。

#### 示例 1:
```
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```    
#### 示例 2:
```
输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
```  

## 5.2 解题思路
① 暴力枚举法，也叫递归法，这里就不看了。  
② 动态规划解法，跟上面那题子序列和最大类似，不过比它复杂点。  
下面具体看下解法。  
  
  

## 5.3 解决方案  

### 5.3.1 动态规划  
因为这里整数数组里可能有为负数的值，而且负数乘以负数又会是正的值，那么我们不能像上面那题求子数组和最大的求法了，这里对于每个nums[i]，要计算正的最大值，也要计算最小值，因为最小值为负，则后面如果有负数的话，负的最小值乘以负数，就会是正的最大值了。  
分两步走：  
1 状态的定义：定义DP[2][i][，DP[0]里存的都是最大值，DP[1]里存的都是最小值；DP[0][i]表示第i个元素及其前面元素组成的子数组(下标0-i)中，连续子序列乘积的最大值，DP[1][i]表示第i个元素及其前面元素组成的子数组(下标0-i)中，连续子序列乘积的最小值。  
2 状态转移方程：  
DP[0][i] = max(DP[0][i-1]*nums[i], DP[1][i-1]*nums[i], nums[i])  
DP[1][i] = max(DP[1][i-1]*nums[i], DP[0][i-1]*nums[i], nums[i])  
那么最大子数组乘积就是上面最大值中的最大值：  
max_product = max(DP[0][0...n]);  


我们可以用一个临时变量存储到i处时当前最大的连续子序列和，遍历完数组后，临时变量的值就是要求的值，这里只需要一层循环，故复杂度为O(N). 

c++代码如下，这里我用了俩数组，效果跟DP[2][i]一样：  
```c++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        if(nums.empty())
    		return 0;

    	int len = nums.size();

    	vector<int> max_val(len);
    	vector<int> min_val(len);
    	max_val[0] = nums[0];
    	min_val[0] = nums[0];
    	int max_product = nums[0];	

    	for(int i = 1; i < len; i++){
    		max_val[i] = max(max(max_val[i-1]*nums[i], min_val[i-1]*nums[i]), nums[i]);
    		min_val[i] = min(min(max_val[i-1]*nums[i], min_val[i-1]*nums[i]), nums[i]);
    		max_product = max(max_product, max_val[i]);
    	}
	    
    	return max_product;
    }
};
```  

这里可以优化下，上面其实没必要用数组， 因为我们每次都取最小或最大值，所以可以只用两个变量存储，改进代码如下:  
  
```c++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        if(nums.empty())
        return 0;

        int max_val = nums[0], min_val = nums[0], max_product = nums[0];
        int tmp_max = 0, tmp_min = 0;   

        for(int i = 1; i < nums.size(); i++){
            tmp_max = max_val*nums[i];
            tmp_min = min_val*nums[i];

            max_val = max(max(tmp_max, tmp_min), nums[i]);
            min_val = min(min(tmp_max, tmp_min), nums[i]);

            max_product = max(max_product, max_val);
        }
        
        return max_product;
    }
};
```  
  
  <br/>
  <br/>
  <br/>  
  
***  
<h1 id="1.6"> LeetCode 887 </h1>  [回到目录](#0)  
## 6 [鸡蛋掉落 super egg drop](https://leetcode-cn.com/problems/super-egg-drop/)

## 6.1 题目描述
> 你将获得 `K` 个鸡蛋，并可以使用一栋从 1 到 `N`  共有 `N` 层楼的建筑。  
> 每个蛋的功能都是一样的，如果一个蛋碎了，你就不能再把它掉下去。  
> 你知道存在楼层 `F` ，满足 `0 <= F <= N` 任何从高于 F 的楼层落下的鸡蛋都会碎，从 `F` 楼层或比它低的楼层落下的鸡蛋都不会破。  
> 每次移动，你可以取一个鸡蛋（如果你有完整的鸡蛋）并把它从任一楼层 X 扔下（满足 `1 <= X <= N`）。  
> 你的目标是确切地知道 `F` 的值是多少。  
> 无论 `F` 的初始值如何，你确定 `F` 的值的最小移动次数是多少？  

#### 示例 1:
```
输入：K = 1, N = 2
输出：2
解释：
鸡蛋从 1 楼掉落。如果它碎了，我们肯定知道 F = 0 。
否则，鸡蛋从 2 楼掉落。如果它碎了，我们肯定知道 F = 1 。
如果它没碎，那么我们肯定知道 F = 2 。
因此，在最坏的情况下我们需要移动 2 次以确定 F 是多少。
```  

#### 示例 2:  
```
输入：K = 2, N = 6
输出：3
```  

#### 示例 3:  
```
输入：K = 3, N = 14
输出：4
```
  
#### 提示:
* 1 <= K <= 100  
* 1 <= N <= 10000  

## 6.2 解题思路  
仔细审题，在通过示例1的解释，还是比较容易的理解题目的意思，但是如何求解却没那么简单了。  

仔细思考之后，感觉如果鸡蛋够的话，可以这样：  
- 第一次从第N层仍鸡蛋，如果没碎，那F就是N了，如果碎了，则从N/2层仍鸡蛋  
  - 同理如果没碎，那么F就在N/2-N层之间；  
    - 然后同样思路在3N/4层仍鸡蛋 ....  
  - 如果碎了，那么F就在0-N/2层之间，然后再在N/4层处仍鸡蛋....    
  
    

  
这样也能最快的求解出F的值。   


但是本题中鸡蛋个数，层数，F都不确定，但至少这也是个思路了....    
  
  
我们来仔细思考给出的另外2个示例，从人正常的思考过程，最简单的求解过程应该就是至底向上，逐步的迭代着1...N层尝试，这样是可以得出结果的，也就是递归的思路；而且整个求解过程就是从某一层仍鸡蛋，碎或者不碎，然后再到另一个范围内继续同样的方式处理，也就是本题能够划分出子问题来求解。既然这样的话，那我们就可以考虑试着用动态规划的思想来解决本题了。

## 6.3 解决方案  


### 6.3.1 动态规划  
    
再来仔细审题，题目描述的最后两句：  
> 你的目标是确切地知道 `F` 的值是多少。  
最后要确切的知道这个F是多少。  
> 无论 `F` 的初始值如何，你确定 `F` 的值的最小移动次数是多少？   
**无论**`F是多少` `确定F的值的`**最小移动次数**，仔细品味下，就是要最终确定出F，**最坏情况下，最少移动的次数**。   

我们可以定义状态DP[k][n]（k个鸡蛋数，共n层），他k个鸡蛋在n层中找出确定的F最少需要移动的次数，那么如果在第n层：    
 - 鸡蛋不碎，那么我们后面就要在第n层到第N层之间来找F，就是说楼层范围变成了N-n，即状态转变成了DP[k][N-n]，因为此时鸡蛋没碎，还能继续用，k不用减1,。  
 - 鸡蛋碎了，这时F的值肯定在n层之下，楼层范围是n-1了，即状态变成了DP[k-1][n-1]。  
  
然后可以如此递推着，因为我们虽然知道F的存在，但是不知道他具体会是多少，那么要求解最坏情况下，最少移动次数的话，只能取所有上述两种情况下移动次数的最大值中的最小值。  
则状态转移方程就为：  
DP[k][n] = 1 + min(max(DP[k][N-n], DP[k-1][n-1])) 其中1≤n≤N，k范围：K->0  


初始值：  
DP[k][0]=0 (即N=0时)  
DP[k][1]=1 (N=1,只有1层时)  
DP[0][n]=0 (没有鸡蛋时)  
DP[1][n]=n (只有一个鸡蛋时，则只能至底向上逐层了)  

c++代码如下：
```c++
int superEggDrop(int K, int N) {
    vector<vector<int>> DP(N+1, vector<int>(K+1, 0xffffffff));
    for (int i = 0; i <= K; ++i) {
	DP[0][i] = 0;
	DP[1][i] = 1;
    }
    for (int i = 0; i <= N; ++i) {
	DP[i][0] = 0;
	DP[i][1] = i;
    }

for (int i = 2; i <= N; i++) {
    for (int j = 2; j <= K; j++) {
	int left = 1, right = i;
	while (left+1 < right) {
	    int mid = (left+right)/2;
	    if (DP[i - mid][j] < DP[mid-1][j-1])
		right = mid;
	    else if (DP[i-mid][j] > DP[mid-1][j-1])
		left = mid;
	    else
		left = right = mid;
	}
	DP[i][j] = min(max(DP[right-1][j-1], DP[i-right][j]), max(DP[left-1][j-1], DP[i-left][j])) + 1;
    }
}
return DP[N][K];
}
```  


  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.7"> LeetCode 354 </h1>  [回到目录](#0)  
## 7 [俄罗斯套娃信封问题 russian doll envelopes](https://leetcode-cn.com/problems/russian-doll-envelopes/)  


## 7.1 题目描述
给定一些标记了宽度和高度的信封，宽度和高度以整数对形式 `(w, h)` 出现。当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。  
  
  

请计算最多能有多少个信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。  

### 说明
不允许旋转信封。  


#### 示例:
```
输入: envelopes = [[5,4],[6,4],[6,7],[2,3]]  
输出: 3   
解释: 最多信封的个数为 3, 组合为: [2,3] => [5,4] => [6,7]。  
```    
  
  
## 7.2 解题思路
① 显然，本题可以用递归去做，但是会有很多的重复计算，复杂度高，耗时大。  
② 动态规划解法，仔细思考本题就会发现，跟之前做过的求最长上升子序列很类似，只不过这里我们要先将信封按宽度、高度先排好序。  
下面具体看下解法。  
  
  

## 7.3 解决方案  

### 7.3.1 动态规划  
注意题目中要求是一个信封的**宽度和高度都比**另一个信封的大时，才能满足放入。所以这里我们的比较函数要有两个维度的比较，先按宽度或长度比较大小，若相等则要按另一个维度继续比较排序，不然求出来的值就是不对的。  
关于DP状态定义及转移方程，跟[300.  最长上升子序列 LIS](#1.1)的思路很类似，这里就不具体说了。  
  
  
具体c++代码如下：  

```c++
bool cmp_less(vector<int> &a, vector<int> &b) 
{
	if(a[0] != b[0])
		return a[0] < b[0];
	else if (a[1] != b[1])
		return a[1] > b[1];

	return 0;
}

int maxEnvelopes(vector<vector<int>>& envelopes) 
{
	int len = envelopes.size();
	if (len <= 1)
	    return len;

	sort(envelopes.begin(), envelopes.end(), cmp_less);

	vector<int> DP(len, 1);
	int max_envelopes = 1;
	for (int i = 0; i < len; ++i) {
	    for (int j = 0; j < i; ++j) {
		if (envelopes[i][1] > envelopes[j][1])
		    DP[i] = max(DP[i], DP[j]+1);
	    }
	    max_envelopes = max(max_envelopes, DP[i]);
	}
	return max_envelopes;
}
```

  <br/>
  <br/>
  <br/>

***  
<h1 id="1.8"> LeetCode 198 </h1>  [回到目录](#0)  
## 8 [打家劫舍 house robber](https://leetcode-cn.com/problems/house-robber/)

## 8.1 题目描述
你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。  


给定一个代表每个房屋存放金额的非负整数数组，计算你在**不触动警报装置的情况下**，能够偷窃到的最高金额。  


#### 示例 1:
```
输入: [1,2,3,1]
输出: 4
解释: 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。  
     偷窃到的最高金额 = 1 + 3 = 4 。
```

#### 示例 2:
```
输入: [2,7,9,3,1]  
输出: 12  
解释: 偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。  
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。  
```  


## 8.2 解题思路  
仔细审题后，我们知道，就是求解一组数据的最大和，且所选的这些数据不能相邻。  
1、首先这道题可以用回溯递归的方式解决  
2、动态规划思路，可以从前面的子结构逐步最优求解，具体解法看下面。  
  
  

## 8.3 解决方案
### 8.3.1 动态规划  
两步走：  
1、状态定义：DP[i]表示到第i个元素时，其前面满足条件的数据元素之和的最大值。  
2、状态转移方程：DP[i] = max(DP[i-2]+nums[i], DP[i-1]) (2<=i<n)。因为第i个元素我们可以选或者不选，不选的话，就是取DP[i-1]。  
初始值：DP[0] = nums[0]  
		DP[1] = max(nums[0], nums[1])  
		
c++代码实现如下：
```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int len = nums.size();
        if(len == 0)
            return 0;
        if(len == 1)
            return nums[0];
        if(len == 2)
            return max(nums[0],nums[1]);

        vector<int> DP(len);
        DP[0] = nums[0];
        DP[1] = max(nums[0], nums[1]);

        for(int i = 2; i < len; i++){
            DP[i] = max(DP[i-2]+nums[i], DP[i-1]);
        }

        return DP[len-1];
    }
};
```

### 8.3.2 优化版  
官方解答中的代码写的相当简洁，只用俩临时变量存储当前最大和及前一个最大和的值。  

```c++
int rob(vector<int>& nums) 
{
	int prevMax = 0, currMax = 0;
	for (int x : nums) {
		int temp = currMax;
		currMax = max(prevMax + x, currMax);
		prevMax = temp;
	}
	return currMax;
}
```

  <br/>
  <br/>
  <br/>

***  
<h1 id="1.9"> LeetCode 213 </h1>  [回到目录](#0)  
## 9 [打家劫舍 Ⅱ house robber](https://leetcode-cn.com/problems/house-robber-ii/)

## 9.1 题目描述
你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，这个地方所有的房屋都**围成一圈**，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。  


给定一个代表每个房屋存放金额的非负整数数组，计算你在**不触动警报装置的情况下**，能够偷窃到的最高金额。  


#### 示例 1:
```
输入: [2,3,2]
输出: 3
解释: 你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
```

#### 示例 2:
```
输入: [1,2,3,1]
输出: 4
解释: 你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
     偷窃到的最高金额 = 1 + 3 = 4 。
```  


## 9.2 解题思路  
本题跟上面那题类似，但是多限制条件：这个地方所有的房屋都**围成一圈**, 也就是数组元素形成了环状，然后从这个环形数组中取最大和且所选的这些数据不能相邻。  
1、首先这道题可以用回溯递归的方式解决  
2、动态规划思路，可以从前面的子结构逐步求解，具体解法看下面。  
  
  

## 9.3 解决方案
### 9.3.1 动态规划  
两步走：  
1、状态定义：DP[i]表示到第i个元素时，其前面满足条件的数据元素之和的最大值。   
2、状态转移方程：  
因为数据是环形的了，所以首尾元素不能同时选上，那怎么办呢，这里我们可以类似[198.  打家劫舍](#1.8)求解，分两种情况：  
  - 不选首元素，从第1到len-1下标元素里找出最大和的值，此时初始状态DP[0] = nums[0];DP[1] = max(nums[0], nums[1]);
  - 不选末尾元素，从0到len-2下标元素里找最大和的值，此时初始状态DP[1] = nums[1];DP[2] = max(nums[1], nums[2]);  
  		
最终要求的最大值就在两种情况中选取最大值即可。  

c++代码实现如下：
```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int len = nums.size();
        if(len == 0)
            return 0;
        if(len == 1)
            return nums[0];
        if(len == 2)
            return max(nums[0],nums[1]);

        vector<int> DP(len);
		
		// 不选0号元素，从1...len-1下标中的元素中计算
        DP[1] = nums[1];
        DP[2] = max(nums[1], nums[2]);
        for(int i = 3; i < len; i++){
            DP[i] = max(DP[i-2]+nums[i], DP[i-1]);
        }

		// 不选最后一个元素，从0...len-2下标的元素中计算
        DP[0] = nums[0];
        DP[1] = max(nums[0], nums[1]);
        for(int i = 2; i < len-1; i++){
            DP[i] = max(DP[i-2]+nums[i], DP[i-1]);
        }
		
		// 取两种情况下最大值
        return max(DP[len-1], DP[len-2]);
    }
};
```  
  
  
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.10">LeetCode 121</h1>  [回到目录](#0)  
## 10 [买卖股票的最佳时机 best time to buy and sell stock](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)   
## 10.1 题目描述  
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。  

如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。  

> 注意：你不能在买入股票前卖出股票。  


#### 示例 1:
```
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。  
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。   
```  

#### 示例 2:
```
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```


## 10.2 解题思路  
1、因为只能买卖一次，所以我们可以找出前面的最小值，记录下来，然后让后面的每一个减去这个最小值，取其结果最大的即为所求。    
2、动态规划解法，下面我们就来重点分析下动态规划。   


  
## 10.3 解决方案  

### 10.3.1 
思路1代码实现如下：
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty())
            return 0;

        int min_val = prices[0];
        int max_profit = 0;
        
        for(int i = 1; i < prices.size(); i++){
            max_profit = max(max_profit, prices[i] - min_val);
            min_val = min(min_val, prices[i]);
        }

        return max_profit;
    }
};
```
  
### 10.3.2 动态规划
两步走  
 - 状态定义：DP[i][j]，其中i表示到第i天，第二维的j表示： 0为未买过股票，1为买入了股票但是还未卖，2为买过一次股票，现在卖掉。  
 - 状态转移方程：  
            DP[i][0] = DP[i-1][0];  // 之前未买卖过股票，这次也不买入股票时，收益的最大值  
            DP[i][1] = max(DP[i-1][1], DP[i-1][0]-prices[i]);  // 之前买入了，这次也不卖掉；或者之前没买入，这次买入时，收益最大值   
			DP[i][2] = DP[i-1][1] + prices[i];  // 之前买入的，这次卖掉，收益最大值   
			
  
最后，收益最大值就是所有的DP[i][0]，DP[i][2]中取最大值，因为收益最大，最后股票肯定要卖掉，所以不用考虑 DP[i][1] (这里股票价格为正)。      

  
因为就用了一重循环，所以算法复杂度为O(n)，空间复杂度也是O(n * 3),也即为O(n)。  

c++代码如下：
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int len = prices.size();
        if(len == 0)
            return 0;

        int max_profit = 0;
        vector<vector<int>> DP(len, vector<int>(3));
        DP[0][1] = 0-prices[0]; //买入
        
        for(int i = 1; i < len; i++){
            DP[i][0] = DP[i-1][0];
            DP[i][1] = max(DP[i-1][1], DP[i-1][0]-prices[i]);
            DP[i][2] = DP[i-1][1] + prices[i];
            max_profit = max(max_profit, max(DP[i][0], DP[i][2]));
        }

        return max_profit;
    }
};
```    
  
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.11">LeetCode 122</h1>  [回到目录](#0)  
## 11 [买卖股票的最佳时机Ⅱ best time to buy and sell stock](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)    

## 11.1 题目描述    
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。   
设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。   
  
**注意**：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。  


#### 示例 1:
```
输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
```  

#### 示例 2:
```
输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
```  
#### 示例 3：
```
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```  
#### 提示：
- 1 <= prices.length <= 3 * 10 ^ 4
- 0 <= prices[i] <= 10 ^ 4


## 11.2 解题思路  
本题跟[121.  买卖股票的最佳时机](#1.10)类似，不同之处在于，本题可以交易多次，同样用动态规划解法，下面我们就来重点分析下动态规划。   


  
## 11.3 解决方案  

### 11.3.1 动态规划  
两步走      
1.状态定义：    
 	DP[i][j]，其中i表示到第i天，第二维的j表示： 0表示当前手中没有股票；1表示当前手中有一股，可以卖掉，也可以不卖。      
	
2.状态转移方程：  
DP[i][0]就表示到第i天，手中没有股票时，收益最大值。他可以从以下两个状态转移过来：  
a、DP[i-1][0];  // 前一天手中没有股票，这次也不买入股票时，收益的最大值     
b、DP[i-1][1]+prices[i]; //前一天手中有一股，这次卖掉     
则，DP[i][0]最大值取上面俩最大值。     
  
DP[i][1]表示到第i天，手中有一股时，收益最大值。它可以从以下俩状态转移过来：  
a、DP[i-1][1];  // 前一天手中也有股票，这次也不卖掉  
b、DP[i-1][0]-prices[i]; //前一天手中没有股票，这次买入一股  
同样，DP[i][1]取两种情况的最大值。     
				  
最终的最大利润，就在 DP[len-1][0] 中(因为最后肯定要把股票卖掉，才能利润最大)。        
  
因为就用了一重循环，所以算法复杂度为O(n)，空间复杂度也是O(n * 2),也即为O(n)。    

c++代码如下：
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty())
            return 0;
        
        int len = prices.size();
        vector<vector<int>> DP(len, vector<int>(2));
        DP[0][0] = 0;
        DP[0][1] = -prices[0];

        for(int i = 1; i < len; i++) {
            DP[i][0] = max(DP[i-1][0], DP[i-1][1]+prices[i]);
            DP[i][1] = max(DP[i-1][1], DP[i-1][0]-prices[i]);
        }
        return DP[len-1][0];        
    }
};
```
  
  
  
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.12">LeetCode 123</h1>  [回到目录](#0)  
## 12 [买卖股票的最佳时机Ⅲ best time to buy and sell stock](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)    

## 12.1 题目描述      
给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。    

设计一个算法来计算你所能获取的最大利润。你最多可以完成 `两笔` 交易。   
  
**注意**：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。  


#### 示例 1:
```
输入: [3,3,5,0,0,3,1,4]  
输出: 6  
解释: 在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。  
     随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。  
```  

#### 示例 2:
```
输入: [1,2,3,4,5]  
输出: 4  
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。     
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。     
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。  
```  
#### 示例 3：
```
输入: [7,6,4,3,1] 
输出: 0 
解释: 在这个情况下, 没有交易完成, 所以最大利润为 0。
```    

## 12.2 解题思路  
本题是买卖股票系列的第三题，跟前两题不同之处在于，本题限定**最多只能交易两次**，同样用动态规划解法，本题是LeetCode188题的一个特例版，下面我们就来重点分析下动态规划。   


  
## 12.3 解决方案  

### 12.3.1 动态规划  
我这里用了泛化版的解法，假设最多可以交易count次。  
两步走      
 1.状态定义：    
 	DP[i][j][y]，其中i表示到第i天；第二维j表示到当前天数时，交易了j次；第三维的y表示： 0表示当前手中没有股票；1表示当前手中有一股，可以卖掉，也可以不卖。      
	
 2.状态转移方程：    
 DP[i][j][0]就表示到第i天，已经交易了j次，手中没有股票时，收益最大值。他可以从以下两个状态转移过来：  
 a、DP[i-1][j][0];  // 前一天的时候也已经交易了j次，但是手中没有股票，这次也不买入股票时，收益的最大值     
 b、DP[i-1][j][1]+prices[i]; //前一天的时候也已经交易了j次，但是前一天手中有一股，这次卖掉     
 则，DP[i][j][0]最大值取上面俩最大值。     
  
  
DP[i][j][1]表示到第i天，已经交易了j次，手中有一股时，收益最大值。它可以从以下俩状态转移过来：  
a、DP[i-1][j][1];  // 前一天已经交易了j次，且手中也有股票，这次也不卖掉      
b、DP[i-1][j-1][0]-prices[i]; //前一天已经交易了j-1次，但是手中没有股票了，这次买入一股     
同样，DP[i][j][1]取两种情况的最大值。     
		  
最终的最大利润，就在DP[len-1][j][0] (0<=j<=count,本题count为2) 中(因为最后肯定要把股票卖掉，才能利润最大)。         
  
因为需要两层循环，所以算法复杂度为O(n * count)，空间复杂度也是O(n * count * 2)，也即为O(n * count)。     

c++代码如下：
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty())
            return 0;
        
        int len = prices.size();
        int k = 2; //最多交易k次，本题可将k设置为2
        vector<vector<vector<int>>> max_profit(len, vector<vector<int>>(k+1, vector<int>(2, 0xff000000)));
        max_profit[0][0][0] = 0;
        max_profit[0][1][1] = -prices[0];
        int max_val = 0;

        for(int i = 1; i < len; i++){
            max_profit[i][0][0] = 0;
            for(int j = 1; j <= k; j++){
                max_profit[i][j][0] = max(max_profit[i-1][j][0], max_profit[i-1][j][1]+prices[i]);
                max_profit[i][j][1] = max(max_profit[i-1][j][1], max_profit[i-1][j-1][0]-prices[i]);
            }            
        }
        for(int j = 0; j <= k; j++)
            max_val = max(max_val, max_profit[len-1][j][0]);

        return max_val;
    }
};
```  
  

  
  
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.13">LeetCode 188</h1>  [回到目录](#0)  
## 13 [买卖股票的最佳时机Ⅳ best time to buy and sell stock](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)    

## 13.1 题目描述      
给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。  

设计一个算法来计算你所能获取的最大利润。你最多可以完成 `k` 笔交易。  

  
**注意**：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。  


#### 示例 1:  
```
输入: [2,4,1], k = 2  
输出: 2  
解释: 在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。  
```  

#### 示例 2:  
```
输入: [3,2,6,5,0,3], k = 2  
输出: 7  
解释: 在第 2 天 (股票价格 = 2) 的时候买入，在第 3 天 (股票价格 = 6) 的时候卖出, 这笔交易所能获得利润 = 6-2 = 4 。  
     随后，在第 5 天 (股票价格 = 0) 的时候买入，在第 6 天 (股票价格 = 3) 的时候卖出, 这笔交易所能获得利润 = 3-0 = 3 。  
```    

## 13.2 解题思路   
本题是买卖股票系列的第四题，跟前几题不同之处在于，本题限定**最多只能交易k次**，同样用动态规划解法，下面我们就来重点分析下动态规划。   


  
## 13.3 解决方案  

### 13.3.1 动态规划  
两步走      
 1.状态定义：    
 	DP[i][j][y]，其中i表示到第i天；第二维j表示到当前天数时，交易了j次；第三维的y表示： 0表示当前手中没有股票；1表示当前手中有一股，可以卖掉，也可以不卖。      
	
 2.状态转移方程：    
 DP[i][j][0]就表示到第i天，已经交易了j次，手中没有股票时，收益最大值。他可以从以下两个状态转移过来：  
 a、DP[i-1][j][0];  // 前一天的时候也已经交易了j次，但是手中没有股票，这次也不买入股票时，收益的最大值     
 b、DP[i-1][j][1]+prices[i]; //前一天的时候也已经交易了j次，但是前一天手中有一股，这次卖掉     
 则，DP[i][j][0]最大值取上面俩最大值。     
  
  
DP[i][j][1]表示到第i天，已经交易了j次，手中有一股时，收益最大值。它可以从以下俩状态转移过来：  
a、DP[i-1][j][1];  // 前一天已经交易了j次，且手中也有股票，这次也不卖掉      
b、DP[i-1][j-1][0]-prices[i]; //前一天已经交易了j-1次，但是手中没有股票了，这次买入一股     
同样，DP[i][j][1]取两种情况的最大值。     
		  
最终的最大利润，就在DP[len-1][j][0] (0<=j<=k) 中(因为最后肯定要把股票卖掉，才能利润最大)。         
  
因为需要两层循环，所以算法复杂度为O(n * k)，空间复杂度也是O(n * k)。     

c++代码如下：
```c++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        if(prices.empty() || k <= 0)
            return 0;
        
        int len = prices.size();
        vector<vector<vector<int>>> max_profit(len, vector<vector<int>>(k+1, vector<int>(2, 0xff000000)));
        max_profit[0][0][0] = 0;
        max_profit[0][1][1] = -prices[0];
        int max_val = 0;

        for(int i = 1; i < len; i++){
            max_profit[i][0][0] = 0;
            for(int j = 1; j <= k; j++){
                max_profit[i][j][0] = max(max_profit[i-1][j][0], max_profit[i-1][j][1]+prices[i]);
                max_profit[i][j][1] = max(max_profit[i-1][j][1], max_profit[i-1][j-1][0]-prices[i]);
            }            
        }
        for(int j = 0; j <= k; j++)
            max_val = max(max_val, max_profit[len-1][j][0]);

        return max_val;
    }
};
```  
上面代码提交leetcode时，当k超大时(执行到第209个测试用例时，k=10亿，n=1w)，报*AddressSanitizer: allocator is out of memory trying to allocate 0x59682f018 bytes*错误，所以还需优化。

优化如下：
```c++
int maxProfit(int k, vector<int>& prices) {
	if(prices.empty() || k <= 0) 
		return 0;

	int len = prices.size();
	if(k > (len/2+1)) // 交易次数最多只能为总天数的一半
		k = len/2 + 1;

	vector<vector<vector<int>>> max_profit(len, vector<vector<int>>(k+1, vector<int>(2, 0xff000000)));
	max_profit[0][0][0] = 0;
	max_profit[0][1][1] = -prices[0];
	int max_val = 0;

	for(int i = 1; i < len; i++){
		max_profit[i][0][0] = 0;
		for(int j = 1; j <= k; j++){
			max_profit[i][j][0] = max(max_profit[i-1][j][0], max_profit[i-1][j][1]+prices[i]);
			max_profit[i][j][1] = max(max_profit[i-1][j][1], max_profit[i-1][j-1][0]-prices[i]);
		}            
	}
	for(int j = 0; j <= k; j++)
		max_val = max(max_val, max_profit[len-1][j][0]);

	return max_val;
}
```  
然后拿上面那个测试用例测试(k=10亿，n=1w)，报超出时间限制！我在我自己虚拟机测试了下，跑完用了56s，看来还是要继续优化。仔细再审题发现，其实某一天如果手中有股票的话，可以当天先卖掉再买入的，也就是说k可以大于天数的一半。  
当k>len/2时，此时可以贪心法，只要利润不为负，就可以累加计算。修正之后代码如下，LeetCode提交通过：  

```c++
    int maxProfit(int k, vector<int>& prices) {
        if(prices.empty() || k <= 0)
            return 0;
        
        int len = prices.size();
        int max_val = 0;
        if(k > len/2) {
            for(int i = 1; i < len; i++){
                int tmp_val = prices[i] - prices[i-1];
                if(tmp_val > 0){
                    max_val += tmp_val;
                }
            }
            return max_val;
        }

        // k < (len/2+1)情况        
        vector<vector<vector<int>>> max_profit(len, vector<vector<int>>(k+1, vector<int>(2, 0xff000000)));
        max_profit[0][0][0] = 0;
        max_profit[0][1][1] = -prices[0];

        for(int i = 1; i < len; i++){
            max_profit[i][0][0] = 0;
            for(int j = 1; j <= k; j++){
                max_profit[i][j][0] = max(max_profit[i-1][j][0], max_profit[i-1][j][1]+prices[i]);
                max_profit[i][j][1] = max(max_profit[i-1][j][1], max_profit[i-1][j-1][0]-prices[i]);
            }            
        }
        for(int j = 0; j <= k; j++)
            max_val = max(max_val, max_profit[len-1][j][0]);

        return max_val;
    }
```

[题解中](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/solution/c-zhuang-tai-ya-suo-dong-tai-gui-hua-by-da-li-wa-4/)有个优化点，采用k倒序来压缩状态，在时间和空间复杂度上都降低了不少，比较优秀，代码贴出来学习下：  
```c++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        if(k<=0 || prices.empty())
            return 0;
        
        int len = prices.size();
        int max_val = 0;
        if(k > len/2) {
            for(int i = 1; i < len; i++){
                int tmp_val = prices[i] - prices[i-1];
                if(tmp_val > 0){
                    max_val += tmp_val;
                }
            }
            return max_val;
        }
        
        vector<vector<int> > dp(k + 1, vector<int>{0, INT_MIN});
        for (int i = 0; i < len; ++i) {
            for (int j = k; j > 0; --j) {
                dp[j][0] = max(dp[j][0], dp[j][1] + prices[i]);
                dp[j][1] = max(dp[j][1], dp[j - 1][0] - prices[i]);
            }
        }
        return dp[k][0];
    }
};
```

  
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.14">LeetCode 309</h1>  [回到目录](#0)  
## 14 [最佳买卖股票时机含冷冻期 best time to buy and sell stock with cooldown](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)    

## 14.1 题目描述      
给定一个整数数组，其中第 i 个元素代表了第 i 天的股票价格 。  

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:  
- 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。  
- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。  
  
  
#### 示例:  
```
输入: [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]  
```   

## 14.2 解题思路   
本题是买卖股票系列的第五题，跟前几题不同之处在于，本题限定**冷冻期为 1 天**，同样用动态规划解法，下面我们就来重点分析下动态规划。   


  
## 14.3 解决方案  

### 14.3.1 动态规划  
两步走       
 1.状态定义：    
 	max_profit[i][j]，其中i表示到第i天；第二维的j表示： 0表示当前手中没有股票；1表示当前手中有一股，可以卖掉，也可以不卖。       
	
 2.状态转移方程：     
max_profit[i][0]表示到第i天，手中没有股票时，当前的最大收益。它可以从下面两个状态转移过来：    
	 - max_profit[i-1][0] 表示前一天手中也没有股票，第i天的时候保持不变（不买入）  
	 - max_profit[i-1][1] + prices[i] 表示前一天手中有股票，第i天卖出  
max_profit[i][0]最大值取两者最大值即可。  

max_profit[i][1]表示到第i天，手中有一股时，当前最大收益值。它可以从下面俩状态转移过来：  
	 - max_profit[i-1][1] 表示前一天手中有一股，第i天时保持不变（不卖出）  
	 - max_profit[i-2][0] - prices[i] 表示到大前天时手中没有股票，第i天买入一股   
max_profit[i][1]最大值取两者最大值即可。
	 
		  
最终的最大利润，就在DP[len-1][0] 中(因为最后肯定要把股票卖掉，才能利润最大)。          
  
因为只需一层循环，所以算法复杂度为O(n)，空间复杂度为O(n * 2)。     

c++代码如下：
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {        
        int len = prices.size();
        if(len <= 1)
            return 0;
        
        vector<vector<int>> max_profit(len, vector<int>(2));
        max_profit[0][1] = -prices[0];
        max_profit[1][0] = max(max_profit[0][1]+prices[1], max_profit[0][0]);
        max_profit[1][1] = max(max_profit[0][1], max_profit[0][0]-prices[1]);

        for(int i = 2; i < len; i++) {
            max_profit[i][0] = max(max_profit[i-1][0], max_profit[i-1][1] + prices[i]);
            max_profit[i][1] = max(max_profit[i-1][1], max_profit[i-2][0] - prices[i]);
        }
        return max_profit[len-1][0];
    }
};
```   

  
  
  
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.15">LeetCode 714</h1>  [回到目录](#0)  
## 15 [买卖股票的最佳时机含手续费 best time to buy and sell stock with transaction fee](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)    

## 15.1 题目描述      
给定一个整数数组 `prices`，其中第 `i` 个元素代表了第 `i` 天的股票价格 ；非负整数 `fee` 代表了交易股票的手续费用。  
  
你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。  

返回获得利润的最大值。  

**注意**：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。
  
  
#### 示例:  
```
输入: prices = [1, 3, 2, 8, 4, 9], fee = 2
输出: 8
解释: 能够达到的最大利润:  
在此处买入 prices[0] = 1
在此处卖出 prices[3] = 8
在此处买入 prices[4] = 4
在此处卖出 prices[5] = 9
总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```     

**注意**:    
	- 0 < prices.length <= 50000.   
	- 0 < prices[i] < 50000.   
	- 0 <= fee < 50000.   

## 15.2 解题思路    
本题是买卖股票系列的第六题，本题跟[LeetCode 122](#1.11)基本一样的，就是多了个交易费，每笔交易要付费(买入卖出算一次交易)，同样用动态规划解法。   


  
## 15.3 解决方案  

### 15.3.1 动态规划    
两步走      
1.状态定义：    
 	DP[i][j]，其中i表示到第i天，第二维的j表示： 0表示当前手中没有股票；1表示当前手中有一股，可以卖掉，也可以不卖。      
	
2.状态转移方程：  
DP[i][0]就表示到第i天，手中没有股票时，收益最大值。他可以从以下两个状态转移过来：  
a、DP[i-1][0];  // 前一天手中没有股票，这次也不买入股票时，收益的最大值     
b、DP[i-1][1]+prices[i]-fee; //前一天手中有一股，这次卖掉，我们在这里减去交易费，买入的地方就不必减了    
则，DP[i][0]最大值取上面俩最大值。     
  
DP[i][1]表示到第i天，手中有一股时，收益最大值。它可以从以下俩状态转移过来：  
a、DP[i-1][1];  // 前一天手中也有股票，这次也不卖掉  
b、DP[i-1][0]-prices[i]; //前一天手中没有股票，这次买入一股  
同样，DP[i][1]取两种情况的最大值。     
				  
最终的最大利润，就在 DP[len-1][0] 中(因为最后肯定要把股票卖掉，才能利润最大)。        
  
因为就用了一重循环，所以算法复杂度为O(n)，空间复杂度也是O(n * 2),也即为O(n)。  

c++代码如下：
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        if(prices.empty())
            return 0;
        
        int len = prices.size();
        vector<vector<int>> DP(len, vector<int>(2));
        DP[0][0] = 0;
        DP[0][1] = -prices[0];

        for(int i = 1; i < len; i++) {
            DP[i][0] = max(DP[i-1][0], DP[i-1][1]+prices[i]-fee);
            DP[i][1] = max(DP[i-1][1], DP[i-1][0]-prices[i]);
        }
        return DP[len-1][0];
    }
};
```     
  
   
  
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.16">LeetCode 72</h1>  [回到目录](#0)  
## 16 [编辑距离 edit-distance](https://leetcode-cn.com/problems/edit-distance/)    

## 16.1 题目描述      
给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。  

你可以对一个单词进行如下三种操作：  

 - 插入一个字符  
 - 删除一个字符  
 - 替换一个字符  
  
  
#### 示例 1:  
```
输入：word1 = "horse", word2 = "ros"  
输出：3  
解释：  
horse -> rorse (将 'h' 替换为 'r')  
rorse -> rose (删除 'r')  
rose -> ros (删除 'e')  
```  

#### 示例 2：  
```
输入：word1 = "intention", word2 = "execution"  
输出：5  
解释：  
intention -> inention (删除 't')  
inention -> enention (将 'i' 替换为 'e')  
enention -> exention (将 'n' 替换为 'x')  
exention -> exection (将 'n' 替换为 'c')  
exection -> execution (插入 'u')  
```  

## 16.2  解决思路
本题看完后，感觉无从下手，但是dp专题的话，肯定是往动态规划上想，但是仍不得其法，后来还是看了题解，具体解法看下节。

## 16.3 解法   
看了官方解答的视频，讲的很好，很清晰。  

### 16.3.1 动态规划  
  
  
首先，定义状态： DP[i][j]，它表示从word1前i个字符变换成word2的前j个字符，所需的最少操作次数。  
然后就是状态转移方程：   
 若word1[i-1] == word2[j-1]，则DP[i][j]取DP[i-1][j-1]，无需操作。  
 否则的话，有三种情况：  
 - 增加，即针对word1中前i个字符增加一个字符就能变成word2中前j个字符，也就相当于将word2的第j个字符删掉后，两个子串就相同了，则DP[i][j] = DP[i][j-1] + 1  
 - 删除，即针对word1中前i个字符删除掉一个字符就能变成word2中前j个字符，则DP[i][j] = DP[i-1][j] + 1  
 - 修改，即针对word1中前i个字符删修改一个字符就能变成word2中前j个字符，相当于分别把这两子串的最后一个字符删掉后，就相同了，则DP[i][j] = DP[i-1][j-1] + 1    
   
  
  DP[i][j]取上面三种情况下的最小值即可。  

注：要考虑到 word1 或 word2 一个字母都没有，即全增加/删除的情况，所以预留 DP[0][j] 和 DP[i][0]。    
 
  
  
```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int row_len = word1.size()+1;
        int col_len = word2.size()+1;
        if(row_len== 0 || col_len == 0)
            return 0;
        
        vector<vector<int>> DP(row_len, vector<int>(col_len));
        for (int i = 0; i < row_len; i++) //初始化
            DP[i][0] = i;
        
        for (int j = 0; j < col_len; j++)
            DP[0][j] = j;

        for (int i = 1; i < row_len; i++) {
            for (int j = 1; j < col_len; j++) {
                DP[i][j] = min(DP[i-1][j-1], min(DP[i-1][j], DP[i][j-1])) + 1;

                if (word1[i-1] == word2[j-1])
                    DP[i][j] = min(DP[i][j], DP[i-1][j-1]);
            }
        }
        return DP[row_len-1][col_len-1];
    }
};
```  
或者按官方写法：
```c++
int minDistance(string word1, string word2) {
	int row_len = word1.size()+1;
	int col_len = word2.size()+1;
	if(row_len==0 || col_len ==0)
		return 0;

	vector<vector<int>> DP(row_len, vector<int>(col_len));
	for (int i = 0; i < row_len; i++) //初始化
		DP[i][0] = i;

	for (int j = 0; j < col_len; j++)
		DP[0][j] = j;

	// 计算所有 DP 值
	for (int i = 1; i < row_len; i++) {
		for (int j = 1; j < col_len; j++) {
			int left = DP[i-1][j] + 1;
			int down = DP[i][j-1] + 1;
			int left_down = DP[i-1][j-1];
			if (word1[i-1] != word2[j-1])
				left_down += 1;

			DP[i][j] = min(left, min(down, left_down));
		}
	}
	return DP[row_len-1][col_len-1];
}
```  
代码中可以看出，有两层循环来遍历两个字符串，所以时间复杂度就是O(col_len * row_len)；空间复杂度方面，因为申请了个二维数组，所以空间复杂度也是O(col_len * row_len)。  

### 16.3.2 递归法  
补上递归解法代码：  
```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
      int len1 = word1.length();
      int len2 = word2.length();

      if(len1==0 || len2==0)
        return max(len1, len2);

      if(word1[len1-1] == word2[len2-1])
        return minDistance(word1.substr(0, len1-1), word2.substr(0, len2-1));

      return min(min(minDistance(word1, word2.substr(0, len2-1)), minDistance(word1.substr(0, len1-1), word2)), 
        minDistance(word1.substr(0, len1-1), word2.substr(0, len2-1)))+1;
    }
};
```
动态规划解法是对递归解法的优化，因为递归中，有大量重复的计算。     
  
  
     
  
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.17">LeetCode 44</h1>  [回到目录](#0)  
## 17 [通配符匹配 wildcard matching](https://leetcode-cn.com/problems/wildcard-matching/)    

## 17.1 题目描述     
给定一个字符串 (`s`) 和一个字符模式 (`p`) ，实现一个支持 `'?'` 和 `'*'` 的通配符匹配。   
> `'?'` 可以匹配任何单个字符。
> `'*'` 可以匹配任意字符串（包括空字符串）。  
两个字符串**完全匹配**才算匹配成功。  
  
#### 说明：
- `s` 可能为空，且只包含从 `a-z` 的小写字母。    
- `p` 可能为空，且只包含从 `a-z` 的小写字母，以及字符 `?` 和 `*`。   

#### 示例 1：
```
输入:
s = "aa"
p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。
```  

#### 示例 2：
```
输入:
s = "aa"
p = "*"
输出: true
解释: '*' 可以匹配任意字符串。
```  

#### 示例 3：
```
输入:
s = "cb"
p = "?a"
输出: false
解释: '?' 可以匹配 'c', 但第二个 'a' 无法匹配 'b'。
```    
  

#### 示例 4：
```
输入:
s = "adceb"
p = "*a*b"
输出: true
解释: 第一个 '*' 可以匹配空字符串, 第二个 '*' 可以匹配字符串 "dce".
```    
   
  
### 17.2 解题思路  
- 递归
- 迭代
- 动态规划

### 17.3 解决方案  

#### 17.3.1 迭代法
```c
bool isMatch(char * s, char * p){
	if(s==NULL || p==NULL)
		return false;

	int sLen = strlen(s), pLen = strlen(p);
	int sIdx = 0, pIdx = 0;
	int starIdx = -1, sTmpIdx = -1;

	while (sIdx < sLen) {
	  if (pIdx < pLen && (p[pIdx] == '?' || p[pIdx] == s[sIdx])){
		++sIdx;
		++pIdx;
	  }

	  // If pattern character = '*'
	  else if (pIdx < pLen && p[pIdx] == '*') {          
		starIdx = pIdx;
		sTmpIdx = sIdx;
		++pIdx;
	  }      
	  else if (starIdx == -1) {
		return false;
	  }
	  else {
		pIdx = starIdx + 1;
		sIdx = sTmpIdx + 1;
		sTmpIdx = sIdx;
	  }
	}

	for(int i = pIdx; i < pLen; i++)
	  if (p[i] != '*') 
		return false;

	return true;
}
```   

#### 17.3.2 动态规划
这里看到题解中有个[解答](https://leetcode-cn.com/problems/wildcard-matching/solution/tong-su-xiang-xi-shuang-zhi-zhen-he-dong-tai-gui-h/)很不错: 
定义状态：opt[i][j],表示字符串s[i]和模式串p[j]是否匹配。  
假设s长度为n，模式串长度为m，则结束条件及递推关系如下：  
opt[i][j] = true // i==n && j==m    
opt[i][j] = false // i!=n && j==m    
opt[i][j] = is_all_star_or_not(p) // i==n && j!=m  
opt[i][j] = opt[i+1][j+1] // i!=n && j!=m && (s[i]==p[j] || p[j]=='?')  
opt[i][j] = opt[i+1][j] || opt[i][j+1] // i!=n && j!=m && p[j]=='*'  
opt[i][j] = false // otherwise   

动态规划自顶向下代码（带memo优化），我把原代码改成了纯cpp：    
```c++
bool isMatch(string s, string p) {
	vector<vector<char>> memo(s.size()+1, vector<char>(p.size()+1, -1));
	return do_match(s, p, 0, 0, memo);
}

bool do_match(string &s, string &p, int i, int j, vector<vector<char>>& memo) {
	if (memo[i][j] != -1) 
		return memo[i][j];

	if (i == s.size() && j == p.size()) 
		return memo[i][j] = true;

	if (i != s.size() && j == p.size()) 
		return memo[i][j] = false;

	// is_all_star_or_not(p[j, m))
	if (i == s.size() && j != p.size()) {
		int temp = j;
		while (p[temp] == '*') 
			++temp;

		return memo[i][j] = (temp == p.size());
	}

	if (s[i] == p[j] || p[j] == '?') 
		return memo[i][j] = do_match(s, p, i+1, j+1, memo);
	else if (p[j] == '*')
		return memo[i][j] = (do_match(s, p, i+1, j, memo) || do_match(s, p, i, j+1, memo));
	else 
		return memo[i][j] = false;
}
```  

自底向上动态规划：  
```c++
bool isMatch(string s, string p) {
	int n = s.size(), m = p.size();
	bool dp[m + 1];
	dp[m] = true;
	for (int j = m - 1; j >= 0; j--) {
		dp[j] = (p[j]=='*'?dp[j+1]:false);
	}

	for (int i = n - 1; i >= 0; --i) {
		bool oldright = dp[m];
		for (int j = m - 1; j >= 0; --j) {
			if (s[i] == p[j] || p[j] == '?') {
				int temp = dp[j];
				dp[j] = oldright;
				oldright = temp;
			}
			else if (p[j] == '*') {
				int temp = dp[j];
				dp[j] = dp[j] || dp[j+1];
				oldright = temp;
			}
			else oldright = dp[j], dp[j] = false;
		}
		dp[m] = false;
	}
	return dp[0];
}
```    

  
    
  <br/>
  <br/>
  <br/>
  
***
<h1 id="1.18">LeetCode 10</h1>  [回到目录](#0)   
## 18 [正则表达式匹配 regular expression matching](https://leetcode-cn.com/problems/regular-expression-matching/)    

## 18.1 题目描述     
给定一个字符串 `s` 和一个字符规律 `p` ，请你来实现一个支持 `'.'` 和 `'*'` 的正则表达式匹配。   
> `'.'` 匹配任意单个字符。
> `'*'` 匹配零个或者多个前面的那一个元素。  
所谓匹配，要是涵盖**整个**字符串`s`的，而不是部分字符串。  
  
#### 说明：
- `s` 可能为空，且只包含从 `a-z` 的小写字母。    
- `p` 可能为空，且只包含从 `a-z` 的小写字母，以及字符 `.` 和 `*`。   

#### 示例 1：
```
输入:
s = "aa"
p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。
```    
#### 示例 2：
```
输入:
s = "aa"
p = "a*"
输出: true
解释: 因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
```    
#### 示例 3：
```
输入:
s = "ab"
p = ".*"
输出: true
解释: ".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。  
```    
#### 示例 4：
```
输入:
s = "aab"
p = "c*a*b"
输出: true
解释: 因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。  
```    

## 18.2 解决方法

### 18.2.1 动态规划  
该解法[在此](https://leetcode.com/problems/regular-expression-matching/discuss/5684/C%2B%2B-O(n)-space-DP)，分两步走：  
  
定义状态： DP[i][j]，表示s[0..i]跟p[0..j]是否匹配，匹配则为true，否则为false。    
  
状态转移方程：  
 - dp[i][j] = dp[i-1][j-1]   //s[i-1] == p[j-1] 或者 p[j-1] == '.')   
 - dp[i][j] = dp[i][j-2]     //p[j-1] == '*' 且该星号之前没匹配过    
 - dp[i][j] = dp[i-1][j] 且 s[i-1] == p[j-2] 或者 p[j-2] == '.' 有一个成立   //p[j-1] == '*' 且至少已经匹配了一次     
	
代码如下：  
```c++
bool isMatch(string s, string p) {
    int m = s.size(), n = p.size();
    vector<vector<bool>> DP(m + 1, vector<bool>(n + 1, false));
    DP[0][0] = true;
    for (int i = 0; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (p[j - 1] == '*') {
                DP[i][j] = DP[i][j - 2] || (i && DP[i - 1][j] && (s[i - 1] == p[j - 2] || p[j - 2] == '.'));
            } else {
                DP[i][j] = i && DP[i - 1][j - 1] && (s[i - 1] == p[j - 1] || p[j - 1] == '.');
            }
        }
    }
    return DP[m][n];
}
```


优化空间，只用一维数组即可，代码如下：  

```c++
bool isMatch(string s, string p) {
    int m = s.size(), n = p.size();
    vector<bool> DP(n + 1, false);
    for (int i = 0; i <= m; i++) {
        bool pre = DP[0];
        DP[0] = !i;
        for (int j = 1; j <= n; j++) {
            bool temp = DP[j];
            if (p[j - 1] == '*') {
                DP[j] = DP[j - 2] || (i && DP[j] && (s[i - 1] == p[j - 2] || p[j - 2] == '.'));
            } else {
                DP[j] = i && pre && (s[i - 1] == p[j - 1] || p[j - 1] == '.');
            }
            pre = temp;
        }
    }
    return DP[n];
}
```   
  
  
  
### 18.2.2 迭代法  
[题解](https://leetcode.com/problems/regular-expression-matching/discuss/5659/The-shortest-AC-code.)中看到一个很精简的代码：    
```c
bool isMatch(const char *s, const char *p) {
    for(char c = *p; c != 0; ++s, c = *p) {
        if(*(p+1) != '*')
            p++;
        else if(isMatch(s, p+2))
            return true;
        if((*s==0) || ((c!='.') && (c!=*s)))
            return false;
    }
    return *s == 0;
}
```    


 

  <br/>
  <br/>
  <br/>
  <br/>
  
<h1 id="2"> 区间DP </h1>  [回到目录](#0)  
  
      
  <br/>
  <br/>
  
***
<h1 id="2.1">LeetCode 516</h1>  [回到目录](#0)    
## 1 [最长回文子序列 longest palindromic subsequence](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)      

## 1.1 题目描述      
给定一个字符串`s`，找到其中最长的回文子序列。可以假设`s`的最大长度为`1000`。   

#### 示例 1：  
```  
输入:  
"bbbab"  

输出:  
4  
一个可能的最长回文子序列为 "bbbb"。  
```  
#### 示例 2：  
```   
输入:  
"cbbd"  

输出:  
2  
一个可能的最长回文子序列为 "bb"。   
```    

## 1.2 解决方案  
- 递归
- 动态规划


### 1.2.1 递归法

```c++
class Solution {
public:
    int do_lpss(string s, int i, int j, vector<vector<int>>& memo) {
        if (memo[i][j] >= 0)
            return memo[i][j];
        
        if (i > j)      
			return 0;
			
        if (i == j)     
			return 1;
        
        if (s[i] == s[j])
            memo[i][j] = do_lpss(s, i+1, j-1, memo) + 2;
        else
            memo[i][j] = max(do_lpss(s, i+1, j, memo), do_lpss(s, i, j-1, memo));
        
        return memo[i][j];
    }
	
    int longestPalindromeSubseq(string s) {
        int s_len = s.length();
        if(s_len <= 1)
            return s_len;

        vector<vector<int>> memo(s_len, vector<int>(s_len, -1));
        return do_lpss(s, 0, s_len-1, memo);
    }
};
```  

### 1.2.1 动态规划    
其实就是对上面递归的优化，这里分两步走：  
 - 定义状态： dp[i][j], 表示该字符串s的下标i到j直接最长回文子序列的最长值
 - 状态转移： s[i]==s[j]时，dp[i][j] = dp[i+1][j-1] + 2  
 		  否则的话，dp[i][j] = max(dp[i+1][j], dp[i][j-1])  
  
c++代码实现如下：  
```c++
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        int s_len = s.length();
        if(s_len <= 1)
            return s_len;
        
        vector<vector<int>> dp(s_len, vector<int>(s_len));
        for (int i = s_len - 1; i >= 0; i--) {
            dp[i][i] = 1;
            for (int j = i+1; j < s_len; j++) {
                if (s[i] == s[j]) {
                    dp[i][j] = dp[i+1][j-1] + 2;
                } else {
                    dp[i][j] = max(dp[i+1][j], dp[i][j-1]);
                }
            }
        }
        return dp[0][s_len-1];
    }
};
```





  <br/>    
  
      
  <br/>
  <br/>
  
***
<h1 id="2.2">LeetCode 730</h1>  [回到目录](#0)    
## 2 [统计不同回文子字符串 count different palindromic subsequences](https://leetcode-cn.com/problems/count-different-palindromic-subsequences/)      

## 2.1 题目描述      
给定一个字符串 `S`，找出 `S` 中不同的非空回文子序列个数，并返回该数字与 `10^9 + 7` 的模。  

通过从 `S` 中删除 `0` 个或多个字符来获得子字符序列。  

如果一个字符序列与它反转后的字符序列一致，那么它是回文字符序列。  

如果对于某个 `i`，`A_i != B_i`，那么 `A_1, A_2, ...` 和 `B_1, B_2, ...` 这两个字符序列是不同的。  

#### 示例 1：  
```  
输入：
S = 'bccb'
输出：6
解释：
6 个不同的非空回文子字符序列分别为：'b', 'c', 'bb', 'cc', 'bcb', 'bccb'。
注意：'bcb' 虽然出现两次但仅计数一次。  
```  
#### 示例 2：  
```   
输入：
S = 'abcdabcdabcdabcdabcdabcdabcdabcddcbadcbadcbadcbadcbadcbadcbadcba'
输出：104860361
解释：
共有 3104860382 个不同的非空回文子字符序列，对 10^9 + 7 取模为 104860361。   
```    

#### 提示  
- 字符串 `S` 的长度将在`[1, 1000]`范围内。  
- 每个字符 `S[i]` 将会是集合 `{'a', 'b', 'c', 'd'}` 中的某一个。  


## 2.2 解决方案  
- 动态规划


### 2.2.1 动态规划  
本题参考[这里](https://leetcode.com/problems/count-different-palindromic-subsequences/discuss/246186/C%2B%2B-neat)，没看太懂，以后有空慢慢消化，先加上了，c++代码如下：  
```c++
int countPalindromicSubsequences(string S) {
    int s_len = S.size();
	if(s_len == 0)
		return 0;

    vector<vector<int>> DP(s_len, vector<int>(s_len, 0));
    for (int i = 0; i < s_len; ++i)  // initial
        DP[i][i] = 1;
	
    long mod = 1000000007;
    for (int gap = 1; gap < s_len; ++gap) {
        for (int i = 0, j = i+gap; j < s_len; ++i, ++j) {           
            if (S[i] != S[j]) {
                DP[i][j] = DP[i+1][j] + DP[i][j-1] - DP[i+1][j-1];
            } else {
                DP[i][j] = 2 * DP[i+1][j-1];
                
                int l = i+1, r = j-1;
                while(l <= r && S[l] != S[i]) 
                    l++;

                while(l <= r && S[r] != S[i]) 
                    r--;
                
                if (l < r)
                    DP[i][j] -= DP[l+1][r-1];  
                else if (l == r)
                    DP[i][j] += 1;
                else if (l > r)
                    DP[i][j] += 2;
            }
            DP[i][j] = (DP[i][j]+mod) % mod;
        }
    }
    
    return DP[0][s_len-1];
}
```  
  
  
  <br/>    
  
      
  <br/>
  <br/>
  
***
<h1 id="2.3">LeetCode 1039</h1>  [回到目录](#0)    
## 3 [多边形三角剖分的最低得分 minimum score triangulation of polygon](https://leetcode-cn.com/problems/minimum-score-triangulation-of-polygon/)      

## 3.1 题目描述      
给定 `N`，想象一个凸 `N` 边多边形，其顶点按顺时针顺序依次标记为 `A[0], A[i], ..., A[N-1]`。

假设您将多边形剖分为 `N-2` 个三角形。对于每个三角形，该三角形的值是顶点标记的乘积，三角剖分的分数是进行三角剖分后所有 `N-2` 个三角形的值之和。  

返回多边形进行三角剖分后可以得到的最低分。  

#### 示例 1：
```
输入：[1,2,3]  
输出：6   
解释：多边形已经三角化，唯一三角形的分数为 6。  
```  

#### 示例 2：
```
输入：[3,7,4,5]
输出：144
解释：有两种三角剖分，可能得分分别为：3*7*5 + 4*5*7 = 245，或 3*4*5 + 3*4*7 = 144。最低分数为 144。
```    

#### 示例 3：
```
输入：[1,3,1,4,1,5]
输出：13
解释：最低分数三角剖分的得分情况为 1*1*3 + 1*1*4 + 1*1*5 + 1*1*1 = 13。
```  

#### 提示：
 - 3 <= A.length <= 50  
 - 1 <= A[i] <= 100   
 
 
## 3.2 解决方法
 
### 3.2.1 动态规划     
定义状态：dp[i][j], 表示从i到j序列的最低分。假设以从顶点i到j的`底边`的三角形的`顶点`为m，则三角形imj将多边形分成三部分，总分即为三部分的分数和。  
  
状态转移方程为：    
dp[i][j]=min(dp[i][m]+A[i]*A[j]*A[m]+dp[m][j]), m ∈ [i+1,j-1]      

```c++
int minScoreTriangulation(vector<int>& A) {
    int len = A.size();
    vector<vector<int>> socres(len, vector<int>(len));
    
    for (int i = len-1; i >= 0; i--) {
        for (int j = i+1; j < len; j++) {
            for (int k = i+1; k < j; k++) { //k ∈[i+1, j-1]
                int tmp_score = A[i]*A[j]*A[k] + socres[i][k] + socres[k][j];
                socres[i][j] = (socres[i][j]!=0) ? min(socres[i][j], tmp_score) : tmp_score;
            }
        }
    }
    return socres[0][len-1];
}
```  
代码中可以看出，三层循环，所以时间复杂度： O（n³），空间复杂度O（n²）。   

  
  
  <br/>    
  
      
  <br/>
  <br/>
  
***
<h1 id="2.4">LeetCode 664</h1>  [回到目录](#0)    
## 4 [奇怪的打印机 strange printer](https://leetcode-cn.com/problems/strange-printer/)       

## 4.1 题目描述      
有台奇怪的打印机有以下两个特殊要求：   
	1. 打印机每次只能打印同一个字符序列。    
	2. 每次可以在任意起始和结束位置打印新字符，并且会覆盖掉原来已有的字符。    
	
给定一个只包含小写英文字母的字符串，你的任务是计算这个打印机打印它需要的最少次数。    

#### 示例 1：
```
输入: "aaabbb"
输出: 2
解释: 首先打印 "aaa" 然后打印 "bbb"。 
```  

#### 示例 2：
```
输入: "aba"
输出: 2
解释: 首先打印 "aaa" 然后在第二个位置打印 "b" 覆盖掉原来的字符 'a'。
```    

#### 提示：  
 - 输入字符串的长度不会超过 100。    
  
 
## 4.2 解决方法  
 
### 4.2.1 动态规划     
定义状态：min_count[i][j], 表示从下标i到j字符，打印机要打印的最少次数。    
  
状态转移方程为：     
 - 当s[i]==s[i+1]时， min_count[i][i+1]=1    
 - 当s[i]!=s[i+1]时， min_count[i][i+1]=2  

那么类似的，我们对于s从下标i到j，假设中间有个第k个字符(i < k < j), 有如下转移方程：  
 - 当s[k]==s[i]时，`min_count[j][i]` 就为 `min_count[j][k] + min_count[k+1][i] - 1` 中最小的；    
 - 当s[k]!=s[i]时，`min_count[j][i]` 就为 `min_count[j][k] + min_count[k+1][i]` 中最小的；    

完整cpp代码如下：  
```c++
int strangePrinter(string s) {
    if(s.empty())
        return 0;

    int len = s.size();
    vector<vector<int>> min_count(len, vector<int>(len, len));
    for(int i = 0; i < len; i++) {
        min_count[i][i] = 1;
    }

    for(int i = 0; i < len; i++) {
        for(int j = i; j >= 0; j--) {
            for(int k = j; k < i; k++) {
                if(s[k] == s[i]) 
                    min_count[j][i] = min(min_count[j][i], min_count[j][k]+min_count[k+1][i]-1);
                else 
                    min_count[j][i] = min(min_count[j][i], min_count[j][k]+min_count[k+1][i]);
            }
        }
    }
    return min_count[0][len-1];
}
```  
代码中可以看出，三层循环，所以时间复杂度： O（n³），空间复杂度O（n²）。     

  
  
  
   
  
  <br/>    
  
      
  <br/>
  <br/>
  
***
<h1 id="2.5">LeetCode 312</h1>  [回到目录](#0)    
## 5 [戳气球 burst balloons](https://leetcode-cn.com/problems/burst-balloons/)       

## 5.1 题目描述      
有 `n` 个气球，编号为 `0` 到 `n-1`，每个气球上都标有一个数字，这些数字存在数组 `nums` 中。  

现在要求你戳破所有的气球。每当你戳破一个气球 i 时，你可以获得 `nums[left] * nums[i] * nums[right]` 个硬币。 这里的 `left` 和 `right` 代表和 `i` 相邻的两个气球的序号。注意当你戳破了气球 `i` 后，气球 `left` 和气球 `right` 就变成了相邻的气球。

求所能获得硬币的最大数量。  

#### 说明 ：
 - 你可以假设 `nums[-1] = nums[n] = 1`，但注意它们不是真实存在的所以并不能被戳破。  
 - 0 ≤ `n` ≤ 500, 0 ≤ `nums[i]` ≤ 100

#### 示例 ：
```
输入: [3,1,5,8]
输出: 167 
解释: nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
     coins =  3*1*5      +  3*5*8    +  1*3*8      + 1*8*1   = 167
```    
 
## 5.2 解决方法    
题目意思还是比较好理解，最简单最直接就是暴力穷举法，然后找出最大值，但是时间复杂度非常巨大(为O(n!))，一般不可接受。  
因为暴力枚举法包含了大量的重复计算，所以可以做优化，一种是分治思想，加上缓存计算；还有就是动态规划。  
 
### 5.2.1 递归+缓存优化   
仔细揣摩就会发现，如果第k个气球被刺破之后，问题就变成了：求k左边部分能获取的最大金币数，加上k的右边部分能获取的最大金币数的和，这两个子问题去求解。这种分治思想很适合用递归来实现。  

完整cpp代码如下：  
```c++
int maxCoins(vector<int>& nums) {
    int len = nums.size();
    vector<int> ex_nums(len+2, 1);
    int i = 1;
    for(int x : nums) {
        if(x > 0)
            ex_nums[i++] = x;
    }
    int real_len = i + 1; 

    vector<vector<int>> memo(real_len, vector<int>(real_len));
    return burst(memo, ex_nums, 0, real_len-1);
}

int burst(vector<vector<int>>& memo, vector<int>& nums, int left, int right) {
    if (left+1 == right) 
        return 0;
    
    if (memo[left][right] > 0) 
        return memo[left][right];
    
    int ans = 0;
    for (int i = left + 1; i < right; ++i) {
        ans = max(ans, nums[left]*nums[i]*nums[right] + 
        	burst(memo, nums, left, i) + burst(memo, nums, i, right));
    }
	
    memo[left][right] = ans;
    return ans;
}
```  
代码中可以看出，三层循环，所以时间复杂度： O（n³），空间复杂度O（n²）。   
   
   
### 5.2.2 动态规划     
两步走：    
定义状态dp[i][j]，表示从第i个气球到第j个气球之间(不包含i，j)，能获取的最大金币数。  
状态转移如下：    
假设i，j中间有个k，则k把i，j分为左右两部分，左边是（i，k]，右边是[k，j），则
dp[i][j] = max{nums[i]*nums[k]*nums[j] + dp[i][k] + dp[k][j]}    
    
若i+1=j，即i，j是邻接的时候，dp[i][j] = 0.    
  
  c++代码如下：
 ```c++
 int maxCoins(vector<int>& nums) {
    int len = nums.size();
    vector<int> ext_nums(len+2);
    
    int i = 1;
    for (int x : nums) {
        if (x > 0) 
            ext_nums[i++] = x;
    }
    ext_nums[0] = ext_nums[i++] = 1;


    vector<vector<int>> dp(i, vector<int>(i));
    for (int k = 2; k < i; ++k) {
        for (int left = 0; left < i-k; ++left) {
            int right = left + k;
            for (int i = left + 1; i < right; ++i)
                dp[left][right] = max(dp[left][right],
                    ext_nums[left] * ext_nums[i] * ext_nums[right] + dp[left][i] + dp[i][right]);
        }
    }

    return dp[0][i - 1];
}
 ```  


  
  
  
  



  <br/>
  <br/>
  <br/>
  <br/>
  
<h1 id="3"> 背包DP </h1>  [回到目录](#0)  
  
  
  <br/>  
  
  
 

  <br/>
  <br/>
  <br/>
  <br/>
  
<h1 id="4"> 树形DP </h1>  [回到目录](#0)  
  
  
  <br/>  
  
  
<h1 id="4.1"> LeetCode 337 </h1>  [回到目录](#0)  
## 1 [打家劫舍 Ⅲ house robber](https://leetcode-cn.com/problems/house-robber-iii/)

## 1.1 题目描述
在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果**两个直接相连**的房子在同一天晚上被打劫，房屋将自动报警。  
计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。  
  

#### 示例 1:
```
输入: [3,2,3,null,3,null,1]

     3
    / \
   2   3
    \   \ 
     3   1

输出: 7 
解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.
```

#### 示例 2:
```
输入: [3,4,5,1,3,null,1]

     3
    / \
   4   5
  / \   \ 
 1   3   1

输出: 9
解释: 小偷一晚能够盗取的最高金额 = 4 + 5 = 9.
```    

## 1.2 解题思路    
仔细审题可以看出，本题是树形的动态规划问题，注意题目要求：如果**两个直接相连**的房子在同一天晚上被打劫，房屋将自动报警。  
   
由于题目要求可知，直接相连的节点间只能有一个能选，所以就分两种情况了：   
 - 选根节点，则其子节点就不能再选了   
 - 不选根节点，则俩子节点都能选取    
   
     
所以本题可以暴力枚举(递归)求解，也可以优化改进后用动态规划算法求解。具体下面分析。  
  

## 1.3 解决方案  
### 1.3.1 暴力枚举   
根据上面分析可以写出暴力枚举的解法，如下：  

c++代码(递归求解)  
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int rob(TreeNode* root) {
        if(root == nullptr)
            return 0;

        int max_val = root->val; //选取根节点，然后递归求根节点的孙子节点
        if(root->left != nullptr)
            max_val += rob(root->left->left) + rob(root->left->right);
        
        if(root->right != nullptr)
            max_val += rob(root->right->left) + rob(root->right->right);

        return max(max_val, rob(root->left)+rob(root->right)); //两种情况取最大值
    }
};
```

  
### 1.3.2 动态规划  
看到[题解中](https://leetcode-cn.com/problems/house-robber-iii/solution/cdong-tai-gui-hua-si-xiang-shi-xian-xiang-xi-shuo-/)，这个代码写的还是很易懂的。  
  

代码中用深度优先遍历树，然后用pair存储选或不选该节点时的最大值   
```c++
class Solution {
public:
    pair<int, int> dfs(TreeNode *root) {
        if (root == nullptr)
            return { 0, 0 };
		
        auto left_pair = dfs(root->left);
        auto right_pair = dfs(root->right);
        return { root->val + left_pair.second + right_pair.second, 
                max(left_pair.first, left_pair.second) + max(right_pair.first, right_pair.second) };
    }
    
    int rob(TreeNode* root) {
        auto p = dfs(root);
        return max(p.first, p.second);
    }
};
```
  
  <br/>
另一种解法：    
由于只有两种状态，所以可以用一个数组(仅2个元素就够了)来保存，当0时，就为不打劫当前节点，当为1时，就为打劫当前节点。    
  - 当为0时，只需返回左子树和右子树的和最大值    
  - 当为1时，只需将root节点的值加上不选取左子树和右子树(即左子树和右子树状态码均为0的状态)的结果返回即可    

c++代码如下：  
```c++
class Solution {
public:
    void do_rob(TreeNode* root, vector<int>& max_val) {
        if (root == nullptr) 
            return ;
        
        vector<int> left_val(2);
        do_rob(root->left, left_val);
        
        vector<int> right_val(2);
        do_rob(root->right, right_val);

        max_val[0] = max(left_val[0], left_val[1]) + max(right_val[0], right_val[1]);
        max_val[1] = root->val + left_val[0] + right_val[0];
        
        return ;
    }

    int rob(TreeNode* root) {
        if(root == nullptr)
            return 0;

        vector<int> max_val(2);
        do_rob(root, max_val);
        return max(max_val[0], max_val[1]);
    }
};
```




