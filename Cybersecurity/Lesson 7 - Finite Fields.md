
# Finite Fields 

![[Pasted image 20261014184133.png|487]]
## Gerarchy of algebric structure

```
Gruops ⊂ Abelian groups ⊂ Rings ⊂ Anelli Commutativi ⊂ Domini di Integrità ⊂ Campi ⊂ Campi Finiti
```

#### Groups $(G, *)$
A set of elements with a binary operation denoted by ⊛ that associates to each ordered pair (a,b) of elements in G an element (a ⊛ b ) in G , such that the following axioms are obeyed:
- **(A1) Closure:** $\forall a, b \in G \implies a * b \in G$
- **(A2) Associative:** $a * (b * c) = (a * b) * c$
- **(A3) Identity element:** $\exists e \in G \mid a * e = e * a = a$
- **(A4) Inverse element:** $\forall a \in G, \exists a^{-1} \in G \mid a * a^{-1} = a^{-1} * a = e$

#### Abelian groups
A set of elements with a binary operation denoted by ⊛ that associates to each ordered pair (a,b) of elements in G an element (a ⊛ b ) in G , such that the following axioms are obeyed:
- **(A1) Closure:** $\forall a, b \in G \implies a * b \in G$
- **(A2) Associative:** $a * (b * c) = (a * b) * c$
- **(A3) Identity element:** $\exists e \in G \mid a * e = e * a = a$
- **(A4) Inverse element:** $\forall a \in G, \exists a^{-1} \in G \mid a * a^{-1} = a^{-1} * a = e$
- **(A5) Commutative:** $a * b = b * a$


Exponentiation is defined within a group as a repeated application of the group operator, so that a 3 = a⊛a⊛a •We define a 0 = e as the identity element, and a -n = (a -1 ) n , where a -1 is the inverse element of a within the group  •
#### Cyclic group
A group G is **cyclic** if every element of G is a power a k (k is an integer) of a fixed element a ∈ G
  $$G = \{g^k \mid k \in \mathbb{Z}\}$$
The element a is said to generate the group G or to be a **generator** of G.
We define a 0 = e as the identity element, and a -n = (a -1 ) n , where a -1 is the inverse element of a within the group.
A cyclic group is *always abelian* and may be finite or infinite

---

#### Ring $(R, +, \cdot)$
A ring R , sometimes denoted by {R , + , * }, is a set of elements with two binary operations, called addition and multiplication, such that for all a , b , c in R the following axioms are obeyed: 
1. R is an abelian group with respect to addition; that is, R satisfies axioms A1 through A5.
2. For the case of an additive group, we denote the identity element as 0 and the inverse of a as –a.
The two operations:
3. $(R, +)$ is an **Abelian group**  (identity $0$, inverse $-a$).
4. For the multiplication:
   - **(M1) Closure under multiplication**: if $a$ and $b$ belong to R, then $a \cdot b$ is also in R
   - **(M2) Associativity of multiplication:** $a(bc) = (ab)c$
   - **(M3) Distributive laws:**
     - $a(b + c) = ab + ac$
     - $(a + b)c = ac + bc$

> In essence, a ring is a set in which we can do addition, subtraction [a - b = a + (-b )], and multiplication without leaving the set.

####  Commutative rings
A ring is said to be commutative if it satisfies the following additional condition:
- **(M4) Commutativity of multiplication:** $ab = ba$

#### Dominio di Integrità (Integral Domain)
An integral domain is a commutative ring that obeys the following axioms:
- **(M5) Multiplicative identity::** $\exists 1 \in R \mid a \cdot 1 = 1 \cdot a = a$
- **(M6)No zero divisors:** $ab = 0 \implies a = 0 \lor b = 0$

#### Field $(F, +, \cdot)$
A **field** F , sometimes denoted by {F, +,* }, is a set of elements with two binary operations, called addition and multiplication, such that for all a, b, c in F the following axioms are obeyed:
- F is an integral domain; that is, F satisfies axioms A1 through *A5 and M1 through M6*
- **(M7)Multiplicative inverse:** per ogni $a \in F \setminus \{0\}$, esiste $a^{-1} \in F$ tale che:
  $$a \cdot a^{-1} = a^{-1} \cdot a = 1$$
In essence, a field is a set in which we can do addition, subtraction, multiplication, and division without leaving the set. Division is defined with the following rule:$\frac{a}{b} = a \cdot(b^{-1})$
- Example: $\mathbb{Q}, \mathbb{R}, \mathbb{C}$. Note:  $\mathbb{Z}$ **isn't** a field.

---
![[Pasted image 20261014184417.png|702]]
## Finite fields: $GF(p^n)$ 
Finite fields play a crucial role in many cryptographic algorithms.  That **the order of a finite field** must be a power of a prime $p^n$ , where $n$ is a positive integer
### Case $n = 1$: $GF(p)$
- Elements: $\{0, 1, \dots, p-1\}$
- Operations: addition e multiplication modulo $p$.
- **Why $p$ needs to be a prime number?**
 
  - If $m$ is not prime (e.g. modulo $8$): zero divisors appear ($2 \cdot 4 \equiv 0 \pmod 8$) and even elements **do not have a multiplicative inverse**. 
  - If $p$ is prime (e.g. modulo $7$): every non-zero element has a multiplicative inverse modulo $p$.
---

## Finite Fields for Computer Science: GF($2^n$)

Computers operate natively with powers of two (e.g., $2^8 = 256$ states for 1 byte). Because $2^n$ is not a prime number, standard modular arithmetic modulo $2^n$ does not yield a field (non-zero elements sharing factors with 2 lack multiplicative inverses). Therefore, **polynomial arithmetic** over $\text{GF}(2)$ is used instead.

### 1. Polynomial Representation

Elements of $\text{GF}(2^n)$ are represented as polynomials of degree at most $n-1$, with coefficients in $\text{GF}(2) = \{0, 1\}$:

$$f(x) = a_{n-1}x^{n-1} + \dots + a_1 x + a_0 \quad \text{where } a_i \in \{0, 1\}$$

There is a one-to-one mapping between each polynomial and an $n$-bit binary string: the coefficient $a_k$ corresponds to the $k$-th bit.

- **Example:** In $\text{GF}(2^4)$ (nibble-sized elements):
    - The polynomial $x^3 + x + 1$ maps to coefficients:
        $$(1 \cdot x^3) + (0 \cdot x^2) + (1 \cdot x^1) + (1 \cdot x^0) \longleftrightarrow 1011_2 \text{ (0x0B)}$$
    - The bit string $0110_2$ maps to:
        $$0 \cdot x^3 + 1 \cdot x^2 + 1 \cdot x^1 + 0 \cdot x^0 = x^2 + x$$
        

### 2. Operational Rules

#### Addition and Subtraction

Coefficients are added modulo 2 ($0+0=0$, $0+1=1$, $1+0=1$, $1+1=0$).

- This is identical to a bitwise **XOR** ($\oplus$) operation.
    
- Since $-1 \equiv 1 \pmod 2$, subtraction is identical to addition ($A - B = A + B$).
    
- **Example:** In $\text{GF}(2^4)$, add $A = x^3 + x + 1$ ($1011_2$) and $B = x^2 + x + 1$ ($0111_2$):
    
    $$(x^3 + x + 1) + (x^2 + x + 1) = x^3 + x^2 + (1+1)x + (1+1) = x^3 + x^2$$
    
    Using bitwise XOR:
    
    $$\begin{aligned} A: \quad & 1011_2 \\ B: \quad & 0111_2 \\ \hline A \oplus B: \quad & 1100_2 \longleftrightarrow x^3 + x^2 \end{aligned}$$
    

#### Multiplication & Irreducible Polynomials

Multiplication consists of two phases:

1. Standard algebraic polynomial multiplication over $\text{GF}(2)$ (hardware implementation: shift-and-XOR).
    
2. Reduction modulo an **irreducible polynomial** $m(x)$ of degree $n$ (a polynomial that cannot be factored into non-trivial polynomials of lower degree over $\text{GF}(2)$).
    

- **Example:** In $\text{GF}(2^3)$ with irreducible polynomial $m(x) = x^3 + x + 1$ ($1011_2$):
    
    - Multiply $A(x) = x^2 + 1$ ($101_2$) and $B(x) = x^2 + x$ ($110_2$):
        
        $$P(x) = (x^2 + 1)(x^2 + x) = x^4 + x^3 + x^2 + x$$
        
    - Reduce $P(x)$ modulo $m(x) = x^3 + x + 1$:
        
        - Divide $x^4 + x^3 + x^2 + x$ by $x^3 + x + 1$:
            
            $$x \cdot (x^3 + x + 1) = x^4 + x^2 + x$$
            
        - Subtract (XOR) to find the remainder:
            
            $$(x^4 + x^3 + x^2 + x) \oplus (x^4 + x^2 + x) = x^3$$
            
        - Divide $x^3$ by $x^3 + x + 1$:
            
            $$1 \cdot (x^3 + x + 1) = x^3 + x + 1$$
            
        - Remainder:
            
            $$x^3 \oplus (x^3 + x + 1) = x + 1 \longleftrightarrow 011_2$$
            
    - Therefore, $(x^2 + 1) \cdot (x^2 + x) \equiv x + 1 \pmod{x^3 + x + 1}$.
        

### 3. Key Algorithms

#### Extended Euclidean Algorithm for Polynomials

Computes the greatest common divisor $\gcd(a(x), b(x))$ and finds the Bézout polynomials $v(x)$ and $w(x)$ such that:

$$a(x)v(x) + b(x)w(x) = \gcd(a(x), b(x)) = 1 \implies w(x) \equiv b(x)^{-1} \pmod{a(x)}$$

This is the standard method for finding the **multiplicative inverse** of an element $b(x)$ modulo $m(x)$.

- **Example:** Find the inverse of $b(x) = x^2$ in $\text{GF}(2^3)$ modulo $m(x) = x^3 + x + 1$:
    
    1. Divide $m(x)$ by $b(x)$:
        
        $$x^3 + x + 1 = (x) \cdot (x^2) + (x + 1) \implies (x + 1) = m(x) + x \cdot b(x)$$
        
    2. Divide $x^2$ by $(x + 1)$:
        
        $$x^2 = (x + 1)(x + 1) + 1 \implies 1 = x^2 + (x + 1)(x + 1)$$
        
    3. Substitute back $(x + 1)$:
        
        $$1 = b(x) + (x + 1)[m(x) + x \cdot b(x)]$$
        
        $$1 = (x + 1)m(x) + [1 + x(x + 1)]b(x)$$
        
        $$1 = (x + 1)m(x) + (x^2 + x + 1)b(x)$$
        
    4. Modulo $m(x)$:
        
        $$(x^2 + x + 1) \cdot x^2 \equiv 1 \pmod{x^3 + x + 1}$$
        
        Thus, $(x^2)^{-1} = x^2 + x + 1$ ($111_2$).
        

### 4. Generators in Finite Fields

The non-zero multiplicative group $\text{GF}(2^n)^*$ is cyclic and has order $2^n - 1$. A **generator** (or primitive element) $g$ is an element whose successive powers produce all $2^n - 1$ non-zero field elements:

$$\text{GF}(2^n)^* = \{g^0, g^1, g^2, \dots, g^{2^n - 2}\}$$

- A root of a **primitive irreducible polynomial** acts as a generator for the field.
    
- Multiplication reduces to integer addition modulo $2^n - 1$ using lookup tables (log/antilog tables):
    
    $$g^i \cdot g^j = g^{(i+j) \bmod (2^n - 1)}$$
    
- **Example:** In $\text{GF}(2^3)$ modulo $m(x) = x^3 + x + 1$, let $g = x$. Since $m(g) = 0$, we have $g^3 = g + 1$:
    
    - $g^0 = 1 = 001_2$
        
    - $g^1 = x = 010_2$
        
    - $g^2 = x^2 = 100_2$
        
    - $g^3 = x + 1 = 011_2$
        
    - $g^4 = x \cdot g^3 = x^2 + x = 110_2$
        
    - $g^5 = x \cdot g^4 = x^3 + x^2 = (x + 1) + x^2 = x^2 + x + 1 = 111_2$
        
    - $g^6 = x \cdot g^5 = x^3 + x^2 + x = (x + 1) + x^2 + x = x^2 + 1 = 101_2$
        
    - $g^7 = g^0 = 1$ (cycle repeats)
        
- **Multiplication via exponents:** Multiply $A = x^2 + x$ ($g^4$) by $B = x^2 + 1$ ($g^6$):
    
    $$g^4 \cdot g^6 = g^{(4 + 6) \bmod 7} = g^{10 \bmod 7} = g^3 = x + 1 \longleftrightarrow 011_2$$
    
    This matches the result computed earlier via polynomial long division