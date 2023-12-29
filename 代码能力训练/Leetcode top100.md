## 哈希

### 1.两数之和

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

 

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**示例 2：**

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例 3：**

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

~~~cpp
/*
思路:
采用哈希表:
如果target-nums[i]有值，就输出i与target-nums[i]的下标
如果target-nums[i]无值，就将nums[i]的下标i存到哈希表中
*/
~~~

~~~cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> hash;
        for(int i=0;i<nums.size();i++){
            if(hash.count(target-nums[i])){
                return {hash[target-nums[i]],i};
            }else  hash[nums[i]] = i;
        }
        return {-1,-1};
    }
};
~~~

### 49.字母异位词分词

给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。

**字母异位词** 是由重新排列源单词的所有字母得到的一个新单词。

**示例 1:**

```
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

**示例 2:**

```
输入: strs = [""]
输出: [[""]]
```

**示例 3:**

```
输入: strs = ["a"]
输出: [["a"]]
```

~~~cpp
/*
思路 先对每个字符串进行排序，相同顺序的存到哈希表中
最后将相同顺序的存到一个vector<string>中
*/
~~~

~~~cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string,vector<string>> hash;
        string str;
        for(int i=0;i<strs.size();i++){
            str=strs[i];
            sort(str.begin(),str.end());
            hash[str].push_back(strs[i]);
        }
        vector<vector<string>> res;
        for(auto [x,c]:hash){
            res.push_back(c);
        }
        return res;
    }
};
~~~

### 128.最长连续序列

给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 `O(n)` 的算法解决此问题。

**示例 1：**

```
输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

**示例 2：**

```
输入：nums = [0,3,7,2,5,8,4,6,0,1]
输出：9
```

~~~cpp
/* 
思路：
采用一个无序集合，存储所有非重复的值
遍历无序集合，如果x-1存在则跳过，找到最起始的位置进行遍历，计数。
*/
~~~

~~~cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> un_set;
        for(auto c:nums){
            un_set.insert(c);        
        }
        int res = 0;
        for(auto c:un_set){
            if(!un_set.count(c-1)){
                int curnum = c;
                int curlength = 1;
                while(un_set.count(curnum+1)){
                    curnum+=1;
                    curlength+=1;
                }
                res = max(curlength,res);
            }
            
        }
        return res;
    }
};
~~~

## 双指针

### 283.移动零

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**请注意** ，必须在不复制数组的情况下原地对数组进行操作。

**示例 1:**

```
输入: nums = [0,1,0,3,12]
输出: [1,3,12,0,0]
```

**示例 2:**

```
输入: nums = [0]
输出: [0]
```

~~~cpp
/*
思路:双指针算法
将不等于0的挪到前面，后面全部补为0
*/
~~~

~~~cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int i=0,j=0;
        for(auto c:nums){
            if(c!=0){
                nums[j++]=c;                
            }
        }
        for(j;j<nums.size();j++) nums[j] = 0;
    }
};
~~~

### 11.盛最多水的容器

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

**说明：**你不能倾斜容器。

**示例 1：**

![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**示例 2：**

```
输入：height = [1,1]
输出：1
```

~~~c++
/*
思路 左右往里面夹着，每次以最低的为高，算个面积 一直算直到两者相等，求出最高即可
//先将i往里挪 还是先将j往里挪呢？ 注意是先挪低的那一方
在每个状态下，无论长板或短板向中间收窄一格，都会导致水槽 底边宽度 −1 变短：
若向内 移动短板 ，水槽的短板 min(h[i],h[j]) 可能变大，因此下个水槽的面积 可能增大 。
若向内 移动长板 ，水槽的短板 min(h[i],h[j]) 不变或变小，因此下个水槽的面积 一定变小 。

作者：Krahets
链接：https://leetcode.cn/problems/container-with-most-water/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



*/
~~~

~~~cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int i=0,j=height.size()-1;
        int res = 0;
        while(i<j){
            int min = height[i]<height[j]?height[i]:height[j];
            //先将i往里挪 还是先将j往里挪呢？ 先挪低的
            res = max(min*(j-i),res);  
            if(height[i]<height[j]) i++;
            else j--;            
        }
        return res;
    }
};
~~~

### 15.三数之和

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请

你返回所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

**示例 1：**

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

**示例 2：**

```
输入：nums = [0,1,1]
输出：[]
解释：唯一可能的三元组和不为 0 。
```

**示例 3：**

```
输入：nums = [0,0,0]
输出：[[0,0,0]]
解释：唯一可能的三元组和为 0 。
```

~~~cpp
/*
思路：
先对数组进行排序  三指针 i j k，固定i j往右增大 k往左缩小
主要设置去除重复值，前面出现的不用去除 如 -1 -1 2， -2 1 1 遇到第一个重复的可能会用到后面的值不用去重，后面重复的需要去除
*/
~~~

~~~cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(),nums.end());
        vector<vector<int>>res;
        for(int i=0;i<nums.size();i++){
            //将i固定            
            if(i&&nums[i] == nums[i-1]) continue;
            for(int j=i+1,k=nums.size()-1;j<k;j++){
                if(j>i+1 && nums[j] == nums[j-1]) continue;
                while(j<k && nums[i]+nums[j]+nums[k]>0) k--;
                if(j<k && nums[i]+nums[j]+nums[k] == 0) 
                res.push_back({nums[i],nums[j],nums[k]});
            }
        }
        return res;

    }
};
~~~

### 42.接雨水

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。
```

**示例 2：**

```
输入：height = [4,2,0,3,2,5]
输出：9
```

~~~cpp
/*
实现思路  //针对除第一个和最后一个柱子 找左边最大的右边最大的 的最小值(包括本身) -当前高度
*/
~~~

~~~cpp
class Solution {
public:
    int trap(vector<int>& height) {
        //针对除第一个和最后一个柱子 找左边最大的右边最大的(包括本身)-当前高度
        int n = height.size();
        vector<int> left(n),right(n);
        left[0]=height[0],right[n-1] = height[n-1];
        for(int i=1;i<height.size();i++){
            left[i] = max(left[i-1],height[i]);
            right[n-i-1] = max(right[n-i],height[n-i-1]);
        }
        int res = 0;
        for(int i=1;i<n-1;i++){
            res += min(left[i],right[i]) - height[i];
        }
        return res;

    }
};
~~~

## 滑动窗口

### 3.无重复字符的最长字串

给定一个字符串 `s` ，请你找出其中不含有重复字符的 **最长子串** 的长度。

**示例 1:**

```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例 2:**

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例 3:**

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

~~~cpp
/*
思路：滑动窗口
用一个哈希表，i,j作为滑动窗口，当hash[i]==2时候，j++,直到去除重复的为止
*/
~~~

~~~cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char,int>hash;
        int res=0;
        for(int i=0,j=0;i<s.size();i++){
            hash[s[i]]++;
            while(hash[s[i]]>1) hash[s[j++]]--;
            res = max(res,i-j+1);
        }
        return res;

    }
};
~~~

### 438.找到字符串中所有字母异位词

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

**异位词** 指由相同字母重排列形成的字符串（包括相同的字符串）。

**示例 1:**

```
输入: s = "cbaebabacd", p = "abc"
输出: [0,6]
解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
```

 **示例 2:**

```
输入: s = "abab", p = "ab"
输出: [0,1,2]
解释:
起始索引等于 0 的子串是 "ab", 它是 "ab" 的异位词。
起始索引等于 1 的子串是 "ba", 它是 "ab" 的异位词。
起始索引等于 2 的子串是 "ab", 它是 "ab" 的异位词。
```

~~~cpp
//采用一个滑动窗口
// i,j i为起始位置，j为终止位置，当i加一个位置，hash[s[i]]--.同时hash[s[j]]++,判断此时的哈希表是否相等即可
~~~

~~~cpp
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        unordered_map<char,int> hs;
        unordered_map<char,int> hp;
        int slen = s.size(),plen=p.size();
        if(slen < plen) return {};
        for(int i=0;i<plen;i++){
            hs[s[i]]++; hp[p[i]]++;
        }
        vector<int> res;
        if(hs == hp) res.push_back(0);
        for(int i=0;i+plen<slen;i++){
            if(--hs[s[i]] == 0) hs.erase(s[i]);
            hs[s[i+plen]]++;
            if(hs == hp) res.push_back(i+1);
        }
        return res;
    }
};
~~~

## 子串

### 560.和为K的子数组

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 *该数组中和为 `k` 的子数组的个数* 。

子数组是数组中元素的连续非空序列。

**示例 1：**

```
输入：nums = [1,1,1], k = 2
输出：2
```

**示例 2：**

```
输入：nums = [1,2,3], k = 3
输出：2
```

~~~cpp
/**
思路：采用前缀和＋哈希表解决
前缀和求出来后存到哈希表中，每个试着减去k如果有值说明有连续字串和为K
**/
~~~

~~~cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int n=nums.size();
        vector<int> f(n);
        f[0]=nums[0];
        for(int i=1;i<n;i++){
            f[i]=f[i-1]+nums[i];
        }
        unordered_map<int,int> hash;
        hash[0]=1;
        int res=0;
        for(int i=0;i<n;i++){
            res+=hash[f[i]-k];
            hash[f[i]]++;
        }
        return res;

    }
};
~~~

### 239.滑动窗口最大值

给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。

返回 *滑动窗口中的最大值* 。

**示例 1：**

```
输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
输出：[3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

~~~
-1 3 
~~~

**示例 2：**

```
输入：nums = [1], k = 1
输出：[1]
```

~~~cpp
/**
思路：采用单调队列解决，队头为最大，每轮进行比较 i-k+1判断是否滑出窗口
队尾到队头  从小到大
来个值直接将小于它的全部干掉，塞到队尾
**/
~~~

~~~cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        //单调队列解决
        deque<int> dq;
        vector<int> res;
        for(int i=0;i<nums.size();i++){
            //单调队列
            //有更大的把小的撵走，更大的下标肯定更优先选择，来小的塞到后面，防止前面大的滑出去了，小的可替
            if(dq.size()&& i-k+1 > dq.front() ) dq.pop_front();
            while(dq.size() && nums[i]> nums[dq.back()]) dq.pop_back();
            dq.push_back(i);
            if(i-k+1>=0) res.push_back(nums[dq.front()]);
        }
        return res;
    }
};
~~~

### 76.最小覆盖字串

给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

**注意：**

- 对于 `t` 中重复字符，我们寻找的子字符串中该字符数量必须不少于 `t` 中该字符数量。
- 如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

**示例 1：**

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
解释：最小覆盖子串 "BANC" 包含来自字符串 t 的 'A'、'B' 和 'C'。
```

**示例 2：**

```
输入：s = "a", t = "a"
输出："a"
解释：整个字符串 s 是最小覆盖子串。
```

**示例 3:**

```
输入: s = "a", t = "aa"
输出: ""
解释: t 中两个字符 'a' 均应包含在 s 的子串中，
因此没有符合条件的子字符串，返回空字符串
```

~~~cpp
/*
思路：采用哈希表法，用两个哈希表 进行比较
如果遍历的串里存在t的值就让count++，然后进行窗口滑动即可
*/
~~~

~~~cpp
class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<int,int> hs;
        unordered_map<int,int> ht;
        for(auto c:t) ht[c]++;
        int count = 0;
        string res;
        for(int i=0,j=0;i<s.size();i++){
            hs[s[i]]++;
            //只算到第一个找到的位置即可，后面不会再更新了
            if(hs[s[i]]<=ht[s[i]]) count++;
            while(hs[s[j]] > ht[s[j]]) hs[s[j++]]--;
            if(count == t.size() && (res.empty() || i-j+1<res.size()) ){
                res = s.substr(j,i-j+1);
            }            
        }
        return res;
    }
};
~~~

## 普通数组

### 53.最大子数组和

给你一个整数数组 `nums` ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**子数组** 是数组中的一个连续部分。

**示例 1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**示例 2：**

```
输入：nums = [1]
输出：1
```

**示例 3：**

```
输入：nums = [5,4,-1,7,8]
输出：23
```

~~~cpp
1 2 5
1 3
~~~

~~~cpp
/**
思路：求前缀和：
如果前缀和为负数，则舍弃掉前缀，采用当前值为最大。
如果为正数，则利用上前缀和
**/
~~~

~~~cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        //可以求出前缀和 找出最大值和最小值
        int n = nums.size();
        if(n==1) return nums[0];
        vector<int> f(n);
        f[0] = nums[0];
        int res = -2e9;
        for(int i=1;i<n;i++){
            f[i]=max(nums[i],f[i-1]+nums[i]);
            res = max(f[i],res);
        }
        return max(res,f[0]);
    }
};
~~~

### 56.合并区间

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回 *一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间* 。

**示例 1：**

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**示例 2：**

```
输入：intervals = [[1,4],[4,5]]
输出：[[1,5]]
解释：区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

~~~cpp
/*
先将起始位置从头到尾进行排序，然后判断是否有重叠的，有重叠的将末尾更新为最新的即可
*/
~~~

~~~cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<pair<int,int>> s;
        vector<vector<int>> res;
        for(auto c:intervals) s.push_back({c[0],c[1]});
        sort(s.begin(),s.end());
        int st=-1,ed=-1;
        for(auto c:s){
            if(ed<c.first){
                //说明是分离的
                if(ed != -1) res.push_back({st,ed});
                st = c.first,ed=c.second;
            }else ed=max(ed,c.second);
        }
        res.push_back({st,ed});
        return res;
    }
};
~~~

### 189.轮转数组

给定一个整数数组 `nums`，将数组中的元素向右轮转 `k` 个位置，其中 `k` 是非负数。

**示例 1:**

```
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右轮转 1 步: [7,1,2,3,4,5,6]
向右轮转 2 步: [6,7,1,2,3,4,5]
向右轮转 3 步: [5,6,7,1,2,3,4]
```

**示例 2:**

```
输入：nums = [-1,-100,3,99], k = 2
输出：[3,99,-1,-100]
解释: 
向右轮转 1 步: [99,-1,-100,3]
向右轮转 2 步: [3,99,-1,-100]
```

~~~cpp
/*
思路 
两部分都逆置
然后整体逆置
*/
~~~

~~~cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();
        k = k % n; // 处理 k 大于数组长度的情况
        reverse(nums.begin(),nums.end()-k);
        reverse(nums.end()-k,nums.end());
        reverse(nums.begin(),nums.end());
    }
};
~~~

### 238.除自身以外数组的乘积

给你一个整数数组 `nums`，返回 *数组 `answer` ，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积* 。

题目数据 **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内。

请 **不要使用除法，**且在 `O(*n*)` 时间复杂度内完成此题。

**示例 1:**

```
输入: nums = [1,2,3,4]
输出: [24,12,8,6]
```

**示例 2:**

```
输入: nums = [-1,1,0,-3,3]
输出: [0,0,9,0,0]
```

~~~cpp
/*
思路：针对每个数字算左边的乘积和右边的乘积，然后遍历每个数字将左右两部分的乘积乘起来即可
*/
~~~

~~~cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        //针对每个数算一个左边的乘积和右边的乘积
        int n=nums.size();
        vector<int> left(n),right(n);
        left[0]=1;
        right[n-1]=1;
        for(int i=1;i<nums.size();i++){
            left[i]=left[i-1]*nums[i-1];            
            right[n-i-1] =right[n-i]*nums[n-i];
        }
        vector<int> res(n);
        for(int i=0;i<n;i++){
            res[i]=left[i]*right[i];
        }
        return res;
    }
};
~~~

### 41.缺失的第一个正数

给你一个未排序的整数数组 `nums` ，请你找出其中没有出现的最小的正整数。

请你实现时间复杂度为 `O(n)` 并且只使用常数级别额外空间的解决方案。

**示例 1：**

```
输入：nums = [1,2,0]
输出：3
```

**示例 2：**

```
输入：nums = [3,4,-1,1]
输出：2
```

**示例 3：**

```
输入：nums = [7,8,9,11,12]
输出：1
```

~~~cpp
/**
思路1：哈希表法，但不是使用常数级别额外空间，代码较简单
思路2：将每个元素放到正确的位置，原地交换，不停进行检查直到当前位置为负数或者大于n或者已经放到正确位置才退出循环
**/
~~~

~~~cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        unordered_map<int,int> hash;
        for(auto c:nums) hash[c]++;
        for(int i=1;;i++){
            if(!hash.count(i)) return i;
        }
    }
};
~~~

~~~cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n=nums.size();
        for(int i=0;i<nums.size();i++){
            while(nums[i] !=i+1)
            {
                //while循环是不停检查
                if(nums[i]<=0 || nums[i]>n || nums[i] == nums[nums[i]-1]){
                    break;
                }
                //进行互换 nums[3]=8则 将nums[7]的值赋值为nums[3],nums[7]=8;
                int idx = nums[i]-1;
                nums[i] = nums[idx];
                nums[idx] = idx+1;
            }

        }
        for(int i=0;i<nums.size();i++){
            if(nums[i] != i+1) return i+1;
        }
        return nums.size()+1;
    }
};
~~~

