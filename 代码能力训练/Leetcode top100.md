

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

![示例图片](.\images\1.png)

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







