# 背包问题

## 01背包（朴素）

### 思路
**构建状态转移方程**
对于$f[i][j]$ (使用了前i个元素，背包容量为j时的所能的最大价值)
- 将情况分成使用了第i个元素使用，和没使用第i个元素
有
$f[i][j]$ = max($f[i -1][j]$ , $f[i -1][j - v[i]]$ + $w[i]$);

>$v[i]$为第i个元素的体积，$w[i]$为第i个元素的价值

**注意**
- 当出现 $v[i]$ > j 时，要变成 $f[i][j]$ = $f[i -1][j]$;
- 初始化数据时，从下标1开始，与$f[i][j]$定义相吻合
### 源码
```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int N = 1010;
int f[N][N];
int n, m;
int v[N], w[N];

int main(){
	cin >> n >> m;
	for(int i = 1; i <= n; i++){
		cin >> v[i] >> w[i];
	}
	for(int i = 1; i <= n; i++){
		for(int j  = 1; j <= m; j++){
			if(j >= v[i])f[i][j] = max(f[i - 1][j], f[i - 1][j - v[i]] + w[i]);
			else f[i][j] = f[i - 1][j];
		}	
	}
	cout << f[n][m];
	return 0;
} 
```

## 01背包（滚动数组优化）
### 思路
- 只是将上述二维数组优化为一维数组，减少占用内存

- 因为对于$f[i][j]$ = max($f[i -1][j]$ , $f[i -1][j - v[i]]$ + $w[i]$);，每次i更新都是使用i - 1的数据，所以考虑不使用i

**从原本的**

```cpp
for(int i = 1; i <= n; i++){
		for(int j  = 1; j <= m; j++){
			if(j >= v[i])f[i][j] = max(f[i - 1][j], f[i - 1][j - v[i]] + w[i]);
			else f[i][j] = f[i - 1][j];
		}	
	}
```

**到现在的**
**将$f[N][N]$改为$f[N]$

```cpp
for(int i = 1; i <= n; i++){
		for(int j  = m; j >= v[i]; j--){
			f[j] = max(f[j], f[j - v[i]] + w[i]);
		}	
	}
```

> 因为每次i更新都是i-1上迭代,所以可以直接将$[i]$删除,所以$f[i][j]$ = $f[i - 1][j]$可以直接被优化删除,但注意对于
```cpp
for(int j  >= v[i]; j <= m; j--){
			f[j] = max(f[j], f[j - v[i]] + w[i]);
		}
```

如果是从小到大进行遍历,那么因为 j > j - $v[i]$,$f[j - v[i]]$必然在$f[j]$前被更新,即为$f[j]$更新时取max 时用的是 $f[i][j - v[i]]$ 而不是 $f[i - 1][j - v[i]]$.所以j从大到小进行遍历,这样确保是对i-1数据进行迭代.



## 完全背包(朴素)

### 思路
- 和01背包问题相似
- 完全背包不同的地方在于每个物品的数量都有无线个,所以情况分为使用了0,1,2,3,4,5....次第i个物品
```cpp
for(int i = 1; i <= n; i++){
	for(int j = 1; j<= m; j++){
		for(int k = 0; k * v[i]<= j; k++){
			f[i][j] = max(f[i - 1][j], f[i - 1][j - k * v[i]] + k * w[i]);
		}
	}
}
cout << f[n][m] << endl;
```

看一下 f$[i][j]$ 和 $f[i][j - v[i]$ 的求解公式：

$f[i][j]$ = max( $f[i-1][j]$ , $f[i-1][j - v[i]]$ + $w[i]$ , $f[i-1][j - 2 * v[i]]$+ 2 * $w[i]$ , $f[i-1][j - 3 * v[i]]$ + 3 * $w[i]$ , .....)。
$f[i][j - v[i]]$ = max( $f[i-1][j - v[i]]$ , $f[i-1][j - 2 * v[i]]$ + $w[i]$, $f[i-1][j - 3 * v[i]]$ + 2 * $w[i]$ , .....) 。

由上两式，可得出如下递推关系：
$f[i][j]$ = max($f[i][j-v]$ + w , $f[i-1][j]$);

```cpp
for(int i = 1; i <= n; i ++ )
    {
        for(int j = 0; j <= m; j ++ )
        {
            if(v[i] <= j)//第 i 种能放进去
                f[i][j] =max(f[i - 1][j], f[i][j - v[i]] + w[i]);
            else//如果第 i 件物品不能放进去
                f[i][j] = f[i - 1][j];
        }
    }
```

### 源码
```cpp
#include<iostream>
using namespace std;

const int N = 1010;

int n, m;
int f[N][N], v[N], w[N];

int main()
{
    cin >> n >> m;
    for(int i = 1; i <= n; i ++ )
        cin >> v[i] >> w[i];
    for(int i = 1; i <= n; i ++ )
    {
        for(int j = 0; j <= m; j ++ )
        {
            if(v[i] <= j)
                f[i][j] =max(f[i - 1][j], f[i][j - v[i]] + w[i]);
            else
                f[i][j] = f[i - 1][j];
        }
    }
    cout << f[n][m] << endl;
}

```

## 完全背包（优化为滚动数组）
>和01背包问题优化很像

### 源码
```cpp
#include<iostream>
using namespace std;
const int N = 1010;
int f[N];
int v[N],w[N];
int main()
{
    int n,m;
    cin>>n>>m;
    for(int i = 1 ; i <= n ;i ++)
    {
        cin>>v[i]>>w[i];
    }

    for(int i = 1 ; i<=n ;i++)
    for(int j = v[i] ; j<=m ;j++)
    {
            f[j] = max(f[j],f[j-v[i]] + w[i]);
    }
    cout<<f[m]<<endl;
}

```

### 辨析
**对于01背包问题**
```cpp
for(int i = 1; i <= n; i++){
		for(int j  = m; j >= v[i]; j--){
			f[j] = max(f[j], f[j - v[i]] + w[i]);
		}	
	}
```
**对于完全背包问题**
```cpp
for(int i = 1 ; i<=n ;i++)
    for(int j = v[i] ; j<=m ;j++)
    {
            f[j] = max(f[j],f[j-v[i]] + w[i]);
    }
```


## 多重背包（朴素）
- 就是每种物品会有$s[i]$件，其他和01背包条件一样，做法和多重背包朴素写法一样
```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 110;

int n, m;
int v[N], w[N], s[N];
int f[N][N];

int main()
{
    cin >> n >> m;

    for (int i = 1; i <= n; i ++ ) cin >> v[i] >> w[i] >> s[i];

    for (int i = 1; i <= n; i ++ )
        for (int j = 0; j <= m; j ++ )
            for (int k = 0; k <= s[i] && k * v[i] <= j; k ++ )
                f[i][j] = max(f[i][j], f[i - 1][j - v[i] * k] + w[i] * k);

    cout << f[n][m] << endl;
    return 0;
}

```

> 注意这里是$k$ <= $s[i]$ `&&` $k$ * $v[i]$ <= $j$。

## 多重背包（二进制优化）

**对于完全背包**
```cpp
f[i, j] = max(f[i - 1, j], f[i - 1, j - v] + w, f[i - 1, j - 2v] + 2w, f[i - 1, j - 3v] + 3w, ......)

f[i, j - v] = max(f[i - 1, j - v], f[i - 1, j - 2v] + w, f[i - 1, j - 2v] + 2w, ......)

通过上述比较，可以得到 f[i][j] = max(f[i - 1][j], f[i][j - v] + w)。
```

**对于多重背包**
```cpp
f[i, j] = max(f[i - 1, j], f[i - 1, j - v] + w, f[i - 1, j - 2v] + 2w, ...... f[i - 1, j - Sv] + Sw)

f[i, j - v] = max(f[i - 1, j - v], f[i - 1, j - 2v] + w, ...... f[i - 1, j - Sv] + (S - 1)w, f[i - 1, j - (S + 1)v] + Sw)
```

并不能像完全背包那样进行化简

### 二进制

- 利用二进制，将多重背包转化为了01背包
- 原理：将每种物品（有$s[i]$件）打包为（1，2，4，8..件为一个整体的大物品（价值和容积都进行了叠加）；而任意一个实数可以由二进制数来表示。所以当要取1，2，3直到取到s时,二进制处理后每次拿出数字时最大不会超过log`2`S,进而优化了算法复杂度

> 如果仍然不是很能理解的话，取这样一个例子：要求在一堆苹果选出n个苹果。我们传统的思维是一个一个地去选，选够n个苹果就停止。这样选择的次数就是n次
 
 二进制优化思维就是：现在给出一堆苹果和10个箱子，选出n个苹果。将这一堆苹果分别按照1, 2, 4, 8, 16, .....512分到10个箱子里，那么由于任何一个数字x ∈ $[0, 1023]$ (第11个箱子才能取到1024，评论区有讨论这个)都可以从这10个箱子里的苹果数量表示出来，但是这样选择的次数就是 ≤ 10次。

比如：
- 如果要拿1001次苹果，传统就是要拿1001次；二进制的思维，就是拿7个箱子就行（分别是装有512、256、128、64、32、8、1个苹果的这7个箱子）,这样一来，1001次操作就变成7次操作就行了。

这样利用二进制优化，时间复杂度就从 O(`n³`) 降到 O(`n²logS`), 从 4 * 10⁹ 降到了 2 * 10⁷。

### 源码
```cpp
#include<iostream>
#include<algorithm>
using namespace std;
int cnt = 0;
const int N = 11000, V = 2010;
int n,m;
int a, b, s;//a为容积，b为价值，s为数量
int f[V];
int v[N], w[N];//转换为01背包后的容积和价值

int main(){
    cin >> n >> m;
    for(int i = 1; i <= n; i++){
        cin >> a >> b >> s;
        int k = 1;
        while(k <= s){
            cnt++;
            v[cnt] = a * k;
            w[cnt] = b * k;
            s -= k;
            k *= 2;
        }
        if(s > 0){
            cnt ++;
            v[cnt] = a*s; 
            w[cnt] = b*s;
        }
    }
    n = cnt;
    //转换为01背包
    for(int i = 1; i <= n; i++){
        for(int j = m; j >= v[i]; j--){
            f[j] = max(f[j], f[j - v[i]] + w[i]);
        }
    }
    cout << f[m] << endl;
    return 0;
}
```


## 分组背包
### 注意
**要深刻认识到背包问题的核心思路，对物品选取进行逐个考虑，在选与不选之间迭代，考虑当下到当前物品时，背包容积剩多少时的最大价值**

**对于01背包**
- 考虑选与不选第i - 1个元素，获得当前背包剩余不同容量时的最大价值
- 之后再考虑第i个元素时，利用上一轮获得的最大价值来进行迭代（比如将，选取了第i个元素：最大价值为 第i个元素价值 + 上一轮给出的容积为（总容积 - 第i个元素）的容积时的价值；和不选取第i个元素：最大价值仍为上一轮给出的总容积对应的最大价值）



**对于分组背包更是如此**

- 先考虑了前i -1组元素,获得了当前的不同容量下的最大价值
- 再选取第i组，逐步遍历不同容积下最优
- 在容积确定后，遍历组中所有元素，考虑选取每一个情况下的当前容积所能表现的最大值。
### 源码
```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int N = 110;
int n, m, k;
int f[N];
int s[i];//第i组有多少个物品
int v[N][N], w[N][N];//第i组第j个的物品的数据

int main(){
//初始化数据
	cin >> n >> m;//n表示n组，m为背包容量
	for(int i = 1; i <= n; i++){
		cin >> s[i];
		for(int j = 1; j <= s[i]; j++){
			cin >> v[i][j] >> w[i][j];
		}
	}
	
	for(int i = 1; i <= n; i++){
	//锁定第i组，遍历出考虑前面i组后，每个剩余容积下的背包最大价值
		for(int j = m; j >= 0; j--){
		//转化为了滚动数组，从大到小遍历，确保是与上一轮数据比较
			for(int k = 1; k <= s[i]; k++){
			//遍历考虑取用i组中的第k个物品，迭代更新f数组
				if(j >= v[i][k])
				f[j] = max(f[j], f[j - v[i][k] + w[i][k]);
			}
		}
	}
	cout << f[m] << endl;
	return 0;
}
```

**总结**
- 考虑第i组元素，想迭代获得最大值；那么就应该将前i -1组所有元素都考虑过（迭代过的）的每个容积下对应的最大值的数组更新出来**先定元素组**
- 而对于想确定每个容积下对应的最大值，则要固定容积，探查在第i组选取不同元素下的最后的最大值**二定容积**
- 最后才是遍历第i组下的每个元素**最后定组内元素**

> 关于迭代，第一层中的迭代是更新要处理的当前元素组。
> 第二层迭代是组内选取元素的迭代：是**当下容积最大价值**和**选取了元素，元素价值+剩余容积最大价值**的比较。

- 其实也可以理解为，第二层做的是基于上一层数据，考虑遍i组元素后，更新出最新的f数组；而第一层只是遍历所有元素组。

# 线性DP

## 数字三角形

### 思路
-  很简单的DP思想，对于$f[i][j]$的最大值（最大路径和0，只能考虑2种情况，从左上方/右上方来，考虑这两种情况，取最大值即可
```cpp
 for(int i = 2; i <= n; i++){
        for(int j = 1; j <= i; j++){
            f[i][j] = max(f[i - 1][j - 1] + a[i][j], f[i - 1][j] + a[i][j]);
        }
    }
```

**注意**
- 注意初始化$f[i][j]$，全部取-INF，来区分出边界问题
- 三角中的整数范围为$[-10000, 10000]$，所以遍历寻找最后第n层中的最值时，要初始值要小

### 源码
```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int N = 510, INF = 1e9;
int n;
int f[N][N];
int a[N][N];

int main(){
    cin >> n;
    //传入数据
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= i; j++)
            cin >> a[i][j];
    //初始化f
    for(int i = 0; i <= n; i++){
        for(int j = 0; j <= i + 1; j++){
            f[i][j] = -INF;
        }
    }
    f[1][1] = a[1][1];
    for(int i = 2; i <= n; i++){
        for(int j = 1; j <= i; j++){
            f[i][j] = max(f[i - 1][j - 1] + a[i][j], f[i - 1][j] + a[i][j]);
        }
    }
    int res = -INF;
    for(int i = 1; i <= n; i++)res = max(res, f[n][i]);
    cout << res << endl;
    return 0;
}
```

## 最长上升子序列

### 思路
- 将$f[i]$ 定义为以第i个元素结尾的上升序列的最大长度
- 状态转移方程：找到i前面的小于$a[i]$的元素$a[j]$，有$f[i]$ = max($f[i]$, $f[j] + 1$);
### 源代码

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int N  = 1010;
int a[N];
int f[N];
int n;

int main(){
    cin >> n;
    for(int i = 1; i <= n; i++)cin >> a[i];
    for(int i = 1; i <= n; i++){
        f[i] = 1;
        for(int j = 1; j < i; j++){
            if(a[j] < a[i]){
                f[i] = max(f[i], f[j] + 1);
            }
        }
    }
    int res = 0;
    for(int i = 1; i <= n; i++)res = max(res, f[i]);
    cout << res << endl;
    return 0;
}
```

### 注意
- **牢记背包问题最后所总结的思想，DP问题精髓就是先固定元素，确定考虑前面元素迭代出的f函数,然后利用前面的函数，利用特性创建状态转移方程，来获得$f[i]$数据**

> 1. 先定元素组：for (int i = 1; i <= n; i++)
   - 含义：锁定当前子序列的终点元素 $a[i]$。计算 i 时，前 i - 1 个元素结尾的最长长度已确定。
2. 二定容积：$f[i]$ = 1
 - 含义：确定当前状态的基准值，即子序列仅包含 $a[i]$ 自身时，基础长度为 1。

2. 最后定组内元素：for (int j = 1; j < i; j++) 配合 if ($a[j]$ < $a[i]$)
   - 含义：遍历前面所有合法的候选元素 $a[j]$，通过 max($f[i]$, $f[j]$ + 1) 筛选出最佳的前驱节点。

### 附
**如果想查找这个子序列**
```cpp
 // 从后往前找，找到的就是字典序最小的序列
        vector<int> res(cnt);
        for (int i = n - 1, j = cnt; j > 0; i -- ) {
            if (f[i] == j)
                res[ -- j] = arr[i];
        }
        return res;
    }
```


## 最长公共子序列

### 思路
- $f[i][j]$表示对于A数组的前i个元素和B数组的前j个元素，其最大公共子序列值
- 可以分为2种情况：
- $a[i]$  = $b[j]$，此时： $f[i][j]$ = $f[i - 1][j - 1] + 1$;
-  $a[i]$  != $b[j]$ 此时： $f[i][j]$ = max($f[i -1][j]$, $f[i][j - 1]$);



### 源代码
```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int N = 1010;
char a[N], b[N];
int f[N][N];
int n,m;

int main(){
    cin >> n >> m >> a + 1 >> b + 1;
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++){
            if(a[i] == b[j]){
                f[i][j] = f[i - 1][j - 1] + 1;
            }else{
                f[i][j] = max(f[i][j - 1], f[i - 1][j]);
            }
        }
    }
    cout << f[n][m] << endl;
    return 0;
}
```


## 最短编辑路径

给定两个字符串 A 和 B，现在要将 A 经过若干操作变为 B，可进行的操作有：

1. 删除–将字符串 A 中的某个字符删除。
2. 插入–在字符串 A 的某个位置插入某个字符。
3. 替换–将字符串 A 中的某个字符替换为另一个字符。
### 思路
- 将$f[i][j]$定义为将A数组中前i个元素转变为B数组中的前j元素所需要的最少步数。
- 依旧是状态转移方程


- 把第一个字符串的前 i 个字母变为第二个字符串的前 j - 1 个字符，然后在第一个字符串后面增加第二个字符串的第j个字母。

这种情况下，$f[i][j]$ = $f[i][j - 1]$ + 1。



把第一个字符串的前 i - 1 个字母变为第二个字符串的前 j 个字符，然后去掉最后一个字符。



这种情况下，$f[i][j]$ = $f[i - 1][j]$ + 1。


把第一个字符串的前 i - 1 个字母变为第二个字符串的前 j - 1个字符。变化之后，对比最后一个字符，如果相等，则变换完成，如果不同，把第一个字符串的最后一个字符变为第二个字符串的最后一个字符，


这种情况下，$f[i][j]$ = $f[i - 1][j - 1]$ + 1(最一个字符不同) 或$f[i][j]$ = $f[i - 1][j - 1]$ (最一个字符相同)。


### 源代码

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int N = 1010;
int f[N][N];
int n, m;
char a[N], b[N];

int main(){
    cin >> n >> a + 1 >> m >> b + 1;
    for(int i = 1; i <= n; i++){
        f[i][0] = i;
    }
    for(int j = 1; j <= m; j++){
        f[0][j] = j;
    }
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++){
            f[i][j] = min(f[i - 1][j] + 1, f[i][j - 1] + 1);
            if(a[i] != b[j])f[i][j] = min(f[i][j], f[i - 1][j - 1] + 1);
            else f[i][j] = min(f[i][j], f[i - 1][j - 1]);
        }
    }
    cout << f[n][m];
    return 0;
}
```

# 区间DP

## 石子合并

```txt
设有 N 堆石子排成一排，其编号为 1,2,3,…,N。

每堆石子有一定的质量，可以用一个整数来描述，现在要将这 N 堆石子合并成为一堆。

每次只能合并相邻的两堆，合并的代价为这两堆石子的质量之和，合并后与这两堆石子相邻的石子将和新堆相邻，合并时由于选择的顺序不同，合并的总代价也不相同。

例如有 4 堆石子分别为 `1 3 5 2`， 我们可以先合并 1、2 堆，代价为 4，得到 `4 5 2`， 又合并 1、2 堆，代价为 9，得到 `9 2` ，再合并得到 11，总代价为 4+9+11=24；

如果第二步是先合并 2、3 堆，则代价为 7，得到 `4 7`，最后一次合并代价为 11，总代价为 4+7+11=22。

问题是：找出一种合理的方法，使总的代价最小，输出最小代价。
```

### 思路
- 依旧是DP老套路，定义$f[i][j]$，为位置i到位置j的石子合并的最小代价。先遍历堆的长度，在固定了长度的基础上，遍历所有此长度的石堆组合，再取石堆中的所有位置，运用状态转移方程获得最小代价
- 状态转移方程：$f[l][r] = min(f[l][r], f[l][i] + f[i + 1][r] + s[r] - s[l - 1])$

### 源代码

```cpp
#include<iostream>
#include<algorithm>
using namespace std;

int n;
const int N = 310;
int s[N];//代价前缀和数组
int f[N][N];//从i到j这个范围内的合并最小代价

int main(){
    cin >> n;
    for(int i = 1; i <= n; i++)cin >> s[i];
    for(int i = 1; i <= n; i++)s[i] += s[i - 1];
    
    for(int lens = 2; lens <= n; lens++){
        for(int i = 1; i + lens - 1 <= n; i++){
            int l = i, r = i + lens - 1;
            f[l][r] = 1e9;
            for(int i = l; i < r; i++){
                f[l][r] = min(f[l][r], f[l][i] + f[i + 1][r] + s[r] - s[l - 1]);
            }
        }
    }
    cout << f[1][n] << endl;
    return 0;
}
```

### 总结

- 想求$f[1][n]$，就要lens 值从1到n下时的所有可对应石堆的f；
- 故先遍历不同lens，再根据lens不同，遍历所有符合长度石堆；
- 最后利用状态转移方程，遍历石堆中的点，得到固定长度，固定起始点和终止点的石堆的最小代价。