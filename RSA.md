# RSA加密算法概念

RSA加密算法是一种非对称加密算法，在公开密钥加密和电子商业中被广泛使用。RSA是由罗纳德·李维斯特（Ron Rivest）、阿迪·萨莫尔（Adi Shamir）和伦纳德·阿德曼（Leonard Adleman）在1977年一起提出的。当时他们三人都在麻省理工学院工作。RSA 就是他们三人姓氏开头字母拼在一起组成的。

# 前期知识

## 数论运算基本性质

(a,b)：a 与 b 的最大公因数；

[a,b]：a 与 b 的最小公倍数；

[x]：Gauss 函数，实数 x 的整数部分；

{x}：Gauss 函数，实数 x 的小数部分。

a|b（“|”是整除符号），读作“a整除b”或“b能被a整除”。a叫做b的约数（或因数），b叫做a的倍数。

整除的基本性质

①若b|a，c|a，且b和c互质，则bc|a。

②对任意非零整数a，±a|a=±1。

③若a|b，b|a，则|a|=|b|。

④如果a能被b整除，c是任意整数，那么积ac也能被b整除。

⑤对任意整数a，b>0，存在唯一的数对q，r，使a=bq+r，其中0≤r<b，这个事实称为带余除法定理，是整除理论的基础。

⑥若c|a，c|b，则称c是a，b的公因数。若d是a，b的公因数，d≥0，且d可被a，b的任意公因数整除，则d是a，b的最大公因数。若a，b的最大公因数等于1，则称a，b互素，也称互质。累次利用带余除法可以求出a，b的最大公因数，这种方法常称为辗转相除法。又称欧几里得算法。

 

余数：

a = qn+r

r = a mod n = a%n

同模：

a ≡ b(mod n) 等价于 a%n = b%n

性质：

a%n = b%n 则 (a-b)%n=0

(a+b)%n = ((a%n)+(b%n))%n

(a*b)%n = ((a%n)*(b%n))%n

(a+k*n)%n = a%n

(k*a)%n = (k*(a%n))%n

a%(n1*n2) = r 则 a%n1 = r%n1

## 欧拉函数φ(n)

如果 m⊥n，有 φ(m*n) = φ(m) * φ(n)

如果p1,p2,…,pk为n的所有质因数，且每种质因数只有一个（不重复）

φ(n) = (p1-1)*(p2-1)*…*(pk-1)

如果 n = p^k

φ(n) = (p-1)*(p^(k-1)）

例如：

n = p*p*q

φ(n) = p*(p-1)*(q-1)

## 费马小定理

如果 p 是素数，且 a%p != 0，则 a^(p-1) ≡ 1(mod p)

## 费马-欧拉定理

如果 a、n 为整数，且 a⊥n（a与n互质），则 a^φ(n) ≡ 1(mod n)

# RSA 加密算法及其证明过程

## 密钥生成

1. 找两个大素数p、q，计算N=pq。
2. 欧拉函数φ(N)=φ(p)φ(q)=(p-1)(q-1)。
3. 选取整数e，使e与φ(N)互质，求得e关于φ(N)的模逆元d（ed≡1(mod φ(N))）。
4. 将p、q的记录销毁。

（N, d) 为私钥，(N, e) 为公钥。

## 加密消息

$$
c=m^e mod N
$$

## 解密消息

$$
m = c^d mod N
$$

## 正确性证明

$$
p、q 为素数，n = pq，e < φ(n) 且 e ⊥ φ(n)，m^e ≡ c(mod\space n)，求证 c^d ≡ m(mod\space n)，其中 d^{-1} ≡ e(mod\space φ(n))
$$

【实际应用中，明文被分段，参与运算的 m 始终小于 n，因此有 m^e%n = c，c^d%n = m】

 

m^e = c + k*n

m^(e*d) = (c + k*n)^d 

右边 = c^d + k1*n

1. m⊥n的情况，根据费马-欧拉定理，有

左边 = m^(1+k2*φ(n)) = m*(m^k2)^φ(n)) = m*(1+k3*n) = m + k4*n 

得 c^d + k1*n = m + k4*n

即 c^d ≡ m(mod n)

2. m与n 不互质的情况，假设 m = k5p，则 m⊥q，根据费马-欧拉定理，有

左边 = m*(m^(k2*φ(p)))^φ(q) = m*(1+k6*q) = m + k6*k5*p*q = m + k7*n

得 c^d + k1*n = m + k7*n

得 c^d ≡ m(mod n)

# 破解相关算法及应用

## 威尔逊定理应用——大数阶乘取模

威尔逊定理 

P为质数 等价于 (p-1)!≡-1(mod p)

 

B!%A （A为质数）计算量很大，如果A和B比较接近，可以通过威尔逊定理计算。

B<A的情况：

\# 计算((B+1)…(A-2))%A

def mod_wei(A,B):

  mod = 1

  for i in range(B+1,A-1):

​    mod *= i

​    mod %= A

  return mod

gmpy2.invert(mod_wei(A,B),A) 即为 B!%A

 

推导过程：

(A-1)!≡-1(mod A)

B小于A，带入B

(B!(B+1)…(A-2)(A-1))≡-1(mod A)

定义 x=((B+1)…(A-2))%A

(B!%A*x*(A-1)%A)≡-1(mod A)

因 (A-1)≡-1(mod A)，有

(B!%A*x)≡1(mod A)

因此 B!%A为x关于A的模逆元 gmpy2.invert(x,A)

即：gmpy2.invert(((B+1)*…*(A-2))%A, A)

 

B>=A的情况：

B!%A

=((A-1)!*A*…*B)%A

=0

因数中出现了A，余数为0

## 中国剩余定理——解同余方程

今有物不知其数，三三数之剩二；五五数之剩三；七七数之剩二。问物几何?

 

```python
from functools import reduce
import sympy

def chinese_remainder(n, a):
  sum = 0
  prod = reduce(lambda a, b: a * b, n)
  for n_i, a_i in zip(n, a):
	p = prod // n_i
	sum += a_i * sympy.invert(p, n_i) * p
  return int(sum % prod)

m = chinese_remainder([3,5,7], [2,3,2])
assert(m%3==2)
assert(m%5==3)
assert(m%7==2)
print(m)
# 23
```

以上函数 chinese_remainder(n, a) 为标准的中国剩余定理求解，要求序列n中的模之间互素，否则报错。

以下是另一版中国剩余定理的求解函数：

```python
from functools import reduce
import gmpy2

def CRT(mi, ai):
  assert(reduce(gmpy2.gcd,mi)==1)
  assert (isinstance(mi, list) and isinstance(ai, list))
  M = reduce(lambda x, y: x * y, mi)
  ai_ti_Mi = [a * (M // m) * gmpy2.invert(M // m, m) for (m, a) in zip(mi, ai)]
  return reduce(lambda x, y: x + y, ai_ti_Mi) % M
m = CRT([3,5,7], [2,3,2])
assert(m%3==2)
assert(m%5==3)
assert(m%7==2)
print(m)
```

\---------------------------------------------------------------

模不互素的情况（手工推导求解）：

经过推导，得到模互素（质数模）的同余方程，然后求解：

```python
#m = chinese_remainder([21,15], [2,8])
# m%(3*7)=2 m%(3*5)=8
# m%(3*7)%7=2%7 m%(3*7)%3=2%3 m%(3*5)%5=8%5
# m%7=2 m%3=2 m%5=3
m = CRT([7,3,5], [2,2,3])
print(m)
assert(m%21==2)
assert(m%15==8)
```

\---------------------------------------------------------------

模不互素的情况（直接求解）：

```python
import gmpy2

def GCRT(mi, ai):
  # mi,ai分别表示模数和取模后的值,都为列表结构
  assert (isinstance(mi, list) and isinstance(ai, list))
  curm, cura = mi[0], ai[0]
  for (m, a) in zip(mi[1:], ai[1:]):
	d = gmpy2.gcd(curm, m)
	c = a - cura
	assert (c % d == 0) #不成立则不存在解
	K = c // d * gmpy2.invert(curm // d, m // d)
	cura += curm * K
	curm = curm * m // d
	cura %= curm
  return (cura % curm, curm) #(解,最小公倍数)
f3,lcm = GCRT([21,15],[2,8])
```

n中的模不互素的情况，用GCRT(mi, ai)计算，返回特解f3和模的最小公倍数lcm。由这两项易得通解。

