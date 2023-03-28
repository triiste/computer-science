

[TOC]



# 力扣 TOP 100

## 1653. 使字符串平衡的最少删除次数

给你一个字符串 s ，它仅包含字符 'a' 和 'b' 。

你可以删除 s 中任意数目的字符，使得 s 平衡 。当不存在下标对 (i,j) 满足 i < j ，且 s[i] = 'b' 的同时 s[j]= 'a' ，此时认为 s 是 平衡 的。

请你返回使 s 平衡 的 最少 删除次数。

示例 1：

~~~
输入：s = "aababbab"
输出：2
解释：你可以选择以下任意一种方案：
下标从 0 开始，删除第 2 和第 6 个字符（"aababbab" -> "aaabbb"），
下标从 0 开始，删除第 3 和第 6 个字符（"aababbab" -> "aabbbb"）。
~~~

示例 2：

~~~
输入：s = "bbaaaaabb"
输出：2
解释：唯一的最优解是删除最前面两个字符。
~~~

~~~cpp
//分析
/*
	1.所以的a只能在所有的b左边
	2.要么是a 要么是b
	求调整次数
*/
//方法一 DP
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    //左边a右边b
    int minimumDeletions(string s) {
        int countb=0,dp=0;//令dp表示s[0,i]变平衡最少需要删除的次数
        for(char c:s){
            if(c == 'a'){
                /*
                 * // 如果末尾是a,有两个选择:
                 *       1.删除当前的a 将s[0,i-1]变平衡
                 *       2.保留当前的a,删除之前的所有b
                 */
                dp=min(dp+1,countb);
            }else{
                //如果末尾是b,不需要进行操作，只需要统计b的数
                countb++;
            }
        }
        return  dp;
    }
};
int main(){
  Solution a;
  string s;
  cin>>s;
  cout<<a.minimumDeletions(s)<<endl;
}
~~~

~~~cpp
/**
方法二:
枚举分界线法
找到分界线:

**/
    int minimumDeletions(string s) {
        int rightA = 0, leftB = 0;
        int res;
        for (int i = 0; i < s.length(); i++) {
            if (s[i] == 'a') rightA++;
        }
        res = rightA;
        for (int i = 0; i < s.length(); i++) {
            if (s[i] == 'a') rightA--;
            if (s[i] == 'b') leftB++;
            res=min(res,rightA+leftB);
        }
        return res;
    }
~~~

## 543. 二叉树的直径

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

**示例 :**
给定二叉树

```
          1
         / \
        2   3
       / \     
      4   5    
```

返回 **3**, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。

**注意：**两结点之间的路径长度是以它们之间边的数目表示。

~~~cpp
//找到两条边最深的即可
/**
方法：
	任何一条路径存在最高点，枚举树中每个点作为最高点，一定能找到最大值。
	找到左边最深和右边最深相加即可。
	枚举所有点找到最大值
**/


/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int ans=0;
    int diameterOfBinaryTree(TreeNode* root) {
        dfs(root);
        return ans;
    }
    int dfs(TreeNode * root){
        if(!root) return 0;
           int left=dfs(root->left);
           int right=dfs(root->right);
        ans=max(ans,left+right);
        return max(left,right)+1;
    }
};
~~~

~~~cpp
//DFS 模板 求排列数字
#include<bits/stdc++.h>
using namespace std;
const int N=10;
bool st[N];
void dfs(int u){
    if(u==n){
        for(int i=0;i<n;i++){
            cout<<path[i]<<" ";
        }
        cout<<endl;
    	return;
    }
    for(int i=1;i<=n;i++){
        if(!st[i]){
            path[u]=i;
            st[i]=true;
            dfs(u+1);
            st[i]=false;
            path[u]=0;
        }
    }
    int main(){
        cin>>n;
        dfs(9);
        return 9;
    }              
    
~~~

## 617. 合并二叉树

给你两棵二叉树： root 1 和 root 2 。

想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，不为 null 的节点将直接作为新二叉树的节点。

返回合并后的二叉树。

注意: 合并过程必须从两个树的根节点开始。

**示例 1：**

![示例图片](./images/1.png)

~~~shell
输入：root1 = [1,3,2,5], root2 = [2,1,3,null,4,null,7]
输出：[3,4,5,5,4,null,7]
~~~

**示例 2：**

```
输入：root1 = [1], root2 = [1,2]
输出：[2,2]
```

~~~cpp
/**
	深搜 三种情况
	都为空 -->合并后为空
	一个空一个不空 -->合并后为非空的
	两个都不空-->两者之和
**/


/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
		//相当于先序遍历一遍
        if(root1 ==nullptr)          
                return root2; //左为空保留右边
        if(root2 ==nullptr)
                return root1;//右边为空保留左边
        auto merged=new TreeNode(root1->val+root2->val); //相加
        merged->left  =mergeTrees(root1->left,root2->left); //往左走
        merged->right =mergeTrees(root1->right,root2->right); //往右走
        return merged;      
    }
};
~~~

## 283. 移动零

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**请注意** ，必须在不复制数组的情况下原地对数组进行操作。

**示例 1:**

~~~cpp
输入: nums = [0,1,0,3,12]
输出: [1,3,12,0,0]
~~~

**示例 2:**

~~~cpp
输入: nums = [0]
输出: [0]
~~~

**提示**:

- `1 <= nums.length <= 104`
- `-2^31 <= nums[i] <= 2^31 - 1`

~~~cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int j=0,s=0;
        for(int i=0;i<nums.size();i++){
            if(nums[i]!=0){
                nums[j++]=nums[i];
            }
            else{
                s++;
            }
        }
        for(int i=nums.size()-1;i>=nums.size()-s;i--){
            if(i<0)
                break;
            nums[i]=0;
        }
    }
};
~~~

## 338.比特位计数

给你一个整数 `n` ，对于 `0 <= i <= n` 中的每个 `i` ，计算其二进制表示中 **`1` 的个数** ，返回一个长度为 `n + 1` 的数组 `ans` 作为答案。

**示例 1：**

~~~
输入：n = 2
输出：[0,1,1]
解释：
0 --> 0
1 --> 1
2 --> 10
~~~

**示例 2：**

~~~
输入：n = 5
输出：[0,1,1,2,1,2]
解释：
0 --> 0
1 --> 1
2 --> 10
3 --> 11
4 --> 100
5 --> 101
~~~

**提示：**

- `0 <= n <= 105`

~~~cpp
//acwing 上的题解
//位运算的操作
/*
	求n的第k位数字:n>>k&1
	n的二进制表示中第k位是几
	n=15=(1111)2
	1.先把第k位移到最后一位 n>>k
	2.看个位是几 x&1 即可
	
	
	返回n的最后一位1:lowbit(n) = n & -n
	x=1010  lowbit(x)=10
	x=101000 lowbit(x)=1000
	
		-x 相当于~x+1
		x& -x 相当于 x&(~x+1) 
		 x=101000
		~x=010111
		~x+1=011000
		x & -x =1000
*/
~~~

~~~cpp
class Solution {
public:
    int lowbit(int x) {
        return x & -x;
    }
    vector<int> countBits(int n) {
        int res = 0;
        vector<int> ss(n + 1);
        for (int i = 0; i <= n; i++) {
//            计算二进制中1的个数
            int t = i;
            res = 0;
            while (t) {
                t -= lowbit(t);
                res++;
                if (ss[t] != 0) {
                    res += ss[t];
                    break;
                }
            }
            ss[i] = res;
        }
        return ss;
    }
};
~~~

## 20.有效的括号

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。

**示例 1：**

~~~
输入：s = "()"
输出：true
~~~

**示例 2：**

~~~
输入：s = "()[]{}"
输出：true
~~~

**示例 3：**

~~~
输入：s = "(]"
输出：false
~~~

~~~
提示：

1 <= s.length <= 104
s 仅由括号 '()[]{}' 组成
~~~

~~~cpp
class Solution{
public:
    bool isValid(string s){
        //数组模拟栈
        if(s.size()%2!=0){
            return false;
        }
        stack<char> sstack;
        int len=s.size();
        for (int i = 0; i < len; i++) {
            if (s[i] == ')' && !sstack.empty() && sstack.top() =='(') {
                sstack.pop();
            } else if (s[i] == '}' && !sstack.empty() && sstack.top() =='{') {
                sstack.pop();
            } else if (s[i] == ']' && !sstack.empty() && sstack.top() =='[') {
                sstack.pop();
            } else {
                sstack.push(s[i]);
            }
        }
        return sstack.empty();
    }
};
~~~

## 70.爬楼梯

假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。

每次你可以爬 `1` 或 `2` 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**示例 1：**

~~~
输入：n = 2
输出：2
解释：有两种方法可以爬到楼顶。
1. 1 阶 + 1 阶
2. 2 阶
~~~

**示例 2：**

~~~
输入：n = 3
输出：3
解释：有三种方法可以爬到楼顶。
1. 1 阶 + 1 阶 + 1 阶
2. 1 阶 + 2 阶
3. 2 阶 + 1 阶
~~~

**提示：**

- `1 <= n <= 45`

~~~cpp
/**
台阶数：0 1 2  3  4  5  6  7  8
方案数：1 1 2  3  5  8 13 21 34
**/
class Solution {
public:
    int climbStairs(int n) {
        int a[46];
        a[1]=1,a[0]=1;
        for(int i=2;i<=n;i++){
            a[i]=a[i-1]+a[i-2];
        }    
        return a[n];
    }
};
~~~

## 448. 找到所有数组中消失的数字

给你一个含 n 个整数的数组 nums ，其中 nums[i] 在区间 [1, n] 内。请你找出所有在 [1, n] 范围内但没有出现在 nums 中的数字，并以数组的形式返回结果。

**示例 1：**

```
输入：nums = [4,3,2,7,8,2,3,1]
输出：[5,6]
```

**示例 2：**

~~~
输入：nums = [1,1]
输出：[2]
~~~


提示：

- `n == nums.length`
- `1 <= n <= 105`
- `1 <= nums[i] <= n`

~~~cpp
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int> &nums) {
        vector<bool> n(1e5+10);
        vector<int> res;
        for (int i = 1; i <= nums.size(); i++) {
            n[i]=false;
        }
        for (int i = 0; i < nums.size(); i++) {
            n[nums[i]]=true;
        }
        for(int i=1;i<=nums.size();i++){
            if(!n[i])
                res.push_back(i);
        }
        return res;
    }
};
~~~

~~~cpp
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int> &nums) {

        for(auto x:nums){
            x=abs(x); //x相当于是nums[i]
            if(nums[x-1]>0) nums[x-1]*=-1;//为啥要改变成负的 因为后面会取绝对值 不会丢失原数据
        }
        vector<int> res;
        for(int i=0;i<nums.size();i++){
            if(nums[i]>0)
            res.push_back(i+1);
        }
        return res;
    }
};
~~~

## 21.合并两个有序链表

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的

**示例1**

~~~
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
~~~

**示例2**

~~~
输入：l1 = [], l2 = []
输出：[]
~~~

**示例3**

~~~
输入：l1 = [], l2 = [0]
输出：[0]
~~~

~~~cpp
class Solution {
public:
    ListNode *mergeTwoLists(ListNode *list1, ListNode *list2) {
        int i,j;
        // 1 2 4
        // 1 3 4
        ListNode *p,*q,*x;
        ListNode *t=new ListNode();
        x=t;
        p=list1;q=list2;

        while (p != nullptr && q != nullptr) {
            if (p->val <= q->val) {

                x->next = p;//指向自身了
                p = p->next;
            } else {
                x->next = q;
                q = q->next;
            }
            x = x->next;
        }
        if(p!=nullptr){
            x->next=p;
        }else{
            x->next=q;
        }

        t=t->next;
        ListNode* r=t;
//        while(r != nullptr){
//            cout<<r->val<<" ";
//            r=r->next;
//        }
//        cout<<endl;
        return t;
    }
};
~~~

## 94.二叉树的中序遍历

给定一个二叉树的根节点 `root` ，返回 *它的 **中序** 遍历* 。

**示例1**

~~~
输入：root = [1,null,2,3]
输出：[1,3,2]
~~~

**示例2**

~~~
输入：root = []
输出：[]
~~~

**示例3**

~~~
输入：root = [1]
输出：[1]
~~~

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> bianli;
    vector<int> inorderTraversal(TreeNode* root) {
        if(root!= nullptr){
            inorderTraversal(root->left);
            bianli.push_back(root->val);
            inorderTraversal(root->right);
        }
        return bianli;
    }
};
~~~

## 136.只出现一次的数字

给你一个 非空 整数数组 nums ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。

**示例 1 ：**

```
输入：nums = [2,2,1]
输出：1
```

**示例 2 ：**

```
输入：nums = [4,1,2,1,2]
输出：4
```

**示例 3 ：**

```
输入：nums = [1]
输出：1
```

~~~cpp
//个人粗暴解法
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int x=0;
        vector<int> a(1e5+10,0);
        for(int i=0;i<nums.size();i++){
            nums[i]+=3e4+10;
        }
        for(int i=0;i<nums.size();i++){
                a[nums[i]]++;
        }
        for(int i=0;i<nums.size();i++){
            if(a[nums[i]]==1){
              x=nums[i];
                break;
            }
        }
        x-=3e4+10;
        return x;
    }
};
~~~

~~~cpp
//异或操作即可 
//相同的数异或为0
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int t=nums[0];
        for(int i=1;i<nums.size();i++){
            t=t^nums[i];
        }
        return t;
    }
};

~~~

## 101.对称二叉树

给你一个二叉树的根节点 `root` ， 检查它是否轴对称。

**示例 1：**

```
输入：root = [1,2,2,3,4,4,3]
输出：true
```

**示例 2：**

```
输入：root = [1,2,2,null,3,null,3]
输出：fals
```

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        return dfs(root->left,root->right);
    }
    bool dfs(TreeNode *p,TreeNode *q){
        if(!p&&!q) return true;
        if(!p || !q ||p->val !=q->val) return false;
        return dfs(p->left,q->right) &&dfs(p->right,q->left);
    }
};
~~~

## 104.二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

**说明:** 叶子节点是指没有子节点的节点。

**示例：**

~~~
给定二叉树 `[3,9,20,null,null,15,7]`，

返回它的最大深度 3 。
~~~

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int maxDepth(TreeNode* root) {
        return root? max(maxDepth(root->left),maxDepth(root->right))+1:0;
    }
};
~~~

## 121.买卖股票的最佳时机

给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

**示例 1：**

~~~
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
~~~

**示例 2：**

```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。
```

~~~c++
//一次遍历价格数组（i），每天更新当前历史最低点(minPrice)，更新当前最大利润（maxProfit = prices[i] - minPrices）。
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int min,maxprofit=0;
            min=prices[0];
        for(int i=0;i<prices.size();i++){        
            //求出当前买入的最低点
            if(min>prices[i]) min=prices[i];
            if(prices[i] -min>maxprofit) maxprofit=prices[i] -min;
        }
        return maxprofit;
    }
};
~~~

## 169.多数元素

给定一个大小为 n 的数组 nums ，返回其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**示例 1：**

```
输入：nums = [3,2,3]
输出：3
```

**示例 2：**

```
输入：nums = [2,2,1,1,1,2,2]
输出：2
```

~~~cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int r,c=0;
        for(auto x:nums){
            if(!c) r=x,c=1;
            else if(r!=x) c--;
            else c++;
        }
        return r;
    }
};
~~~

## 141.环形链表

给你一个链表的头节点 head ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。注意：pos 不作为参数进行传递 。仅仅是为了标识链表的实际情况。

如果链表中存在环 ，则返回 true 。 否则，返回 false 。

**示例 1：**

~~~
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
~~~

**示例 2：**

~~~
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
~~~

**示例3：**

~~~
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
~~~

~~~cpp
//快慢指针
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
        if(!head || !head->next) return false;
        auto l=head,f=head->next;        
        while(f){
            l=l->next,f=f->next;
            if(!f) return false;
            f=f->next;
            if(l == f) return true;
        }
        return false;    
    }
};
~~~

## 160.相交链表

给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 null 。

图示两个链表在节点 c1 开始相交：

题目数据 **保证** 整个链式结构中不存在环。

**注意**，函数返回结果后，链表必须 **保持其原始结构** 。

自定义评测：

评测系统 的输入如下（你设计的程序 不适用 此输入）：

intersectVal - 相交的起始节点的值。如果不存在相交节点，这一值为 0
listA - 第一个链表
listB - 第二个链表
skipA - 在 listA 中（从头节点开始）跳到交叉节点的节点数
skipB - 在 listB 中（从头节点开始）跳到交叉节点的节点数
评测系统将根据这些输入创建链式数据结构，并将两个头节点 headA 和 headB 传递给你的程序。如果程序能够正确返回相交节点，那么你的解决方案将被 视作正确答案 。

~~~
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
输出：Intersected at '8'
解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,6,1,8,4,5]。
在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
— 请注意相交节点的值不为 1，因为在链表 A 和链表 B 之中值为 1 的节点 (A 中第二个节点和 B 中第三个节点) 是不同的节点。换句话说，它们在内存中指向两个不同的位置，而链表 A 和链表 B 中值为 8 的节点 (A 中第三个节点，B 中第四个节点) 在内存中指向相同的位置。
~~~

~~~cpp
//不管是否相交两个指针最终都能碰到


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
        auto a=headA,b=headB;
        while(a!=b){
            a=a?a->next:headB;
            b=b?b->next:headA;
        }
        return a;

    }
};
~~~

## 206.反转链表

给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。

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
        ListNode *p,*q=head;
        ListNode *r=new ListNode(0);
        r->next=nullptr;
        while(q){
            //头插法的两步
            p=q->next;    
            q->next=r->next;
            r->next=q;
            q=p;
        }
        return r->next;

    }
};
~~~

##  234.回文链表

给你一个单链表的头节点 `head` ，请你判断该链表是否为回文链表。如果是，返回 `true` ；否则，返回 `false` 。

 ~~~cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        ListNode *p,*q=head,*s=head;
        vector<int> shunxu;
        if(!q) return true;
        //先把原链表顺序保存起来
        while(s){
            shunxu.push_back(s->val);
            s=s->next;
        }
        ListNode *r=new ListNode(0);
        r->next=nullptr;
        while(q){
            //头插法的两步
            p=q->next;
            q->next=r->next;
            r->next=q;
            q=p;
        }
        ListNode *n=r->next;
        for(int i=0;i<shunxu.size();i++){
            if(shunxu[i] != n->val){
                return false;
            }
            n=n->next;
        }
        return true;

    }
};
 ~~~

## 226.翻转二叉树

给你一棵二叉树的根节点 `root` ，翻转这棵二叉树，并返回其根节点。

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(!root) return nullptr;
        swap(root->left,root->right);
        invertTree(root->left);
        invertTree(root->right);
        return root;
    }
};
~~~

## 461.汉明距离

两个整数之间的汉明距离指的是这两个数字对应二进制位不同的位置的数目。

给你两个整数 `x` 和 `y`，计算并返回它们之间的汉明距离。

**示例 1：**

~~~
输入：x = 1, y = 4
输出：2
解释：
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
上面的箭头指出了对应二进制位不同的位置。
~~~

~~~cpp
//acwing 上的题解
//位运算的操作
/*
	求n的第k位数字:n>>k&1
	n的二进制表示中第k位是几
	n=15=(1111)2
	1.先把第k位移到最后一位 n>>k
	2.看个位是几 x&1 即可
	
	
	返回n的最后一位1:lowbit(n) = n & -n
	x=1010  lowbit(x)=10
	x=101000 lowbit(x)=1000
	
		-x 相当于~x+1
		x& -x 相当于 x&(~x+1) 
		 x=101000
		~x=010111
		~x+1=011000
		x & -x =1000
*/
~~~

~~~cpp
class Solution {
public:
    int hammingDistance(int x, int y) {
        int count=0;
        while(x || y){
            if((x&1) != (y&1)){
                count++;
            }
            x=x>>1;
            y=y>>1;

        }
        return count;
    }
};
~~~

##  75. 颜色分类

给定一个包含红色、白色和蓝色、共 n 个元素的数组 nums ，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

必须在不使用库内置的 sort 函数的情况下解决这个问题。

**示例 1：**

```
输入：nums = [2,0,2,1,1,0]
输出：[0,0,1,1,2,2]
```

~~~cpp
/*
三指针算法
要求:
0-j-1 为0
j-i-1 为1
k+1-n-1 为2
i~k  不清晰
*/
~~~

~~~cpp
if(ai==0) swap(ai,aj) i++,j++; 
if(ai==1) i++;
if(ai==2) swap(ai,ak) k--; //不一定是1 所以i不往后加
~~~

~~~cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int i=0,j=0,k=nums.size()-1;
        while(i<=k){
            if(nums[i] == 0){
                swap(nums[i],nums[j]);
                i++;j++;
            }else if(nums[i] == 2){
                swap(nums[i],nums[k]);
                k--;
            }else if(nums[i] == 1){
                i++;
            }
        }
        
    }
};
~~~

## 146.LRU缓存

请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：
LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

**示例：**

~~~
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
~~~

~~~cpp
class LRUCache {
public:
     
    LRUCache(int capacity) {

    }
    
    int get(int key) {

    }
    
    void put(int key, int value) {

    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
~~~





















