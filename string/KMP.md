## KMP算法

`next[]` 的 含义: `x[i-next[i]]...i-1] = x[0...next[i]-1]`   
`next[i]` 为 满足 `x[i-z...i-1] = x[0...z-1]`的最大z值(即x的自身匹配)

```c++
int f[MAXN];
//预处理 蓝书 P212
void kmp_pre(string x){
	memset(f, 0,sizeof(f));
    int i,j;
    int m = x.size();
    f[0] = f[1] = 0;
    FOR(i,1,m){
        int j = f[i];
        while(j && x[i]!=x[j]) j = f[i];
        f[i+1] = x[i]==x[j] ? j+1 : 0;
    }
}

//返回 sub 在 T 中出现的次数，可以重叠
int KMP_Count(string T,string sub){
    int re = 0;
    int n = T.size(), m = sub.size();
    kmp_pre(sub);
    int j = 0;
    FOR(i,0,n){
        while(j && sub[j]!=T[i]) j = f[j];
        if(sub[j] == T[i]) j++;
        if(j == m) re++;
    }
    return re;
}
```