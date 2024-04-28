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

~~~java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer,Integer> hash = new HashMap<>();
        for(int i=0;i<nums.length;i++){
            if(hash.containsKey(target-nums[i])) {
                return new int[]{hash.get(target-nums[i]),i};
            }else{
                hash.put(nums[i],i);
            }        
        }
        return new int[]{-1,-1};
    }
}
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
    public List<List<String>> groupAnagrams(String[] strs) {
        HashMap<String,List<String>> hash = new HashMap<>();
        for(int i=0;i<strs.length;i++){
            char[] ca = strs[i].toCharArray();
            Arrays.sort(ca);
            String sorted = new String(ca);
            hash.computeIfAbsent(sorted,k->new ArrayList<>()).add(strs[i]);
        }
        return new ArrayList<>(hash.values());
    }
}
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

## 矩阵

### 73.矩阵置零

给定一个 `m x n` 的矩阵，如果一个元素为 **0** ，则将其所在行和列的所有元素都设为 **0** 。请使用 **[原地](http://baike.baidu.com/item/原地算法)** 算法**。**

**示例 1：**

<img src="https://assets.leetcode.com/uploads/2020/08/17/mat1.jpg" alt="img" style="zoom:80%;" />

```
输入：matrix = [[1,1,1],[1,0,1],[1,1,1]]
输出：[[1,0,1],[0,0,0],[1,0,1]]
```

**示例 2：**

<img src="https://assets.leetcode.com/uploads/2020/08/17/mat2.jpg" alt="img" style="zoom:80%;" />

```
输入：matrix = [[0,1,2,0],[3,4,5,2],[1,3,1,5]]
输出：[[0,0,0,0],[0,4,5,0],[0,3,1,0]]
```

~~~cpp
/*
思路：采用两个标记数组
*/
~~~

~~~cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m=matrix.size();
        int n=matrix[0].size();
        vector<int> row(m,1),col(n,1);
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(matrix[i][j]== 0){
                    row[i]=0;                    
                    col[j]=0;
                }
            }
        }
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(!row[i] || !col[j]){
                    matrix[i][j]=0;
                }
            }
        }

    }
};
~~~

### 54.螺旋矩阵

给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/13/spiral1.jpg)

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/11/13/spiral.jpg)

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

~~~cpp
/*
思路 从右下左上的顺序轮转，用4个变量记录边界
*/
~~~

~~~cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        //从右下左上四个方向遍历
        int l=0,r=matrix[0].size()-1;
        int u=0,d=matrix.size()-1;
        vector<int> res;
        while(true){
            for(int j=l;j<=r;j++){ //从左到右
                res.push_back(matrix[u][j]);
            }
            if(++u > d) break;
            for(int i=u;i<=d;i++){ //从上到下
                res.push_back(matrix[i][r]);              
            }
            if(--r <l) break;
            for(int i=r;i>=l;i--) //从右到左
            {
                res.push_back(matrix[d][i]);
            }
            if(--d < u) break;
            for(int i=d;i>=u;i--) //从下到上
            {
                res.push_back(matrix[i][l]);
            }
            if(++l > r) break;
        }
        return res;
    }
};
~~~

### 48.旋转图像

给定一个 *n* × *n* 的二维矩阵 `matrix` 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在**[ 原地](https://baike.baidu.com/item/原地算法)** 旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要** 使用另一个矩阵来旋转图像。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/08/28/mat1.jpg)

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[[7,4,1],[8,5,2],[9,6,3]]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/08/28/mat2.jpg)

```
输入：matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
输出：[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

~~~cpp
/**
思路：先水平旋转 后对角线旋转
**/
~~~

~~~cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n=matrix.size();
        //先翻转
        for(int i=0;i<n/2;i++){
            for(int j=0;j<n;j++){
                swap(matrix[i][j],matrix[n-i-1][j]);
            }
        }
        //后主对角线交换
        for(int i=0;i<n;i++)
            for(int j=i+1;j<n;j++)
            swap(matrix[i][j],matrix[j][i]);
        
            
    }
};
~~~

### 240.搜索二维矩阵Ⅱ

编写一个高效的算法来搜索 `*m* x *n*` 矩阵 `matrix` 中的一个目标值 `target` 。该矩阵具有以下特性：

- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/11/25/searchgrid2.jpg)

```
输入：matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 5
输出：true
```

**示例 2：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/11/25/searchgrid.jpg)

```
输入：matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 20
输出：false
```

~~~cpp
//思路：
/*
从第一行最后一个开始，target小就往左，target大就往下
*/

~~~



~~~cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m=matrix.size();
        int n=matrix[0].size();
        int x=0,y=n-1,num;
        while(x<m && y>=0){
            num=matrix[x][y];
            if(num>target) y--;
            else if(num == target) return true;
            else if(num < target) x++;
        }
        return false;
    }
};
~~~

## 链表

### 160.相交链表

给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 `null` 。

图示两个链表在节点 `c1` 开始相交**：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

题目数据 **保证** 整个链式结构中不存在环。

**注意**，函数返回结果后，链表必须 **保持其原始结构** 。

**自定义评测：**

**评测系统** 的输入如下（你设计的程序 **不适用** 此输入）：

- `intersectVal` - 相交的起始节点的值。如果不存在相交节点，这一值为 `0`
- `listA` - 第一个链表
- `listB` - 第二个链表
- `skipA` - 在 `listA` 中（从头节点开始）跳到交叉节点的节点数
- `skipB` - 在 `listB` 中（从头节点开始）跳到交叉节点的节点数

评测系统将根据这些输入创建链式数据结构，并将两个头节点 `headA` 和 `headB` 传递给你的程序。如果程序能够正确返回相交节点，那么你的解决方案将被 **视作正确答案** 

~~~cpp
/*
思路:a走到头 走b b走到头 走a 不相交最后都会为空
*/
~~~

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode *p=headA,*q=headB;
        while(p!=q){  
            if(!p) p=headB;   
            if(!q) q=headA;    
            if(p==q) return p;                           
            p=p->next;
            q=q->next;           
        }
        return p;        
    }
};
~~~

### 206.反转链表

给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex1.jpg)

```
输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex2.jpg)

```
输入：head = [1,2]
输出：[2,1]
```

**示例 3：**

```
输入：head = []
输出：[]
```

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode *p=head,*q,*h=new ListNode(-1);
        while(p){
            q=p->next;
            p->next=h->next;
            h->next=p;
            p=q;
        }
        return h->next;
    }
};
~~~

### 234.回文链表

给你一个单链表的头节点 `head` ，请你判断该链表是否为回文链表。如果是，返回 `true` ；否则，返回 `false` 。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/03/03/pal1linked-list.jpg)

```
输入：head = [1,2,2,1]
输出：true
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/03/03/pal2linked-list.jpg)

```
输入：head = [1,2]
输出：false
```

~~~cpp
/*
思路 用栈 从前往后和从后往前遍历一样说明是回文串
*/
~~~

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        //回文链表进栈即可
        stack<ListNode*> st;
        ListNode* current = head;
        while (current != nullptr) {
            st.push(current);
            current = current->next;
        }
        ListNode* c = head;
        while(c != nullptr){            
            auto node = st.top();
            st.pop();
            if(c->val != node->val) return false;
            c=c->next;
         }
         return true;

    }
};
~~~

### 141.环形链表

给你一个链表的头节点 `head` ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。**注意：`pos` 不作为参数进行传递** 。仅仅是为了标识链表的实际情况。

*如果链表中存在环* ，则返回 `true` 。 否则，返回 `false` 。

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)

```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

~~~cpp
/*
思路 采用快慢指针解决
*/
~~~

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(!head) return false;
        auto *p=head,*q=head;
        do{
            if(p) p=p->next;
            if(q) q=q->next;
            if(q) q=q->next;
        }while(p!=q);
        if(q) return true;
        else return false;
        
    }
};
~~~

### 142.环形链表Ⅱ

给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。

**不允许修改** 链表。

~~~cpp
/**
思路：先使用快慢指针使得两者相遇判断出有环，
相遇点之后 时光倒流 慢指针退到交点 快指针退到-y点，也就是说 从交点走x步会走到c‘，那么从相遇点走x步会走到交点，x步恰好是从起点开始走的
**/
~~~

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if(!head) return NULL;
        auto *p=head,*q=head;
        do{
            if(p) p=p->next;
            if(q) q=q->next;
            if(q) q=q->next;
        }while(p!=q);
        if(!p) return NULL;
        p=head;
        while(p!=q){
            p=p->next;
            q=q->next;
        }
        return q;
    }
};
~~~

### 21.合并两个有序链表

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

**示例 2：**

```
输入：l1 = [], l2 = []
输出：[]
```

**示例 3：**

```
输入：l1 = [], l2 = [0]
输出：[0]
```

~~~cpp
/**
思路 一个一个进行比较 最后收尾
**/
~~~

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        auto *p=list1,*q=list2;
        auto *h=new ListNode(0),*r=h;
        while(p&&q){
            if(p->val < q->val) {r->next=p;p=p->next;r=r->next;}
            else {r->next=q;q=q->next;r=r->next;}
        }
        //尾部自带为空 无需收尾
        if(q) {r->next =q;}
        if(p) {r->next =p;}
        return h->next;
        
    }
};
~~~

### 2.两数相加

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/01/02/addtwonumber1.jpg)

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

**示例 2：**

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

**示例 3：**

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]
```

~~~cpp
/**
思路：逐个相加即可，直到为空,不断往右进位
**/
~~~

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        auto *p=l1,*q=l2,*h=new ListNode(),*r=h;
        int jinwei=0,gewei,x,y,sum;        
        while(p||q){
            if(p) {x=p->val;p=p->next;} else x=0;
            if(q) {y=q->val;q=q->next;} else y=0;
            gewei = (jinwei+x+y)%10;//用的是上一轮的进位
            jinwei =(jinwei+x+y)/10;
            auto n = new ListNode(gewei);
            r->next = n;r=r->next;
        }
        if(jinwei) 
        {
            auto n = new ListNode(jinwei);
            r->next=n;r=r->next;
        }
        r->next=nullptr;
        return h->next;
    }
};
~~~

### 19.删除链表的倒数第K个结点

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**示例 2：**

```
输入：head = [1], n = 1
输出：[]
```

**示例 3：**

```
输入：head = [1,2], n = 1
输出：[1]
```

~~~cpp
/*
采用倒数第K个元素办法 一个先不走 一个走K步 找到后 拿到前驱删除即可
*/
~~~

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(!head->next && n == 1) return nullptr;
        auto *p=head,*q=head,*pre=new ListNode(-999);
        int count=0;
        while(p){
            if(count>=n) {
                pre = q;
                q=q->next;
            }
            p=p->next;count++;
        }
        if(pre->val == -999)  head =q->next;            
        else pre->next =q->next;
        delete q;
        return head;
    }
};
~~~

### 24.两两交换链表中的节点 

给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/swap_ex1.jpg)

```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**示例 2：**

```
输入：head = []
输出：[]
```

**示例 3：**

```
输入：head = [1]
输出：[1]
```

~~~cpp
//两两进行交换，直到剩下最后一个为止，如果为奇数个则不交换最后一个
~~~

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if(!head || !head->next) return head;
        auto *p = head;
        ListNode *q,*tmp,*h=new ListNode(0);
        ListNode *pre = h;
        head = head->next;
        pre->next = p;
        while(p){
            if(p->next) 
            {  q=p->next; tmp=q->next;
                //下面是交换
                q->next = p;
                p->next =tmp;
                pre->next = q;                
            }
            pre = p;
            p=p->next;            
        }
        return head;       
    }
};
~~~

### 25.K个一组翻转链表

给你链表的头节点 `head` ，每 `k` 个节点一组进行翻转，请你返回修改后的链表。

`k` 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 `k` 的整数倍，那么请将最后剩余的节点保持原有顺序。

你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex1.jpg)

```
输入：head = [1,2,3,4,5], k = 2
输出：[2,1,4,3,5]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex2.jpg)

```
输入：head = [1,2,3,4,5], k = 3
输出：[3,2,1,4,5]
```

~~~cpp
/**
思路 将节点进栈进行操作，然后取出栈内元素即为倒序
**/
~~~

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if(!head) return nullptr;
        stack<ListNode*> st;
        ListNode *h=new ListNode(0);
        ListNode *r = h,*p=head;
        int sum=0;
        while(p) { p=p->next;sum++;}
        int n=sum/k,count = 0,js=0;        
        while(head){
            st.push(head);
            head = head ->next;
            count++;
            if(count == k){
                while(!st.empty()){
                    auto c= st.top();
                    st.pop();
                    r->next = c;
                    r=c;
                }
                count = 0;
                js++;
            }
            if(js == n) break;     
        }
        if(js == n) r->next = head;
        
        return  h->next;
    }
};
~~~

### 138.随机链表的复制

给你一个长度为 `n` 的链表，每个节点包含一个额外增加的随机指针 `random` ，该指针可以指向链表中的任何节点或空节点。

构造这个链表的 **[深拷贝](https://baike.baidu.com/item/深拷贝/22785317?fr=aladdin)**。 深拷贝应该正好由 `n` 个 **全新** 节点组成，其中每个新节点的值都设为其对应的原节点的值。新节点的 `next` 指针和 `random` 指针也都应指向复制链表中的新节点，并使原链表和复制链表中的这些指针能够表示相同的链表状态。**复制链表中的指针都不应指向原链表中的节点** 。

例如，如果原链表中有 `X` 和 `Y` 两个节点，其中 `X.random --> Y` 。那么在复制链表中对应的两个节点 `x` 和 `y` ，同样有 `x.random --> y` 。

返回复制链表的头节点。

用一个由 `n` 个节点组成的链表来表示输入/输出中的链表。每个节点用一个 `[val, random_index]` 表示：

- `val`：一个表示 `Node.val` 的整数。
- `random_index`：随机指针指向的节点索引（范围从 `0` 到 `n-1`）；如果不指向任何节点，则为 `null` 。

你的代码 **只** 接受原链表的头节点 `head` 作为传入参数。

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/01/09/e1.png)

```
输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]
```

**示例 2：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/01/09/e2.png)

```
输入：head = [[1,1],[2,1]]
输出：[[1,1],[2,1]]
```

**示例 3：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/01/09/e3.png)**

```
输入：head = [[3,null],[3,0],[3,null]]
输出：[[3,null],[3,0],[3,null]]
```

~~~java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
class Solution {
    HashMap<Node,Node> kv = new HashMap<Node,Node>();
    public Node copyRandomList(Node head) {
        if(head == null) return null;
        if(!kv.containsKey(head)){
            Node nodeNew = new Node(head.val); //开始的head
            kv.put(head,nodeNew);
            nodeNew.next = copyRandomList(head.next);
            nodeNew.random = copyRandomList(head.random);
        }
        return kv.get(head);
    }
}
~~~

### 148.排序链表

给你链表的头结点 `head` ，请将其按 **升序** 排列并返回 **排序后的链表** 。

**示例 1：**

![image-20240306222602706](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20240306222602706.png)

~~~java
输入：head = [-1,5,3,4,0]
输出：[-1,0,3,4,5]
~~~

**示例 2：**

![image-20240306222659307](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20240306222659307.png)

~~~java
输入：head = [-1,5,3,4,0]
输出：[-1,0,3,4,5]
~~~

~~~java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if(head  == null){
            return null;
        }
        int length = 0;
        ListNode node = head;
        while(node != null){ //算出所有长度
            length++;
            node = node.next;
        }
        ListNode dummyHead = new ListNode(0,head);
        //左边移动是乘2
        for(int subLength = 1; subLength < length;  subLength <<= 1){
            ListNode prev = dummyHead,curr = dummyHead.next;
            //刚好移动到一半
            //每次两两合并 长度为1的时候就遍历到头 
            //长度为2的继续遍历到头
            while(curr != null){  //两个加起来

            ListNode head1 = curr;//一个子链表
            for(int i=1;i<subLength && curr.next != null;i++){
                curr =curr.next;                
            }
            ListNode head2=curr.next;//一个子链表
            curr.next  = null;//置为空
            curr = head2;
            for(int i=1;i<subLength && curr != null && curr.next != null;i++){
                curr = curr.next;
            }
            ListNode next = null; //记录了curr的下一串元素
            if(curr != null){
                next =curr.next;
                curr.next = null;
            }
            ListNode mereged = merge(head1,head2);
            prev.next = mereged;
            while(prev.next !=null){
                prev = prev.next;
            }
            curr = next;
            }
           
        }
        return dummyHead.next;
    }

    public ListNode merge(ListNode head1,ListNode head2) {
        ListNode dummyHead = new ListNode(0);
        ListNode temp = dummyHead,temp1 = head1,temp2 = head2;
        while(temp1 != null && temp2 !=null){
            if(temp1.val <= temp2.val){
                temp.next = temp1;
                temp1 = temp1.next;
            }else{
                temp.next = temp2;
                temp2 =temp2.next;
            }
            temp =temp.next;
        }    
        if(temp1 != null){ //收尾工作
            temp.next = temp1;
        }else if(temp2 != null){
            temp.next = temp2;
        }
        return dummyHead.next;
    }
}
~~~

### 23.合并K个升序链表

给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。

 **示例 1：**

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

~~~java
// 采用优先队列
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
public class Solution {
    static class Cmp implements java.util.Comparator<ListNode> {
        @Override
        public int compare(ListNode a, ListNode b) {
            return Integer.compare(a.val, b.val);
        }
    }

    public ListNode mergeKLists(ListNode[] lists) {
        ListNode head = new ListNode(-1);
        ListNode tail = head;
        PriorityQueue<ListNode> heap = new PriorityQueue<>(new Cmp());

        for (ListNode l : lists) {
            if (l != null) {
                heap.add(l);
            }
        }

        while (!heap.isEmpty()) {
            ListNode t = heap.poll();
            tail.next = t;
            tail = t;
            if (t.next != null) {
                heap.add(t.next);
            }
        }

        return head.next;
    }
}
~~~



~~~java
// 两两合并的思路
class Node{
    int val;
    Node next;
    Node(){}
    Node(int val) {this.val = val;}
    Node(int val,Node next){ this,val = val; this.next = next;}
}
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode ans = null;
        for (int i = 0; i < lists.length; i++) {
            ans = mergeTwoLists(ans, lists[i]);
        }
        return ans;
    }

    public ListNode mergeTwoLists(ListNode a, ListNode b) {
        if (a == null || b == null) {
            return a != null ? a : b;
        }
        ListNode head = new ListNode(0);
        ListNode tail = head, pa = a, pb = b;
        while (pa != null && pb != null) {
            if (pa.val < pb.val) {
                tail.next = pa;
                pa = pa.next;
            } else {
                tail.next = pb;
                pb = pb.next;
            }
            tail = tail.next;
        }
        if (pa != null) {
            tail.next = pa;
        } else {
            tail.next = pb;
        }
        return head.next;
    }
}

~~~

### 146.LRU缓存

请你设计并实现一个满足 LRU (最近最少使用) 缓存]约束的数据结构。

实现 `LRUCache` 类：

- `LRUCache(int capacity)` 以 **正整数** 作为容量 `capacity` 初始化 LRU 缓存
- `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value` ；如果不存在，则向缓存中插入该组 `key-value` 。如果插入操作导致关键字数量超过 `capacity` ，则应该 **逐出** 最久未使用的关键字。

函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。

~~~java
// 函数 `get` 和 `put` 必须以 `O(1)`  那这个直接用hash表
//逐出最久未使用得那就用双向链表 要删除表头和表尾元素

class LRUCache {
    class Node{
        int key;
        int val;
        Node left,right;
        Node(int key,int val){
            this.key = key;
            this.val = val;
            this.left = null;
            this.right = null;
        } 
    }
    private Node L,R;
    private int n;
    private Map<Integer,Node> hash;
    public LRUCache(int capacity) {
        n = capacity;
        L = new Node(-1,-1);
        R = new Node(-1,-1);
        L.right = R;
        R.left = L;
        hash = new HashMap<>();
    }
    public void insert(Node p){
        //双向链表加入头结点
        p.left = L;
        p.right = L.right;
        L.right = p;
        p.right.left = p;
        
    }
    public void remove(Node p){
        //删除一个结点
        p.left.right = p.right;
        p.right.left = p.left;
    }
    public int get(int key) {
        if(hash.containsKey(key)){        
            Node newNode = hash.get(key);
            remove(newNode);
            insert(newNode);
            return newNode.val;
        }else{
            return -1;
        }
    }
    public void put(int key, int value) {
        //放置一个新的值
        if(hash.containsKey(key)){
            Node newNode = hash.get(key);
            remove(newNode);
            insert(newNode);
            newNode.val = value;
        }else{
            if(n == hash.size()){
                //说明满了 得把最后一个删除
                Node p = R.left;
                remove(p);
                hash.remove(p.key);//删除是以key
            }
            Node newNode = new Node(key,value);
            hash.put(key,newNode);
            insert(newNode);
        }
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
~~~

##  树

## 94.二叉树中序遍历()

~~~java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
public class Solution {
    ArrayList<Integer> list = new ArrayList<>();

    public List<Integer> inorderTraversal(TreeNode root) {
        if (root == null) {
            return list;
        }
        inorderTraversal(root.left);
        list.add(root.val);
        inorderTraversal(root.right);      
        return list;
    }
}
~~~

## 104.二叉树的最大深度(递归  左子树和右边最大的深度 +1 无值 0)

~~~java
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
     //一个递归解决 有值 左子树和右边最大的深度 +1 无值 0 
public class Solution {
    public int maxDepth(TreeNode root) {
        return root != null?Math.max(maxDepth(root.left),maxDepth(root.right))+1:0;
    }
}
~~~



## 226.翻转二叉树(先序遍历+两两交换)

~~~java
//先序遍历 两两交换
public class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }

        // Swap the left and right children
        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
        // Recursively invert the left and right subtrees
        invertTree(root.left);
        invertTree(root.right);
        return root;
    }
}

~~~

## 101.对称二叉树(递归进去判断)

~~~java
class Solution {
    public boolean isValid(TreeNode p,TreeNode q){
        if(p == null && q == null) return true;
        if(p == null || q == null || p.val != q.val) return false;
        return isValid(p.left,q.right) && isValid(p.right,q.left);
    }
    public boolean isSymmetric(TreeNode root) {
        if(root == null) return true;
        return isValid(root.left,root.right);
    }
}
~~~

## 543.二叉树的直径(递归进去判断，left和right取最大后加1)

~~~java
class Solution{
    public int ans = 0;
    public int diameterOfBinaryTree(TreeNode root){
        dfs(root);//枚举每个结点
        return ans;
    }
    public int dfs(TreeNode root){
        if(root == null) return 0;
        int left = dfs(root.left);
        int right = dfs(root.right);
        ans = Math.max(ans,left+right);//ans进行更新
        return Math.max(left,right)+1;
    }        
}
~~~

## 102.二叉树的层序遍历(进入队列进行遍历)

给你二叉树的根节点 `root` ，返回其节点值的 **层序遍历** 。 （即逐层地，从左到右访问所有节点）。

~~~java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution{
    public List<List<Integer>> levelOrder(TreeNode root){
        List<List<Integer>> ans = new ArrayList<>();
        if(root == null) return ans;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while(!queue.isEmpty()){
            int num = queue.size();
            List<Integer> level = new ArrayList<>();
            for(int i=0;i<num;i++){
				TreeNode topNode = queue.poll();
                 level.add(topNode.val);
                if(topNode.left != null){
                    queue.offer(topNode.left);
                }
                if(topNode.right != null){
                    queue.offer(topNode.right);
                }
            }
            ans.add(level);                        
        }
        return ans;   
    }
}
~~~

  ## 108. 将有序数组转换为二叉搜索树(总是选择中间位置左边的数字作为根节点)

给你一个整数数组 `nums` ，其中元素已经按 **升序** 排列，请你将其转换为一棵 平衡 二叉搜索树。

**示例 1：**

~~~java
输入：nums = [-10,-3,0,5,9]
输出：[0,-3,9,-10,null,5]
解释：[0,-10,5,null,-3,null,9] 也将被视为正确答案
~~~

~~~java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return helper(nums,0,nums.length - 1);
    }
    public TreeNode helper(int [] nums,int left,int right){
        if(left > right){
            return null;
        }
        //总是选择中间位置左边的数字作为根结点
        int mid = (left + right) / 2;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = helper(nums,left,mid-1);
        root.right = helper(nums,mid+1,right);
        return root;
    }
}
~~~

## 98.验证二叉搜索树(中序遍历一遍即可，中间比较后面是否小于前面的)

给你一个二叉树的根节点 `root` ，判断其是否是一个有效的二叉搜索树。

**有效** 二叉搜索树定义如下：

- 节点的左子树只包含**小于** 当前节点的数。
- 节点的右子树只包含 **大于** 当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树

~~~java
class Solution {
    int first = 1;  // 第一次
    int flag = 0;   // 真的
    int pre;    // 使用 Integer 而不是 int，以便区分 null 和 0
    void zhongxu(TreeNode root) {
        if (root == null || flag == 1) return;

        zhongxu(root.left);

        // 如果不是第1次，就进行比较
        if (first == 0 && (root.val <= pre)) {
            // 说明不符合了
            flag = 1;
            return;
        }
        pre = root.val;
        first = 0;
        zhongxu(root.right);
    }
    public boolean isValidBST(TreeNode root) {
        zhongxu(root);
        return flag == 0;
    }
}
~~~

## 230.二叉搜索树中第k小的元素

给定一个二叉搜索树的**根节点 `root`** ，和一个**整数 `k**` ，请你设计一个算法**查找其中第 `k` 个最小元素**（从 1 开始计数）。

~~~java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    int statick = 0;
    int ans = 0;
    int count = 0;
    public void isSmallK(TreeNode root){
        if(root == null || count >= statick) return;
        isSmallK(root.left);
        count++;
        if(statick == count){
            ans = root.val;
        }
        isSmallK(root.right);
    }
    public int kthSmallest(TreeNode root, int k) {
        statick = k;
        isSmallK(root);
        return ans;        
    }
}
~~~

## 199.二叉树的右视图(层次遍历  取 i == num -1 的值即可)

给定一个二叉树的 **根节点** `root`，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

~~~java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        if(root == null) return ans;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);//先进根结点
        while(!queue.isEmpty()){            
            int num = queue.size();
            for(int i=0;i<num;i++){
                TreeNode node = queue.poll();//出栈加取值
                if(i == num -1) ans.add(node.val);
                if(node.left != null) queue.offer(node.left);
                if(node.right != null) queue.offer(node.right);    
            }
        }
        return ans;
    }
}
~~~

## 114.二叉树展开为链表(根左右逆序遍历，右根左遍历+头插法)

给你二叉树的根结点 `root` ，请你将它展开为一个单链表：

- 展开后的单链表应该同样使用 `TreeNode` ，其中 `right` 子指针指向链表中下一个结点，而左子指针始终为 `null` 。
- 展开后的单链表应该与二叉树 [**先序遍历**](https://baike.baidu.com/item/先序遍历/6442839?fr=aladdin) 顺序相同。

~~~java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    TreeNode last =null;
    public void flatten(TreeNode root) {
        //正常 是根左右
        //右左根头插法即可
        if(root == null) return;
        flatten(root.right);
        flatten(root.left);
        //头插法      
        root.right =last;   
        root.left = null;
        last = root;   
    }
}
~~~

## 105.从前序与中序遍历序列构造二叉树

给定两个整数数组 `preorder` 和 `inorder` ，其中 `preorder` 是二叉树的**先序遍历**， `inorder` 是同一棵树的**中序遍历**，请构造二叉树并返回其根节点。

~~~java
import java.util.HashMap;
import java.util.Map;

class Solution {
    private Map<Integer, Integer> hash;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        hash = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            hash.put(inorder[i], i); // 快速查找下标
        }
        return dfs(preorder, inorder, 0, preorder.length - 1, 0, inorder.length - 1);
    }
    private TreeNode dfs(int[] preorder, int[] inorder, int pl, int pr, int il, int ir) {
        if (pl > pr) {
            return null;
        }
        TreeNode root = new TreeNode(preorder[pl]);
        int k = hash.get(preorder[pl]);
        //[pl+1,pl+1 + k-1-il],[il,k-1];
        //中间要跳过k个值
        //[pl+1+k-il,pr], [k+1,ir]
        root.left = dfs(preorder, inorder, pl + 1, pl + 1 + k - 1 - il, il, k - 1);
        root.right = dfs(preorder, inorder, pl + k + 1 - il, pr, k + 1, ir);
        return root;
    }
}
~~~

## 437.路径总和(两个递归遍历，一个遍历根结点，一个遍历子结点)

给定一个二叉树的根节点 **`root` ，**和一个整数 **`targetSum`** ，求该二叉树里节点值之和等于 `targetSum` 的 **路径** 的数目。

**路径** 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

~~~java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public long rootSum(TreeNode root, long targetSum) {
        if (root == null) return 0;
        long ret = 0;
        if (targetSum == root.val) ret++;
        ret += rootSum(root.left, targetSum - root.val);
        ret += rootSum(root.right, targetSum - root.val);
        return ret;
    }

    public int pathSum(TreeNode root, int targetSum) {
        if (root == null) return 0;
        int ret = (int)rootSum(root, (long)targetSum);
        ret += pathSum(root.left, targetSum);
        ret += pathSum(root.right, targetSum);
        return ret;
    }
}

~~~

## 236. 二叉树的最近公共祖先(先序遍历，每次刷新state状态，仅第一次返回)

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

[百度百科](https://baike.baidu.com/item/最近公共祖先/8918834?fr=aladdin)中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。

~~~java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    TreeNode ans = null;
    int dfs(TreeNode root,TreeNode p,TreeNode q){
        if(root == null) return 0;
        int state = 0;//没次都要重新刷新state
        if(root == p) state |= 1;
        else if(root == q) state |= 2;
        state |= dfs(root.left,p,q);
        state |= dfs(root.right,p,q);
        if(state == 3 & ans == null) ans = root;
        return state;
    }
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        //求p和q的公共祖先
        dfs(root,p,q);
        return ans;        
    }
}
~~~

## 124.二叉树中的最大路径和(后序遍历，求每个结点的最大贡献值)

二叉树中的 **路径** 被定义为一条节点序列，序列中每对相邻节点之间都存在一条边。同一个节点在一条路径序列中 **至多出现一次** 。该路径 **至少包含一个** 节点，且不一定经过根节点。

**路径和** 是路径中各节点值的总和。

给你一个二叉树的根节点 `root` ，返回其 **最大路径和** 。

 ~~~java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    int maxSum = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        maxGain(root);
        return maxSum;
    }
    public int maxGain(TreeNode node){
        if(node == null){
            return 0;
        }
        int left = Math.max(0,maxGain(node.left));
        int right = Math.max(0,maxGain(node.right));
           // 节点的最大路径和取决于该节点的值与该节点的左右子节点的最大贡献值
        int priceNewPath = node.val + left + right;    
        //更新答案(和根结点进行对比)
        maxSum = Math.max(maxSum,priceNewPath);
        //返回结点的最大贡献值
        return node.val + Math.max(left,right);
    }
}
 ~~~

# 图论

## 200.岛屿数量(感染法)

给你一个由 **`'1'`（陆地）和 `'0'`（水）**组成的的二维网格，请你计算网格中岛屿的数量。岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。此外，你可以假设该网格的四条边均被水包围

~~~java
class Solution {
    char[][] g;
    //左上右下
    int[] dx={-1,0,1,0};
    int[] dy={0,1,0,-1};
    public int numIslands(char[][] grid) {
        g=grid;
        int cnt = 0;
        for(int i=0;i<g.length;i++)
            for(int j=0;j<g[i].length;j++){
                if(g[i][j] == '1'){
                    dfs(i,j);//感染法
                    cnt++;
                }
            }
        return cnt;        
    }
    //感染法
    void dfs(int x,int y){
        g[x][y] = '0';//把自己先一感染
        for(int i=0;i<4;i++){
            int a =x+dx[i],b=y+dy[i];
            if(a>=0 && a<g.length && b>=0 && b<g[a].length && g[a][b] == '1')
            dfs(a,b);
        }
    }


}
~~~

## 994.腐烂的橘子 (BFS，采用 i*col + j 存哈希)

在给定的 `m x n` 网格 `grid` 中，每个单元格可以有以下三个值之一：

- 值 `0` 代表空单元格；
- 值 `1` 代表新鲜橘子；
- 值 `2` 代表腐烂的橘子。

每分钟，腐烂的橘子 **周围 4 个方向上相邻** 的新鲜橘子都会腐烂。

返回 *直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 `-1`* 。

~~~java
class Solution {
    int[] dx ={-1,0,1,0};
    int[] dy ={0,1,0,-1};
    public int orangesRotting(int[][] grid) {
        int row = grid.length;
        int col = grid[0].length;
        Queue<Integer> queue = new ArrayDeque<>();//使用ArrayDequeue 替换ArrayList
        Map<Integer,Integer> depth = new HashMap<>();
        for(int i=0;i<row;i++)
            for(int j=0;j<col;j++){
                if(grid[i][j] == 2){
                    int code = i*col + j;//乘列才能更好哈希
                    queue.add(code);
                    depth.put(code,0);
                }
            }
        int ans = 0;
        while(!queue.isEmpty()){
            int code = queue.poll();
            int x= code/col;
            int y= code%col;
            for(int i=0;i<4;i++){
                int xx = x+dx[i];
                int yy = y+dy[i];
                if(xx>=0&&xx<row&&yy>=0&&yy<col&&grid[xx][yy] == 1){
                        grid[xx][yy]=2;
                        int ncode = xx*col+ yy;
                        queue.add(ncode);
                        //计次
                        depth.put(ncode,depth.get(code)+1);
                    
                }
                ans = depth.get(code);//ans会一直更新到最新值
            }
        }
        for(int[] r:grid){
            for(int c:r){
                if(c == 1) return -1;
            }
        }
        return ans;
    }
}
~~~

## 207.课程表(拓扑排序解决)

你这个学期必须选修 `numCourses` 门课程，记为 `0` 到 `numCourses - 1` 。

在选修某些课程之前需要一些先修课程。 先修课程按数组 `prerequisites` 给出，其中 `prerequisites[i] = [ai, bi]` ，表示如果要学习课程 `ai` 则 **必须** 先学习课程 `bi` 。

- 例如，先修课程对 `[0, 1]` 表示：想要学习课程 `0` ，你需要先完成课程 `1` 。

请你判断是否可能完成所有课程的学习？如果可以，返回 `true` ；否则，返回 `false` 。

~~~java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int[] degree = new int[numCourses];
        List<List<Integer>> g = new ArrayList<>();
        for(int i=0;i<numCourses;i++){
            g.add(new ArrayList());
        }
        for(int[] e:prerequisites){ //邻接表
            int a = e[0],b=e[1];//b->a;
            g.get(b).add(a);
            degree[a]++;//存的是入度
        }        
        //拓扑排序
        Queue<Integer> q=new LinkedList<>();
        for(int i=0;i<numCourses;i++){
            if(degree[i] == 0){
                q.offer(i);//把度为0的入队
            }
        }
        int count = 0;
        while(!q.isEmpty()){
            int t = q.poll();
            count++;
            for(int i:g.get(t)){
                if(--degree[i] == 0){
                    q.offer(i);
                }
            }
        }
        return count == numCourses;

    }
}
~~~

## 208.实现Trie(前缀树)

**Trie**（发音类似 "try"）或者说 **前缀树** 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。

请你实现 Trie 类：

- `Trie()` 初始化前缀树对象。
- `void insert(String word)` 向**前缀树**中插入字符串 `word` 。
- `boolean search(String word)` 如果字符串 `word` 在前缀树中，返回 `true`（即，在检索之前已经插入）；否则，返回 `false` 。
- `boolean startsWith(String prefix)` 如果**之前已经插入的字符串** `word` 的前缀之一为 `prefix` ，返回 `true` ；否则，返回 `false` 。

~~~java
class Trie {
    private Trie[] children;
    private boolean isEnd;
    public Trie() {
        children = new Trie[26];
        isEnd = false;
    }
    public void insert(String word) {
        Trie node = this;
        for(int i=0;i<word.length();i++){
            char ch = word.charAt(i);//对每个字符进行解析
            int index = ch - 'a';//求出每个字符的下标
            if(node.children[index] == null){ //子节点不存在 创建一个新的子节点
                node.children[index] = new Trie();                
            }
            //子节点存在，移动到字节点            
            node = node.children[index];
        }
        //令子节点结尾为真
        node.isEnd = true;
    }
    public boolean search(String word) {
        Trie node = searchPrefix(word);
        return node != null && node.isEnd;
    }
    
    public boolean startsWith(String prefix) {
        Trie node = searchPrefix(prefix);
        return node != null;
    }
    private Trie searchPrefix(String prefix){
        Trie node = this;
        for(int i=0;i<prefix.length();i++){
            char ch = prefix.charAt(i);
            int index = ch - 'a';
            if(node.children[index] == null){
                return null;                
            }
            node = node.children[index];
        }
        return node;        
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
~~~

# 回溯

## 46.全排列

给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

~~~java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    boolean st[] = new boolean[6];
    void dfs(int[] nums,int u){
        int n=nums.length;
        if(u == n) {
            ans.add(new ArrayList<>(path));//进u是放u这个下标的数
            return ;
        }
        for(int i=0;i<n;i++){
            if(!st[i]){
                path.add(nums[i]);
                st[i] = true;
                dfs(nums,u+1);
                st[i] = false;
                path.remove(path.size()-1);
            }
        }

    }
    public List<List<Integer>> permute(int[] nums) {
        int n= nums.length;
        dfs(nums,0);        
        return ans;
    }
}
~~~

## 78.子集

给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集(幂集)。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

~~~cpp
class Solution {
    void dfs(int[] nums,int start,List<Integer> path,List<List<Integer>> res){
        res.add(new ArrayList<>(path));//结果
        for(int i = start;i<nums.length;i++){
            path.add(nums[i]);//增加到数组中
            dfs(nums,i+1,path,res);//递归调用
            path.remove(path.size()-1);//删除掉最后一个重新进行匹配
        }
    }
    public List<List<Integer>> subsets(int[] nums) {
        List<Integer> path = new ArrayList<>();
        List<List<Integer>> res = new ArrayList<>();
        dfs(nums,0,path,res);
        return res;
    }
}
~~~

## 17.电话号码的字母组合

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母

~~~java
class Solution {
    public List<String> res = new ArrayList<>();
    public String path="";
    String[] a = {
            "","","abc","def",
            "ghi","jkl","mno",
            "pqrs","tuv","wxyz",
    };
    void fun(String digits,int u,String path){
        if(u == digits.length()) res.add(path);
        else{
            String list = a[digits.charAt(u) - '0'];
            for(char c:list.toCharArray()){
                fun(digits,u+1,path+c);
            }
        }
    }
    public List<String> letterCombinations(String digits) {
        if(digits == "" || digits.length() == 0) return res;
        fun(digits,0,path);
        return res;
    }
}
~~~

## 39.组合总和

给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。

`candidates` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 `target` 的不同组合数少于 `150` 个。

~~~java
class Solution {
    public List<List<Integer>> ans = new ArrayList<>();
    public List<Integer> path = new ArrayList<>();
    void fun(int[] candidates,int u,int target){
        if(target == 0) {
            ans.add(new ArrayList<>(path));
            return ;
        }
        if(u == candidates.length) return;
        for(int i=0;i*candidates[u] <= target;i++){
            //添加i次 
            for(int j=0;j<i;j++){ 
                path.add(candidates[u]);
            }
            fun(candidates,u+1,target-i*candidates[u]);
            //删除最后一个i次
            for(int j=0;j<i;j++){
                //移除path数组i次
                path.remove(path.size()-1);
            }
        }
    }
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        fun(candidates,0,target);
        return ans;
    }
    
}
~~~

## 22.括号生成(左括号永远大于右括号，先递归左再递归右)

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

**示例 1：**

```
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2：**

```
输入：n = 1
输出：["()"]
```

~~~java
class Solution {
    public  List<String> res = new ArrayList<>();
    public  void fun(String s,int l,int r,int n){
        if(l<n) fun(s+'(',l+1,r,n); //左括号优先放
        if(r<n && r<l) fun(s+')',l,r+1,n);//右括号比左括号个数少
        if(s.length() == 2*n){
            res.add(s);
        }
    }
    public List<String> generateParenthesis(int n) {
        fun("",0,0,n);//
        return res;
    }
}
~~~

## 79.单词搜索

给定一个 `m x n` 二维字符网格 **`board`** 和一个字符串单词 `word` 。如果 `word**` 存在于网格中，返回 `true` ；否则，返回 `false` 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

~~~java
class Solution {
    int dx[] = {-1,0,1,0};
    int dy[] = {0,1,0,-1};
    boolean[][] bl = new boolean[6][6];
    boolean dfs(char[][] board,int x,int y,int count,int n,String word){
        bl[x][y] = true;
        if(count == n) return true;
        for(int i=0;i<4;i++){
            int a=x+dx[i],b=y+dy[i];
            if(a>=0 && a<board.length &&b>=0 && b<board[0].length && !bl[a][b]){
                if(board[a][b] == word.charAt(count)){ //相等
                    bl[a][b] =true;
                    if(dfs(board,a,b,count+1,n,word)) return true;
                }
                bl[a][b] = false;
            }
        }
        bl[x][y] =false;
        return false;        
    }
    public boolean exist(char[][] board, String word) {
        int row = board.length;
        int col = board[0].length;
        int n = word.length();
        if(row*col < word.length()) return false;
        for(int i=0;i<row;i++){
            for(int j=0;j<col;j++){
                if(board[i][j] == word.charAt(0)){
                    //进去就是从第二个位置开始查询
                    if(dfs(board,i,j,1,n,word)){
                        return true;
                    }
                }
            }
        }
        return false; 
    }
}
~~~

## 131.分割回文串

给你一个字符串 `s`，请 你将 `s` 分割成一些子串，使每个子串都是 **回文串** 。返回 `s` 所有可能的分割方案。

**示例 1：**

```
输入：s = "aab"
输出：[["a","a","b"],["aa","b"]]
```

**示例 2：**

```
输入：s = "a"
输出：[["a"]]
```

~~~java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> result = new ArrayList<>();
        dfs(s,0,new ArrayList<>(),result);
        return result;
    }
    public void dfs(String s,int startIndex,List<String> cur,List<List<String>> result){
        if(startIndex == s.length()){
            result.add(new ArrayList<>(cur));
            return;
        }
        for(int i=startIndex;i<s.length();i++){
            String substring = s.substring(startIndex,i+1);//左闭右开区间
            if(isPallindrome(substring)){
                //如果字串是回文串
                cur.add(substring);
                dfs(s,i+1,cur,result);//从下一个点开始遍历
                cur.remove(cur.size()-1);
            }
        }
    }
    public boolean isPallindrome(String s){
        //判断一个字符串是否是回文串
        int left = 0;
        int right = s.length()-1;
        while(left<right){
            if(s.charAt(left) != s.charAt(right)) return false;
            left++;right--;
        }
        return true;
    }
}
~~~

## 51.N皇后 

按照国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回所有不同的 **n 皇后问题** 的解决方案。

每一种解法包含一个不同的 **n 皇后问题** 的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

~~~java
class Solution {
    List<List<String>> ans = new ArrayList<>();
    private int n;
    private int[] col;
    private boolean[] onPath,diag1,diag2;
    public List<List<String>> solveNQueens(int n) {
        this.n = n;
        col = new int[n];
        onPath = new boolean[n];
        diag1 = new boolean[n*2-1];
        diag2 = new boolean[n*2-1];
        dfs(0);
        return ans;
    }
    private void dfs(int r){
        if(r == n){
            List<String> board = new ArrayList<>();
            for(int c:col){ //每一列进行遍历
                char[] row = new char[n];
                Arrays.fill(row,'.');
                row[c] = 'Q';//第n行的数据
                board.add(new String(row));//一整行数据                
            }
            ans.add(board);
            return;
        }
        for(int c=0;c<n;c++){ //左到右扫描一整行的数据
            //从第一列开始
            int rc = r-c+n-1;//第一列不为真 onPath是指这一列是否已经放过了
            if(!onPath[c] && !diag1[r+c] && !diag2[rc]){
                col[r] = c;
                onPath[c] = diag1[r+c] = diag2[rc] =true;
                dfs(r+1);//进入到下一行
                onPath[c] = diag1[r+c] = diag2[rc] = false; //恢复现场
            }
        }
    }
}
~~~

##  





















