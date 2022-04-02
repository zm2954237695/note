前缀和

acwing 99. 激光炸弹



地图上有 N 个目标，用整数 Xi,Yi 表示目标在地图上的位置，每个目标都有一个价值 Wi。

**注意**：不同目标可能在同一位置。

现在有一种新型的激光炸弹，可以摧毁一个包含 R×R 个位置的正方形内的所有目标。

激光炸弹的投放是通过卫星定位的，但其有一个缺点，就是其爆炸范围，即那个正方形的边必须和 x，yx，y 轴平行。

求一颗炸弹最多能炸掉地图上总价值为多少的目标。

#### 输入格式

第一行输入正整数 N 和 R，分别代表地图上的目标数目和正方形的边长，数据用空格隔开。

接下来 NN 行，每行输入一组数据，每组数据包括三个整数 Xi,Yi,Wi，分别代表目标的 x 坐标，y 坐标和价值，数据用空格隔开。

#### 输出格式

输出一个正整数，代表一颗炸弹最多能炸掉地图上目标的总价值数目。

#### 数据范围

0≤R≤10^9
0<N≤10000
0≤Xi,Yi≤5000
0≤Wi≤1000

#### 输入样例：

```
2 1
0 0 1
1 1 1
```

#### 输出样例：

```
1
```

题目：求出边长为R的所有正方形区域的最大权重。

先用二维前缀和计算所有的前缀和，再求出边长R的正方形的权重，取max即可。

```c++
#include <algorithm>
#include <climits>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iomanip>
#include <iostream>
#include <list>
#include <map>
#include <queue>
#include <set>
#include <sstream>
#include <stack>
#include <string>
#include <unordered_map>
#include <unordered_set>
#include <vector>
using namespace std;
typedef long long ll;
typedef unsigned long long ull;
#define pb push_back
#define pii pair<int, int>
#define rep(i, a, b) for (int i = a; i <= b; i++)
#define per(i, a, b) for (int i = a; i >= b; i--)
#define ios                      \
    ios::sync_with_stdio(false); \
    cin.tie(0);                  \
    cout.tie(0);
template <typename T>
void write(T x)
{
    if (x < 0)
    {
        putchar('-');
        x = -x;
    }
    if (x > 9)
    {
        write(x / 10);
    }
    putchar(x % 10 + '0');
}

template <typename T>
void read(T &x)
{
    x = 0;
    char ch = getchar();
    ll f = 1;
    while (!isdigit(ch))
    {
        if (ch == '-')
            f *= -1;
        ch = getchar();
    }
    while (isdigit(ch))
    {
        x = x * 10 + ch - 48;
        ch = getchar();
    }
    x *= f;
}
const int maxn = 5005;
int sum[maxn][maxn];
int main()
{
    int n, r;
    read(n);
    read(r);
    r=min(r,5001);
    rep(i,1,n){
        int x, y, w;
        read(x);
        read(y);
        read(w);
        x++;
        y++;
        sum[x][y] += w;
    }
    rep(i,1,maxn-1){
        rep(j,1,maxn-1){
            sum[i][j] += sum[i - 1][j] + sum[i][j - 1] - sum[i - 1][j - 1];
        }
    }
    int res = 0;
    rep(i,r,maxn-1){
        rep(j,r,maxn-1){
            res = max(sum[i][j] - sum[i - r][j] - sum[i][j - r] + sum[i - r][j - r],res);
        }
    }
    write(res);

    return 0;
}

    
    
```

