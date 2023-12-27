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



