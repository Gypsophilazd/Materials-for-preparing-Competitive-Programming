## 题目背景

这是一道模板题。

## 题目描述

读入一个长度为 $n$ 的由大小写英文字母或数字组成的字符串，请把这个字符串的所有非空后缀按字典序（用 ASCII 数值比较）从小到大排序，然后按顺序输出后缀的第一个字符在原串中的位置。位置编号为 $1$ 到 $n$。

## 输入格式

一行一个长度为 $ n $ 的仅包含大小写英文字母或数字的字符串。

## 输出格式

一行，共 $n$ 个整数，表示答案。

## 输入输出样例 #1

### 输入 #1

```
ababa
```

### 输出 #1

```
5 3 1 4 2
```

## 说明/提示

$1\le n \le 10^6$。

### Solution
大意:给定一个字符串,将所有非空后缀按字典序排序,输出各个后缀起始位置

我们可以考虑开桶进行基数排序,但是数据规模达到了1e6,所以我们选择使用倍增法构建后缀数组(SA).
**倍增法核心**
比较每个字符的ASCII值,确定初始排名。再进行倍增比较,每次比较长度为 `k` 的子串,逐步扩大 `k`（1→2→4→...），直到所有后缀的排名唯一.
**排序依据**:若两个子串的前 `k` 个字符相同.则比较后 `k` 个字符的排名.
**代码解析**
`sa[i]` 初始为 `1~n`,表示所有后缀的起始位置.
`rk[i]` 是字符的ASCII值，如 `a` 对应97.
**`Cmp(k)`**：比较两个后缀的前 `k` 个字符的排名，若相同则比较后 `k` 个字符的排名。最后根据新的排名更新sa数组.
若当前后缀与前一个后缀的排名相同,则它们的 `tmp` 值相同;否则递增. 当所有排名唯一时,提前退出.

### AC Code
```
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1e6 + 10;
 
int sa[MAXN], rk[MAXN], tmp[MAXN];
int n, k;
char s[MAXN];
 
struct Cmp{
    int k;
    Cmp(int _k) : k(_k) {}
    bool operator()(int i, int j) const {
        if (rk[i] != rk[j]) return rk[i] < rk[j];
        int ri = (i + k <= n) ? rk[i + k] : -1;
        int rj = (j + k <= n) ? rk[j + k] : -1;
        return ri < rj;
    }
};

void build() {
    for (int i = 1; i <= n; ++i) {
        sa[i] = i;
        rk[i] = s[i];
    }
    for (k = 1; k <= n; k <<= 1) {
        Cmp comp(k);
        sort(sa + 1, sa + n + 1, comp);
        tmp[sa[1]] = 1;
        for (int i = 2; i <= n; ++i) {
            tmp[sa[i]] = tmp[sa[i - 1]] + (comp(sa[i - 1], sa[i]) ? 1 : 0);
        }
        memcpy(rk, tmp, sizeof(int) * (n + 1));
        if (rk[sa[n]] == n) break;
    }
}
int main() {
    cin >> s + 1;
    n = strlen(s + 1);
    build();
    for (int i = 1; i <= n; ++i) printf("%d ", sa[i]);
    return 0;
}
```