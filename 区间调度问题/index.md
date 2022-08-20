# 区间调度算法


<!--more-->

# 1.最多区间调度问题

![image-20220820225419834](https://cdn.staticaly.com/gh/Hongtao-Xu/note@main/img/202208202254896.png)

> 问题的目标是求不重叠的最多区间个数，贪心算法求解：在可选的工作中，每次都选取结束时间最早的工作

```c++
const int MAX_N=100000;  
//输入  
int N,S[MAX_N],T[MAX_N];  
  
//用于对工作排序的pair数组  
pair<int,int> itv[MAX_N];  
  
void solve()  
{  
    //对pair进行的是字典序比较，为了让结束时间早的工作排在前面，把T存入first，//把S存入second  
    for(int i=0;i<N;i++)  
    {  
        itv[i].first=T[i];  
        itv[i].second=S[i];  
    }  
  
    sort(itv,itv+N);  
  
    //t是最后所选工作的结束时间  
    int ans=0,t=0;  
    for(int i=0;i<N;i++)  
    {  
        if(t<itv[i].second)//判断区间是否重叠  
        {  
            ans++;  
            t=itv[i].first;  
        }  
    }  
  
    printf(“%d\n”,ans);  
}  
```

时间复杂度：O(nlogn)

# 2.最大区间调度

![image-20220820230313721](https://cdn.staticaly.com/gh/Hongtao-Xu/note@main/img/202208202303769.png)

> 不重叠的累加区间长度，动态规划算法:按照结束时间排序区间，然后按照第i个区间选择与否进行动态规划



```c++
const int MAX_N=100000;  
//输入  
int N,S[MAX_N],T[MAX_N];  
  
//用于对工作排序的pair数组  
pair<int,int> itv[MAX_N];  
  
void solve()  
{  
    //对pair进行的是字典序比较，为了让结束时间早的工作排在前面，把T存入first，//把S存入second  
    for(int i=0;i<N;i++)  
    {  
        itv[i].first=T[i];  
        itv[i].second=S[i];  
    }  
  
    sort(itv,itv+N);  
  
    dp[0] = itv[0].first-itv[0].second;  
    for (int i = 1; i < N; i++)  
    {  
        int max;  
  
        //select the ith interval  
        int nonOverlap = lower_bound(itv, itv[i].second)-1;  
        if (nonOverlap >= 0)  
            max = dp[nonOverlap] + (itv[i].first-itv[i].second);  
        else  
            max = itv[i].first-itv[i].second;  
  
        //do not select the ith interval  
        dp[i] = max>dp[i-1]?max:dp[i-1];  
    }  
    printf(“%d\n”,dp[N-1]);  
}  
```

# 3.带权区间调度

> 在每个区间上绑定一个权重，求加权之后的区间长度最大值

```c++
const int MAX_N=100000;  
//输入  
int N,S[MAX_N],T[MAX_N];  
  
//用于对工作排序的pair数组  
pair<int,int> itv[MAX_N];  
  
void solve()  
{  
    //对pair进行的是字典序比较，为了让结束时间早的工作排在前面，把T存入first，//把S存入second  
    for(int i=0;i<N;i++)  
    {  
        itv[i].first=T[i];  
        itv[i].second=S[i];  
    }  
  
    sort(itv,itv+N);  
  
    dp[0] = (itv[0].first-itv[0].second)*V[0];  
    for (int i = 1; i < N; i++)  
    {  
        int max;  
  
        //select the ith interval  
        int nonOverlap = lower_bound(itv, itv[i].second)-1;  
        if (nonOverlap >= 0)  
            max = dp[nonOverlap] + (itv[i].first-itv[i].second)*V[i];  
        else  
            max = (itv[i].first-itv[i].second)*V[i];  
  
        //do not select the ith interval  
        dp[i] = max>dp[i-1]?max:dp[i-1];  
    }  
    printf(“%d\n”,dp[N-1]);  
}  
```

带权区间调度应用最广，最大区间调度其次，最多区间调度应用范围最小。从通用的DP到特殊的DP再到贪心算法，难度逐渐降低。下图展示了三个问题的关系：

<img src="https://cdn.staticaly.com/gh/Hongtao-Xu/note@main/img/202208202307377.png" alt="image-20220820230754322" style="zoom:80%;" />

