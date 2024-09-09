---
marp: true
paginate: true
---

# 数学：质数、约数、筛法

20240308

---

### 一、因数与质数

1. 找一个数的所有因数

2. 判断一个数是不是质数

3. 分解质因数

4. 求一个数的因数个数、因数之和

---

### 一、因数与质数

#### 1. 找一个数的所有因数

试除法 $O(n)$

```c++
void getDivisors(int n) {
    for (int i = 1; i <= n; i++)
        if (n % i == 0) cout << i << ' ';
}
```

太慢了！

---

### 一、因数与质数

#### 1. 找一个数的所有因数

性质：$n$ 的因数总是成对出现的，即 $d|n \iff \frac nd | n$

整除符号 $x|y$ 表示 $y\%x=0$

所以我们可以只枚举每对因数中较小的那一个，即满足 $d\le \frac nd$ 的因子 $d$

等价于 $d\times d\le n$ 和 $d\le \sqrt n$

于是我们有 $O(\sqrt n)$ 的试除法

---

### 一、因数与质数

#### 1. 找一个数的所有因数

试除法 $O(\sqrt n)$

```c++
void getDivisors(int n) {
    for (int i = 1; i <= n / i; i++) {
        if (n % i) continue;
        cout << i << ' ';
        if (i != n / i) cout << n / i << ' ';
    }
}
```

三种写法：
`i<=sqrt(n)` 浪费时间（不推荐），`i*i<=n` 可能爆 int，除法稍微慢一点

---

### 一、因数与质数

#### 1. 找一个数的所有因数

小知识：一个数的因数数量大概是多少？

$10^5$ 内因数个数最多的数有 $128$ 个因数
$10^8$ 内因数个数最多的数有不到 $800$ 个因数

---

### 一、因数与质数

#### 2. 判断一个数是不是质数

试除法

```c++
bool isPrime(int n) {
    if (n < 2) return false;
    for (int i = 2; i <= n / i; i++)
        if (n % i == 0) return false;
    return true;
}
```

---

### 一、因数与质数

#### 3. 分解质因数

**唯一分解定理：** 一个大于 $1$ 的自然数可以被表示为有限个质数的乘积，且表示方法是唯一的
$$
n = p_1^{c_1}p_2^{c_2}\cdots p_k^{c_k}
$$
如 $12 = 2\times 2\times 3$

---
### 一、因数与质数

#### 3. 分解质因数

**试除法分解质因数**（跟短除法很像）

```c++
void divide(int n) {
    for (int i = 2; i <= n / i; i++) {
        if (n % i) continue;
        // 此时i一定是质数，为什么？
        int c = 0;
        while (n % i == 0)
            n /= i, c++;
        cout << i << ' ' << c << '\n';
    }
    // 最多只有1个>sqrt(n)的质因子，为什么？
    if (n > 1) cout << n << ' ' << 1;
}
```

---

### 一、因数与质数

#### 4. 求一个数的因数个数、因数之和

唯一分解定理是解决因数问题的常用工具
$$
n = p_1^{c_1}p_2^{c_2}\cdots p_k^{c_k}
$$
**因数个数：**
$$
(c_1+1)(c_2+1)\cdots (c_k+1)
$$
解释：$n$ 的任一因数 $d$ 可以写为 $d=p_1^{b_1}p_2^{b_2}\cdots p_k^{b_k}$，其中 $0\le b_i\le c_i$，所以 $b_i$ 有 $c_i+1$ 种可能的取值

代码实现：开个map

---

### 一、因数与质数

#### 4. 求一个数的因数个数、因数之和

唯一分解定理是解决因数问题的常用工具
$$
n = p_1^{c_1}p_2^{c_2}\cdots p_k^{c_k}
$$

**因数之和：**
$$
(p_1^{0}+p_1^{1}+p_1^2+\cdots +p_1^{c_1})(p_2^{0}+p_2^{1}+p_2^2+\cdots +p_2^{c_2})\cdots (p_k^{0}+p_k^{1}+p_k^2+\cdots +p_k^{c_k})
$$
解释：展开有惊喜，可举例帮助理解

代码实现：每次 $\times p + 1$，类似秦九韶算法

---

### 二、筛法

找出 $1\sim n$ 中的所有质数

1. 朴素筛

2. 埃氏筛

3. 线性筛

---
### 二、筛法

#### 1. 朴素筛

为什么叫“筛”？

在 $2,3,4,5,6,7,8,\cdots$ 中删去（筛掉）$2$ 的倍数、$3$ 的倍数、$\cdots$

最后剩下的没被筛掉的数就是质数

```c++
bool notPri[N];
int primes[N], tot;
void getPrimes(int n) {
    for (int i = 2; i <= n; i++) {
        if (!notPri[i]) primes[++tot] = i;
        for (int j = i + i; j <= n; j += i)
            notPri[j] = true;
    }
}
```

朴素筛法的复杂度是多少？

---
### 二、筛法

#### 1. 朴素筛

小知识：**调和级数**

$$
1+\frac 12 + \frac 13 + \frac 14 + \cdots + \frac 1n \sim\ln n
$$

朴素筛法的复杂度：

$\lfloor\frac n2\rfloor + \lfloor\frac n3\rfloor + \cdots + 1$

$O(n\ln n)$

---

### 二、筛法

#### 2. 埃氏筛

对朴素筛的改进：只删掉质数的倍数（因为合数一定能被它的质因子筛掉）

```c++
bool notPri[N];
int primes[N], tot;
void getPrimes(int n) {
    for (int i = 2; i <= n; i++) {
        if (!notPri[i]) { //只有这一点区别
            primes[++tot] = i;
            for (int j = i + i; j <= n; j += i)
                notPri[j] = true;
        }
    }
}
```

---

### 二、筛法

#### 2. 埃氏筛

埃氏筛的复杂度是多少？

质数分布小规律：$1\sim n$ 中的质数大约有 $n/\ln n$ 个

思考：本来要用 $n$ 个数来筛，现在只用 $n/\ln n$ 个，复杂度大概是 $n/\ln n \times \ln n=O(n)$？

上面的估计很不准确。实际上应该是 $O(n\log (\log n))$。了解即可。证明我不知道。

埃式筛已经“几乎”是线性的，$\ln(\ln10^7)\approx 2.8$，很小

---

### 二、筛法

#### 3. 线性筛（欧拉筛）

还能更快：每个合数只被它的最小质因子筛一次，复杂度 $O(n)$

```c++
// 巨常用，要理解+记忆
bool notPri[N];
int primes[N], tot;
void getPrimes(int n) {
    for (int i = 2; i <= n; i++) {
        if (!notPri[i]) primes[++tot] = i;
        for (int j = 1; primes[j] <= n / i; j++) {
            notPri[primes[j] * i] = true; //注意i和j不要写错
            if (i % primes[j] == 0) break;
        }
    }
}
```

为什么每个合数只被它的最小质因子筛一次？

---

### 二、筛法

#### 3. 线性筛（欧拉筛）

不难发现，`primes[j]` 要么比 `i` 的最小质因子还小（`break`之前），要么就是 `i` 的最小质因子（`break`时）

所以 `primes[j]` 一定是 `primes[j] * i` 的最小质因子！

---

### 二、筛法

#### 3. 线性筛（欧拉筛）

另一种推荐写法：线性筛，顺便求每个数的最小质因子

这种写法可能更能体现欧拉筛的思想

```c++
int p[N], primes[N], tot;
void getPrimes(int n) {
    p[1] = 1; // 根据题目需要设定p[1]的值
    for (int i = 2; i <= n; i++) {
        if (!p[i]) p[i] = i, primes[++tot] = i;
        for (int j = 1; primes[j] <= n / i; j++) {
            p[primes[j] * i] = primes[j];
            if (p[i] == primes[j]) break;
        }
    }
}
```

---
### 二、筛法

#### 3. 线性筛（欧拉筛）

对试除法分解质因数的改进：

先求出每个数的最小质因子（数组`p[]`），不必从 $2$ 开始枚举质因子，每次都用 `p[n]` 来试除即可

复杂度：$O(\sqrt {质数个数})=O(\sqrt{n/\ln n})$ （存疑）

