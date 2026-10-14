
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

## Campi Finiti per l'Informatica: $GF(2^n)$

I calcolatori lavorano con potenze di due ($2^8 = 256$ per 1 byte). Poiché $2^n$ non è primo, non possiamo usare la semplice aritmetica modulo $2^n$. Si usa quindi l'**aritmetica dei polinomi**.

### Rappresentazione Polinomiale
Elementi rappresentati come polinomi a coefficienti in $GF(2) = \{0, 1\}$:
$$f(x) = a_{n-1}x^{n-1} + \dots + a_1 x + a_0 \quad \text{con } a_i \in \{0, 1\}$$
- Corrispondenza biunivoca con stringhe di bit:
  $$x^2 + 1 \longleftrightarrow 101_2$$

### Regole Operative
1. **Addizione e Sottrazione:**
   - I coefficienti sono sommati modulo 2 ($1+1=0, 0+1=1$).
   - Equivalente a un'operazione bit a bit di **XOR ($\oplus$)**.
   - Somma e sottrazione coincidono!
2. **Moltiplicazione:**
   - Moltiplicazione algebrica normale tra polinomi (a livello hardware: **shift and XOR**).
   - Riduzione modulo un **polinomio irriducibile** $m(x)$ di grado $n$.

> **Polinomio Irriducibile (o primo):** Polinomio che non può essere fattorizzato nel prodotto di due polinomi entrambi di grado inferiore su quel campo.

---

## Algoritmi Chiave

### Algoritmo di Euclide Esteso per Polinomi
Usato per trovare il massimo comune divisore $\gcd(a(x), b(x))$ e calcolare l'**inverso moltiplicativo**:
$$a(x)v(x) + b(x)w(x) = \gcd(a(x), b(x)) = 1 \implies w(x) \equiv b(x)^{-1} \pmod{a(x)}$$

### Generatori nei Campi Finiti
- Un generatore $g$ è un elemento le cui potenze generano tutti gli elementi non nulli:
  $$F^* = \{g^0, g^1, g^2, \dots, g^{q-2}\}$$
- Una radice del polinomio irriducibile funge da generatore del campo.
- La moltiplicazione si semplifica notevolmente sommando gli esponenti modulo $2^n - 1$:
  $$g^i \cdot g^j = g^{(i+j) \bmod (2^n-1)}$$