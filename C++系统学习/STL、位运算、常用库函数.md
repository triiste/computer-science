## 容器

### 1.vector

~~~#c++
#include<vector>
//变长数组

vector<int> a;
vector<int> b[233]; //二维数组
vector<int> a({1,2,3});
struct Rec
{
	int x,y; n  
};
vector<Rec> c;



//长度
a.size();

//判空,是空的话返回true
a.empty();

//当前数组清空
a.clear();
  


//迭代器，左闭右开
 vector<int>::iterator it=a.begin();
a.begin()//a的第一个元素地址    
*a.begin()//访问的是a[0]元素
it+2 //访问的是地址a[2];
it   //访问的是地址a[0];    
a.end//a的最后一个位置的下一个位置地址    
    
//遍历vector数组
    for(int i=0;i<a.size();i++)
    cout<<a[i]<<endl;
    for(vector<int>::iterator it=a.begin();it!=a.end();it++)
            cout<<*it<<endl;
    for(int x:a) cout<<x<<endl;


//查元素
a.front()等价于a[0]等价于*a.begin()
a.back()等价于a[a.size()-1]等价于*(a.end()-1)

//改元素
a.push_back(x);//往最后一个加元素
a.pop_back();//删除最后一个元素


~~~

### 2.queue

  ~~~c++
  #include<queue>
  
  queue<int> q;
  queue<double> q;
  priority_queue<int> q;//默认大根堆
  priority_queue<int,vector<int>,greater<int>> b;//小根堆
  priority_queue<pair<int,int>> q;
  struct Rec
  {
  	int a,x,y;
  }
  queue<Rec> b;
  
  
  q.push(1) //在队头插入一个元素
  q.pop() //弹出队尾元素
  q.front() //返回队头
  q.back()  //返回队尾 
  
  //清空队列
  q=queue<int>();
  ~~~

### 3.stack

~~~c++
#include<stack>
stack<int> stk;
stk.push(1);
stk.top();//返回栈顶元素
stk.pop();//删除栈顶元素
~~~

### 4.deque

~~~c++
//双端队列
dequeue<int> q;
q.begin(),q.end();
q.front(),q.back();
q.push_back(1);//最后插入一个元素
q.push_front(2);//开头插入一个元素
q[0];//随机访问一个元素
q.pop_back(),q.pop_front()//弹出最后一个元素和弹出第一个元素
q.clear();//清空队列
~~~

### 5.set

~~~
#include<set>
//不支持随机访问
set<int> a;//不能包含重复元素
multiset<int> b;//元素可以重复
a.size();
a.empty();
a.clear();
a.insert(x);//插入x
a.find(x);//查找x\
if(a.find()==a.end())  //判断x在a中是否存在

~~~

## 函数

### 1.位运算

~~~c++
求x的第k位数字  x >> k & 1
lowbit(x) = x & -x，返回x的最后一位1
~~~

### 2.常用库函数

~~~c++
#include<algorithm>
1)reverse 翻转
    翻转一个vector
    reverse(a.begin(),a.end);
	翻转一个数组，元素存放在下标0~n-1
    reverse(a,a+n);
2)unique 去重
    把一个vector去重：
    int m = unique(a.begin(), a.end()) – a.begin();
    把一个数组去重，元素存放在下标1~n：
    int m = unique(a, a+n) – (a);
3)random_shuffle 随机打乱
    用法与reverse相同
4) sort
bool cmp(int a, int b) {return a > b; }
sort(a, a+n, cmp);


~~~









