# LCS

最长公共子序列

## Base Line
转移方程：
$$
Lcs(X_m, Y_n) = 
\begin{cases}
  LCS(X_{m-1},Y_{n-1}) + 1, \ 当 x_m = y_n \\
  \\
  \max\{LCS(X_{m-1}, Y_n), LCS(X_m, Y_{n-1})\}, \ 当x_m\neq y_n
\end{cases}
$$

```cpp
int c[len_b + 10][len_a + 10];
int Lcs(char * a, int _len_a, char * b, int _len_b)
{
    for(int i = 0; i <= len_a; i++)
        c[0][i] = 0;
    for(int i = 0; i <= len_b; i++)
        c[i][0] = 0;
    
    for(int i = 0; i < len_a; i++)
        for(int j = 0; j < len_b; j++)
        {
            if(a[i] == b[j])
            {
                c[i+1][j+1] = c[i][j] + 1;
            }
            else
            {
                c[i+1][j+!] = max(c[i][j+1], c[i+1][j]);
            }
        }
    return c[_len_a + 1][_len_b + 1];
}
```

## 变形
### 求B与A任意字串的LCS
构建A的后缀加速数组 
$$G[i][c=a\rightarrow z] = min\{j | A[j] = c, i \leq j \leq lenA\}$$
表示由i开始的（含A[i]）最近的下一个c的位置（含A[i]）

当匹配 A[l, r] 时，
$F_{i,j}$： B[0, i] 的长度为 j 的最短匹配端点，即 B[0, i] 与 A[l, $F_{i_j}$] 的LCS恰好为 j，且 $F_{i_j}$ 尽可能小。
\* 当 $F_{i,j} > lenB$时，表示不存在lcs=j.

由此F的转移方程为:
$$F_{i,j} = min\{F_{i-1,j}, G[ F_{i-1,j-1} + 1 ][ B_i ]\}$$

> B[0, i-1] 与 A[l, $F_{i-1,j}$] 的lcs为 j
> B[0, i-1]要添加一个新字符 B[i], 且使得lcs+1, 则A必需要延伸到 "以$F_{i-1,j}+1$开始的(含)下一个等于B[i]的位置" 
> + 即 G[$F_{i-1,j}+1$][{B_i}]

为规避减1,变形为正向推导
$$
\begin{aligned}
    F_{i+1,j} &= min\{F_{i+1,j}, F_{i,j}\} \\
    F_{i+1,j+1} &= min\{F_{i+1,j+1}, G[F_{i,j}][B[i+1]]\}
\end{aligned}
$$

此时的 $F_{i_j}$ 标记了右端点而并没有符合右端点限制，需要进行筛选。

因此
    $$ans = max\{j | F_{i, j} <= r\}$$
```cpp
const int N = 100010, M = 25, S = 26; // N = max len A, M = max len B
int g[N][S], f[M][M];
inline int subLCS(int l, int r) // len of LSC of A[l,r] with B
{
    for (int i = 0; i <= m; i++)
        for (int j = 0; j <= i; j++)
            f[i][j] = N;
    f[0][0] = l - 1;
    for (int i = 0; i < m; i++)
        for (int j = 0; j <= i; j++)
        {
            f[i + 1][j] = max(f[i + 1][j], f[i][j]);
            if (f[i][j] < r)
                f[i + 1][j + 1] = max(f[i + 1][j + 1], g[f[i][j] + 1][b[i + 1]]);
        }
    // r -> l, top -> down
    for (int j = m; j > 0; j--)
        for (int i = l; i <= m; i++)
            if (f[i][l] <= r)
                return i;
    return 0;
}
```