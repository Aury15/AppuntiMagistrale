# Classic Cryptography

Cryptography is used to keep information **confidential**: only the intended receiver should be able to read a message. Every encryption algorithm, from the oldest to the most modern, relies on a **key**.
### Basic terminology

| Term                         | Meaning                                                                                |
| ---------------------------- | -------------------------------------------------------------------------------------- |
| **Plaintext**                | The original, readable message                                                         |
| **Ciphertext**               | The encrypted (scrambled) message                                                      |
| **Key**                      | A fixed-length sequence of bits that looks random; it controls the encryption          |
| **Encryption (enciphering)** | plaintext → ciphertext                                                                 |
| **Decryption (deciphering)** | ciphertext → plaintext                                                                 |
| **Encryptor / Decryptor**    | The entities that perform encryption / decryption                                      |
| **Cryptography**             | The study of encryption schemes (*ciphers*)                                            |
| **Cryptanalysis**            | Breaking a cipher *without* knowing the key                                            |
| **Cryptology**               | Cryptography + cryptanalysis                                                           |
| **Adversary**                | Anyone trying to compromise the confidentiality, integrity or availability of a system |

Before computers existed, people already needed ways to communicate **secret information**. Classical cryptography refers to cryptographic techniques developed before modern computer-based cryptography. The main idea was generally to transform a readable message into something that an unauthorized person could not understand.

>[!definition] Cryptography  
**Cryptography** is the study of techniques for protecting information by transforming it so that only authorized parties can understand it.

>[!important] Observation  
Classical methods did not necessarily provide cryptographic security in the modern computational sense. They often relied on physical secrecy, obscurity, or unusual procedures.

>[!Remember] Symmetric encryption model
>In **symmetric encryption** the *same key* is used to encrypt and to decrypt. A scheme has five ingredients:
>1. **Plaintext** – the input message.
>2. **Encryption algorithm** – applies substitutions/transformations to the plaintext.
>3. **Secret key** – an independent input; the exact transformation depends on it.
>4. **Ciphertext** – the output; it should look like random data.
>5. **Decryption algorithm** – the encryption algorithm run in reverse.
> Alice encrypts with key **K₁**, Bob decrypts with key **K₂**:
> - **K₁ = K₂** → symmetric encryption
> - **K₁ ≠ K₂** → asymmetric (public-key) encryption
For confidentiality of the data itself, symmetric encryption is preferred (it is much faster).

>[!definitions] Key
>A (long) string of random bits, where **every possible string has the same probability** of being chosen. Its length can be fixed by the algorithm or chosen at run time.

| Dimension                      | Options                                                                                            |
| ------------------------------ | -------------------------------------------------------------------------------------------------- |
| **Type of operation**          | *Substitution* (each element is replaced by another) vs. *Transposition* (elements are rearranged) |
| **Number of keys**             | *Symmetric* (single key, secret-key) vs. *Asymmetric* (two keys, public-key)                       |
| **How plaintext is processed** | *Block cipher* (one block at a time) vs. *Stream cipher* (one element at a time, continuously)     |
#### Requirements for secure use
- **Strong algorithm**: even with several ciphertexts (and the plaintexts that produced them), the attacker cannot recover the key or other plaintexts.
- **Secure key exchange and storage**: the shared key must reach the receiver over a **secure channel** and be kept secret. Symmetric keys must always remain confidential.

> **Kerckhoffs's principle**: a cryptosystem must stay secure even if *everything* about it, except the key, is public.
> **Shannon's maxim**: "the enemy knows the system".

### Frequency analysis
Each language has a well-known, public **distribution of letter frequencies**. In English the most frequent letters are E, T, A, O, I, N…

| Language | Most frequent letters |
| -------- | --------------------- |
| English  | E, T, A, O, N, I      |
| French   | E, A, S, I, T         |
| Spanish  | E, A, O, S, N         |
| German   | E, N, I, S, R, A      |
| Italian  | E, A, I, O, N         |
**Idea**: if a cipher just replaces letters, the frequencies are preserved. So if `d` is the most common letter in an English ciphertext, `d` most likely stands for `e`. Frequency analysis gives **probabilistic guesses** about which cipher letter corresponds to which plain letter.
##### Steps for frequency analysis (simplified)
1. Clean the ciphertext and normalize the case.
2. Count the frequency of each character.
3. Align the most frequent cipher letters with the known profile of each candidate language and see which gives plausible text.
4. Refine using language-specific patterns:
   - common letter pairs (TH, QU, CH, LL), Q usually followed by U;
   - common short words (in English: *it, is, to*);
   - recurring fragments (*la, le, de* in Romance languages, *-en, -er* in Germanic ones).

**Limits**: it works poorly on:
- **short messages** (not enough statistics);
- **polyalphabetic ciphers** (the same letter is encrypted in different ways);
- **modern encryption**.
It also requires the text to *have* a frequency structure: if the plaintext is **compressed** before encryption, the statistics are flattened and frequency analysis no longer works.

#### Threat
- **Communication model**: Alice and Bob share an encryption scheme (E, D, K₁, K₂) and a reliable channel; Alice wants to send a message **M** confidentially.
- **Attack on confidentiality**: obtain partial or total information about M.
![[Pasted image 20261008190741.png]]
#### Adversary
| Passive adversary                                    | Active adversary                                                                            |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| Only reads exchanged messages, without changing them | Can modify messages from Alice or Bob, and send fake messages pretending to be someone else |
The adversary knows the algorithms E and D, the message space,  at least part of the ciphertext but does not know the keys $K_1, K_2$.
#### Security goals
If the keys are unknown, then it must be **hard**:
- to obtain even *partial* information about the message;
- to find the key, *even if the plaintext is known*.
**Hard = computationally hard**: it takes an impractical amount of time even with the most powerful computers available (e.g., brute force is too costly).

>[!definitions] Strong encryption
>The term strong encryption refers to encryption schemes that make it impractically difficult for unauthorized persons or systems to gain access to plaintext that has been encrypted. An encryption scheme is **strong** when it is practically impossible for unauthorized parties to recover the plaintext. This requires:
>- an appropriate cryptographic algorithm;
>- a sufficiently long key;
>- appropriate protocols around the algorithm;
>- a well-engineered implementation;
>- no deliberately hidden flaws (**backdoors**).

### Attack
An **attack** in any intentional attempt to compromise the security of a computer system, network, or data by violating one or more core principles of information security, such as confidentiality, integrity, or availability. Some attacks do not directly target information security principles but serve indirect purposes, such as enabling future compromise or gathering intelligence. 
>[!important] 
>Remember, cryptography plays a fundamental role among the measures designed to uphold information security
##### Cryptanalysis vs. Brute force
- **Cryptanalysis**: exploits the structure of the algorithm plus some knowledge of the plaintext (its general features, or known plaintext–ciphertext pairs).
- **Brute force**: try every possible key until readable plaintext appears. On average **half** of the keys must be tried.
If either succeeds, the result is catastrophic: **all past and future messages** encrypted with that key are exposed.
An **oracle** is a theoretical **black box** the attacker can query to get outputs (ciphertexts or plaintexts) without seeing how it works inside. Example: an oracle that decrypts every ciphertext *except* the one the attacker actually cares about.

| Attack                    | Acronym | What the attacker has                                                | Oracle? |
| ------------------------- | ------- | -------------------------------------------------------------------- | ------- |
| Eavesdropping             | —       | Secretly listens to a private communication                          | No      |
| Ciphertext-only           | COA     | Only ciphertexts; tries to deduce plaintext or key                   | No      |
| Known-plaintext           | KPA     | Some plaintext–ciphertext pairs; uses them to infer the key          | No      |
| Chosen-plaintext          | CPA     | Can encrypt plaintexts of their choice and see the ciphertexts       | Yes     |
| Adaptive chosen-plaintext | —       | Like CPA, but each new plaintext is chosen based on previous results | Yes     |
| Chosen-ciphertext         | CCA     | Can decrypt ciphertexts of their choice, **except** the target one   | Yes     |
Other type:

| Attack                       | Description                                                                                     |
| ---------------------------- | ----------------------------------------------------------------------------------------------- |
| **Cryptanalysis**            | Any mathematical technique to break or weaken an algorithm                                      |
| **Brute force**              | Try all keys until the right one is found                                                       |
| **Side-channel**             | Exploits physical data leaked by a device: timing, power consumption, electromagnetic radiation |
| **Replay**                   | Re-sends a previously captured valid message to fool a system                                   |
| **Man-in-the-middle (MITM)** | Intercepts, and possibly alters, the communication between two parties                          |

### How Secure Is "Secure Enough"?
- **Unconditional (perfect) security**: the ciphertext does not contain enough information to determine the plaintext, *no matter how much computing power* the attacker has. Only the **one-time pad** achieves this: each message is encrypted with a random key **as long as the message**, and that key is **never reused**.
- **Computational security**: breaking the cipher costs more than the information is worth, or takes longer than the information stays useful.
##### About brute force
- Modern computers test about 10⁶–10¹⁵ keys/second, depending on the hardware.
- A key of **n bits** gives **2ⁿ** possible keys; with X keys the attacker succeeds after **X/2** tries on average.
- The practical limit today is around **n = 80** bits (and it keeps rising). With **128-bit** keys, even at 10¹² keys/s, it would take over **10¹⁹ years** on average.
- **Trade-off**: a longer key means more security but **lower performance**.
- Brute force assumes the attacker can **recognize the correct plaintext** automatically. That is easy for English text, much harder for compressed or binary data.
### Cesar cipher
The **Caesar cipher** is one of the simplest substitution ciphers. The basic idea is to shift every letter of the alphabet by a fixed number of positions.

For example, with a shift of **3**:
```
Plain:   A B C D E ... W X Y Z
Cipher:  D E F G H ... Z A B C
```
Thus:
```
A → D
B → E
C → F
...
X → A
Y → B
Z → C
```
The key is therefore:
```
k = 3
```
There are only 26 possible shifts, and one of them is the identity transformation. Therefore, in practice, there are only **25 meaningful keys**.

> [!important] Weakness  
> The Caesar cipher can be broken simply by trying every possible key.

This is a **brute-force attack**. Since there are only 25 possibilities, the attack is trivial.

### Alphabetic / Monoalphabetic Substitution
Instead of simply shifting the alphabet, we can create an arbitrary permutation of the alphabet.
For example:
```
Plain:   A B C D E ... W X Y Z
Cipher:  D U Z O L ... Y B A R
```
Every plaintext letter is mapped to exactly one ciphertext letter.
There are: 26! possible permutations. Numerically:  $26!≈4.03×102626! \approx 4.03\times10^{26}$ This is enormously larger than the Caesar key space.
However, a large key space does **not** automatically make the cipher secure.

> [!important] Main weakness  
> A monoalphabetic cipher always uses the **same substitution** for a given letter.

Therefore, it preserves the statistical properties of the original language.
For example, if `E` is the most common letter in English, the ciphertext symbol corresponding to `E` will tend to be one of the most frequent symbols.
This makes the cipher vulnerable to **frequency analysis**.

### Polyalphabetic Substitution
A way to improve upon monoalphabetic substitution is to use **multiple substitution alphabets**. Instead of using the same substitution for every character, different substitutions are used at different positions.
For example, suppose we have three substitution alphabets:
```
Alphabet 0
Alphabet 1
Alphabet 2
```
The rule used for a character depends on its position:
```
position 0 → alphabet 0
position 1 → alphabet 1
position 2 → alphabet 2
position 3 → alphabet 0
position 4 → alphabet 1
...
```
In general, if there are ll alphabets, the permutation used is determined by: $i+1$ where $i$ increases modulo $l$. The purpose is to make simple frequency analysis more difficult.
However:
> [!important] Limitation  
> If the ciphertext is sufficiently long, frequency analysis can still be effective.

This idea leads to the **Vigenère cipher**, discussed later.

### Enigma 
The **Enigma machine** was one of the most sophisticated classical cryptographic systems. It was used during World War II. The machine encrypted each letter by passing it through a sequence of rotating electrical rotors. Because the rotors changed position after each keystroke, the substitution applied to a letter could change from one occurrence to another. Therefore, Enigma was effectively using a changing substitution mechanism. The system was eventually broken by allied cryptanalysts. The slide emphasizes that, despite its complexity, its keyspace was approximately: $10^{23} \text{  to  } 10^{26}$  
>[!important] Lesson  
A cryptosystem may look extremely complicated and still be vulnerable if its effective keyspace or structure is insufficiently secure.

### Playfair cipher
The best-known **multi-letter** substitution cipher: it encrypts **pairs of letters** (digrams) as single units. Invented by Charles Wheatstone (1854), used by the British Army in WWI and by the Allies in WWII.
**Key matrix**: a 5×5 grid built from a keyword (letters of the keyword first, without repetitions, then the rest of the alphabet; I and J share a cell). Example with keyword `MONARCHY`:
```
M O N A R
C H Y B D
E F G I/J K
L P Q S T
U V W X Z
```
**Rules** (the plaintext is split into pairs):
- If a pair has two **equal letters**, insert a filler (`x`) between them.
- **Same row** → each letter is replaced by the one on its **right** (wrapping around).
- **Same column** → each letter is replaced by the one **below** (wrapping around).
- **Otherwise** → each letter is replaced by the letter in **its own row** but in the **other letter's column** (the opposite corners of the rectangle they form).

**Strength and weakness**:
- 26 × 26 = **676 digrams** instead of 26 letters, so single-letter frequency analysis is much harder.
- Considered unbreakable for decades, but it still preserves much of the language's structure: **a few hundred letters** of ciphertext are usually enough to break it.
