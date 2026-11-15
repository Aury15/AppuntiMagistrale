# Number Theory for Cybersecurity


---

# Divisibility and the Division Algorithm

> [!abstract] Definition — Divisibility
> An integer $b\neq0$ **divides** an integer $a$, written as
> 
> $$
> b\mid a
> $$
> 
> if there exists an integer $m$ such that
> 
> $$
> a=mb.
> $$
>
> In other words, $b$ divides $a$ if the division produces **no remainder**.
> In this case, $b$ is called a **divisor** of $a$.

## Properties of Divisibility

- If $a\mid1$, then:
  $$
  a=\pm1
  $$

- If $a\mid b$ and $b\mid a$, then:
  $$
  a=\pm b
  $$

- Every non-zero integer $b$ divides $0$:
  $$
  b\mid0
  $$

- **Transitive property:**
  $$
  a\mid b,\quad b\mid c
  \quad\Longrightarrow\quad
  a\mid c
  $$

- **Linear combination:** if $b\mid g$ and $b\mid h$, then $b$ divides every linear combination
  $$
  mg+nh
  $$
  where $m,n\in\mathbb Z$.

> [!example] Linear Combination
> Since
> $$
> 7\mid14
> $$
> and
> $$
> 7\mid63,
> $$
> then:
> $$
> 7\mid(3\cdot14+2\cdot63).
> $$

## Division Algorithm

> [!note] Division Algorithm
> Given a positive integer $n$ and a non-negative integer $a$, there exist unique integers $q$ and $r$ such that
>
> $$
> a=qn+r
> $$
>
> with
>
> $$
> 0\leq r<n.
> $$
>
> The quotient is:
>
> $$
> q=\left\lfloor\frac an\right\rfloor.
> $$

The value $r$ is the **remainder** of the division.

> [!example]
> Dividing $17$ by $5$:
>
> $$
> 17=3\cdot5+2
> $$
>
> Therefore:
>
> $$
> q=3,\qquad r=2.
> $$

---

# Greatest Common Divisor and Euclidean Algorithm

## Greatest Common Divisor

The **Greatest Common Divisor (GCD)** of $a$ and $b$ is the largest positive integer that divides both numbers.

It is denoted by:

$$
\gcd(a,b).
$$

Special cases:

$$
\gcd(0,0)=0
$$

and

$$
\gcd(a,0)=|a|.
$$

The GCD is independent of the signs:

$$
\gcd(a,b)=\gcd(|a|,|b|).
$$

## Relatively Prime Integers

> [!abstract] Definition — Coprime Integers
> Two integers $a$ and $b$ are **relatively prime** or **coprime** if their only positive common divisor is $1$.
>
> Equivalently:
>
> $$
> \gcd(a,b)=1.
> $$

---

## Euclidean Algorithm

The **Euclidean Algorithm** is an efficient procedure for computing the GCD of two integers.

Assume:

$$
a>b.
$$

Using the Division Algorithm:

$$
a=q_1b+r_1.
$$

If:

$$
r_1=0,
$$

then:

$$
\gcd(a,b)=b.
$$

Otherwise:

$$
\gcd(a,b)=\gcd(b,r_1).
$$

The procedure is repeated until the remainder becomes $0$.

> [!example] Example
> Calculate:
>
> $$
> \gcd(48,18)
> $$
>
> We perform the divisions:
>
> $$
> 48=2\cdot18+12
> $$
>
> $$
> 18=1\cdot12+6
> $$
>
> $$
> 12=2\cdot6+0
> $$
>
> Therefore:
>
> $$
> \boxed{\gcd(48,18)=6}
> $$

---

## Extended Euclidean Algorithm

> [!abstract] Extended Euclidean Algorithm
> The **Extended Euclidean Algorithm** computes not only the GCD, but also two integers $x$ and $y$ such that:
>
> $$
> ax+by=d
> $$
>
> where:
>
> $$
> d=\gcd(a,b).
> $$

The coefficients can be computed recursively using:

$$
x_i=x_{i-2}-q_ix_{i-1}
$$

and:

$$
y_i=y_{i-2}-q_iy_{i-1}.
$$

> [!important] Cryptographic relevance
> The Extended Euclidean Algorithm is particularly important in cryptography because it can be used to compute **modular multiplicative inverses**.

---

# Modular Arithmetic

## Modulo

> [!abstract] Definition — Modulo
> Let $a$ be an integer and $n$ a positive integer.
>
> The expression
>
> $$
> a\bmod n
> $$
>
> represents the **remainder** obtained when dividing $a$ by $n$.
>
> The integer $n$ is called the **modulus**.

From the Division Algorithm:

$$
a=
\left\lfloor\frac an\right\rfloor n+(a\bmod n).
$$

> [!example]
> Since:
>
> $$
> 17=3\cdot5+2
> $$
>
> we have:
>
> $$
> 17\bmod5=2.
> $$

---

# Congruence

Two integers $a$ and $b$ are **congruent modulo $n$** if they have the same remainder when divided by $n$.

Notation:

$$
a\equiv b\pmod n.
$$

The most important equivalent definition is:

$$
a\equiv b\pmod n
\iff
n\mid(a-b).
$$

Therefore:

$$
a\equiv0\pmod n
\iff
n\mid a.
$$

## Properties of Congruence

### Reflexive

$$
a\equiv a\pmod n
$$

### Symmetric

$$
a\equiv b\pmod n
\quad\Longrightarrow\quad
b\equiv a\pmod n
$$

### Transitive

$$
a\equiv b\pmod n
$$

and

$$
b\equiv c\pmod n
$$

imply:

$$
a\equiv c\pmod n.
$$

---

# Properties of Modular Arithmetic

Modular arithmetic preserves the usual arithmetic operations.

## Addition

$$
[(a\bmod n)+(b\bmod n)]\bmod n
=
(a+b)\bmod n
$$

## Subtraction

$$
[(a\bmod n)-(b\bmod n)]\bmod n
=
(a-b)\bmod n
$$

## Multiplication

$$
[(a\bmod n)(b\bmod n)]\bmod n
=
(ab)\bmod n
$$

The arithmetic operations in $\mathbb Z_n$ satisfy the usual:

- commutative laws
- associative laws
- distributive laws

## Additive Inverse

For every:

$$
w\in\mathbb Z_n
$$

there exists an element $z$ such that:

$$
w+z\equiv0\pmod n.
$$

> [!example]
> Modulo $7$, the additive inverse of $3$ is $4$ because:
>
> $$
> 3+4=7\equiv0\pmod7.
> $$

---

> [!warning] Cancellation in Modular Arithmetic
> Addition can always be cancelled:
>
> $$
> a+b\equiv a+c\pmod n
> \quad\Longrightarrow\quad
> b\equiv c\pmod n.
> $$
>
> Multiplication is different.
>
> From:
>
> $$
> ab\equiv ac\pmod n
> $$
>
> we can conclude:
>
> $$
> b\equiv c\pmod n
> $$
>
> **only if**
>
> $$
> \gcd(a,n)=1.
> $$

The reason is that cancellation requires $a$ to have a **multiplicative inverse modulo $n$**.

---

# Modular Multiplicative Inverse

The **modular inverse** of $a$ modulo $n$ is an integer $a^{-1}$ satisfying:

$$
aa^{-1}\equiv1\pmod n.
$$

> [!important] Existence of the Modular Inverse
> The inverse of $a$ modulo $n$ exists **if and only if**:
>
> $$
> \gcd(a,n)=1.
> $$

To compute the inverse, we use the **Extended Euclidean Algorithm**.

If:

$$
\gcd(a,n)=1,
$$

the algorithm finds integers $x$ and $y$ such that:

$$
ax+ny=1.
$$

Taking the equation modulo $n$:

$$
ax\equiv1\pmod n.
$$

Therefore:

$$
\boxed{a^{-1}\equiv x\pmod n}.
$$

---

# Prime Numbers

A **prime number** is an integer greater than $1$ whose only positive divisors are:

$$
1
$$

and itself.

Examples:

$$
2,3,5,7,11,13,\ldots
$$

A prime number cannot be expressed as the product of two smaller positive integers greater than $1$.

---

# Fundamental Theorem of Arithmetic

> [!quote] Fundamental Theorem of Arithmetic
> Every integer $a>1$ can be written **uniquely** as a product of prime numbers.

In general:

$$
a=p_1^{a_1}p_2^{a_2}\cdots p_t^{a_t}
$$

where the $p_i$ are distinct prime numbers.

> [!example]
> The prime factorization of $60$ is:
>
> $$
> 60=2^2\cdot3\cdot5.
> $$

The factorization is unique apart from the ordering of the factors.

---

# Primality Testing

A **primality test** determines whether a number is prime or composite.

## Miller–Rabin

The **Miller–Rabin primality test** is widely used for testing very large integers.

It is a **probabilistic primality test**.

Depending on the result, a number can be classified as:

- **Composite**
- **Probably prime**

By repeating the test with different bases, the probability of incorrectly identifying a composite number as prime can be made extremely small.

> [!important] Cryptographic relevance
> Miller–Rabin is useful in cryptography because cryptographic systems often require very large prime numbers, and testing them efficiently is essential.

---

## AKS

The **AKS algorithm**, introduced in 2002 by Agrawal, Kayal and Saxena, is a **deterministic polynomial-time primality test**.

Unlike Miller–Rabin, AKS provides a deterministic answer.

However, in practical cryptographic applications, probabilistic tests such as Miller–Rabin are generally preferred because of their efficiency.

---

# Fundamental Number-Theoretic Theorems

## Fermat's Little Theorem

> [!quote] Fermat's Little Theorem
> If $p$ is a prime number and $a$ is an integer not divisible by $p$, then:
>
> $$
> a^{p-1}\equiv1\pmod p.
> $$

The condition can be written as:

$$
\gcd(a,p)=1.
$$

An alternative form is:

$$
\boxed{a^p\equiv a\pmod p}
$$

which holds for every integer $a$.

> [!example]
> Let:
>
> $$
> p=7,\qquad a=3.
> $$
>
> Fermat's Little Theorem gives:
>
> $$
> 3^6\equiv1\pmod7.
> $$
>
> Indeed:
>
> $$
> 3^6=729
> $$
>
> and:
>
> $$
> 729\bmod7=1.
> $$

---

# Euler's Totient Function

The **Euler totient function**, denoted by:

$$
\phi(n),
$$

counts the positive integers smaller than $n$ that are relatively prime to $n$.

Formally:

$$
\phi(n)
=
\left|
\{k:1\leq k<n,\ \gcd(k,n)=1\}
\right|.
$$

By definition:

$$
\phi(1)=1.
$$

## Special Case: Product of Two Primes

If:

$$
n=pq
$$

where $p$ and $q$ are distinct prime numbers, then:

$$
\phi(n)=(p-1)(q-1).
$$

> [!important] Cryptographic relevance
> This formula is particularly important in **RSA**, where the modulus is constructed as the product of two large prime numbers.

---

# Euler's Theorem

> [!quote] Euler's Theorem
> If $a$ and $n$ are relatively prime, then:
>
> $$
> \boxed{a^{\phi(n)}\equiv1\pmod n}.
> $$

The condition is:

$$
\gcd(a,n)=1.
$$

An alternative form is:

$$
a^{\phi(n)+1}\equiv a\pmod n.
$$

## Relationship with Fermat's Little Theorem

Euler's theorem generalizes Fermat's Little Theorem.

If $n=p$ is prime, then:

$$
\phi(p)=p-1.
$$

Therefore Euler's theorem becomes:

$$
a^{p-1}\equiv1\pmod p,
$$

which is exactly Fermat's Little Theorem for:

$$
\gcd(a,p)=1.
$$

---

# Chinese Remainder Theorem

> [!quote] Chinese Remainder Theorem — CRT
> The **Chinese Remainder Theorem (CRT)** allows an integer to be reconstructed from its remainders with respect to several **pairwise coprime moduli**.

Consider a system such as:

$$
x\equiv a_1\pmod{n_1}
$$

$$
x\equiv a_2\pmod{n_2}.
$$

If:

$$
\gcd(n_1,n_2)=1,
$$

then the system has a unique solution modulo:

$$
N=n_1n_2.
$$

More generally, if:

$$
n_1,n_2,\ldots,n_k
$$

are pairwise coprime, then the system has a unique solution modulo:

$$
N=n_1n_2\cdots n_k.
$$

## Why is CRT useful?

The Chinese Remainder Theorem allows calculations involving a large modulus to be decomposed into calculations involving several smaller moduli.

This can make some cryptographic computations significantly more efficient.

> [!important] Requirement
> The standard form of CRT requires the moduli to be **pairwise coprime**.

---

# Discrete Logarithms

The **Discrete Logarithm Problem (DLP)** is the modular analogue of the ordinary logarithm.

Given:

$$
a^x\equiv y\pmod n,
$$

the objective is to find the exponent $x$.

It can be written as:

$$
dlog_a(y)=x\pmod n.
$$

In other words:

> Given $a$, $y$ and $n$, find $x$ such that $a^x\equiv y\pmod n$.

## Simple Cases

For:

$$
dlog_a(1),
$$

we have:

$$
a^0=1,
$$

so:

$$
dlog_a(1)\equiv0.
$$

Similarly:

$$
a^1=a,
$$

therefore:

$$
dlog_a(a)\equiv1.
$$

---

# Why Discrete Logarithms Matter for Cybersecurity

The important property of the discrete logarithm problem is the difference between the two directions:

### Easy direction

Given $a$ and $x$, computing:

$$
a^x\bmod n
$$

can be done efficiently using **modular exponentiation**.

### Difficult direction

Given $a$, $y$ and $n$, recovering $x$ from:

$$
a^x\equiv y\pmod n
$$

can be computationally difficult for appropriately chosen mathematical groups and parameters.

This is known as a **computational hardness assumption**.

> [!important] Cryptographic relevance
> The difficulty of the discrete logarithm problem is used as the basis for several cryptographic constructions, including:
>
> - **Diffie–Hellman key exchange**
> - **Elliptic-Curve Cryptography (ECC)**

---

> [!warning] Important Details
> A discrete logarithm does **not necessarily exist** for every choice of $a$, $y$ and $n$.
>
> Even when a solution exists, it may **not be unique**.
>
> Therefore, the discrete logarithm problem must be considered within a specific mathematical group and under appropriate assumptions about the parameters.

---

# Key Connections

> [!summary] Number Theory and Cybersecurity

The main concepts are strongly interconnected:

$$
\boxed{
\text{Divisibility}
\rightarrow
\text{GCD}
\rightarrow
\text{Euclidean Algorithm}
\rightarrow
\text{Modular Arithmetic}
}
$$

Then:

$$
\boxed{
\text{Modular Arithmetic}
\rightarrow
\text{Modular Inverse}
\rightarrow
\text{Cryptographic Algorithms}
}
$$

Prime numbers lead to:

$$
\boxed{
\text{Prime Numbers}
\rightarrow
\text{Fermat}
\rightarrow
\text{Euler}
\rightarrow
\text{RSA}
}
$$

Finally:

$$
\boxed{
\text{Discrete Logarithm}
\rightarrow
\text{Diffie-Hellman}
\rightarrow
\text{ECC}
}
$$

These concepts form some of the mathematical foundations of modern **public-key cryptography**.