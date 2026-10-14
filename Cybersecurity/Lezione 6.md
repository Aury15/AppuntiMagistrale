# Block ciphers and DES

>[!Definitions] Stream cipher
encrypts data continuously (bit by bit) as they arrive, does not wait to have a whole block. It generates a stream of pseudorandom data called keystream (noise), this stream is combined (with the XOR operation) to the plaintext. They are very fast and require little code, excellent for data that arrive in real time and never end (4G).  


>[!definitions] Block cipher 
>Given a block P of plaintext of h bits (h fixed), a key k of fixed # of bits, a cyptographic protocol $E_k$  produces a block C of h bits. function of P and k (for a fixed E).
>Takes a block of data of fixed dimension and grinds it using the key, giving in output an encrypted block of the same dimension. So it processes one block at a time, can reuse keys securely and is the most common method.
> note: lengths of both block ad key (the number of bits) are fixed (not necessarily equal)
> ![[Pasted image 20261014182538.png]]

#### Block VS stream ciphers
Block cipher encrypts, fixed size, block (128 bits) at a time, stream cipher encrypts one bit or byte at time using a keystream. So the block cipher are better for structured data (like files bc the data are usually stored in known formats with a known weight or like database and disk sectiors) and stream ciphers are better for real-time communications. 

![[Pasted image 20261014182600.png|503]]

#### Desired properties
We need some features that strengthen resistance to cryptanalytic attacks
- **Invertibility**: must be able to recover original plaintext;
- **Avalanche effect**: small changes = big output changes;
- **Non linearity**: avoids predictability and simple math reversal;
- **Key sensitive**: small key differences yield vastly  different outputs.

### Late millennium block ciphers

- **DES, data encryption standard**: adopted in 1977, was standard for decades, is a symmetric block cipher. Uses:
	- Block: 64 bits;
	- Key: 56 effective bits;
	- Structure: feistel with 16 rounds.
	The algorithm transform 64bit input in a series of steps into a 64bit output and the same steps with the same key are used to reverse the encryption.
	![[Pasted image 20261014182723.png|509]]
	*des example on slide 28 with avalanche effect*
	**Weak points**: Key of 56 bits is too short for modern computers. In 1998 broken in 3 days; today broken in few minutes. **Avalanche Effect**: Small change (1 bit) in plaintext or key produces drastic change in ciphertext. DES possesses this property.
- **3DES, triple DES**: to solve DES weakness without changing algorithm, DES is used three times. $C = E_{k_3}(D_{k_2}(E_{k_1}(P))$. Uses 2 or 3 different keys (112 or 186 bits), is secure but slow(3 times slower than DES).
*approfondisci 3DES con slide + image of triple DES + 3DES con 3key*
- **RC-2**: adopted in 1987, is designed for exporting cryptography within IBM lotus notes. Uses:
	- Block: 64 bits;
	- Key: variabile size;
	Weak points: vulnerable to an attack using 234 chosen plaintext.
- **IDEA**: Uses:
	- Block: 64 bits;
	- Key: 128 bit.
		Strong only weakned variants have been broken, outdated.
- **Blowfish**: Uses:
	- Block: 64 bits;
	- Key: variable length from 32 up to 448 bits.
	Still strong but deprecated.
- **RCS**: Uses:
	- Block: variable block size (32, 64, 128);
	- Key: variable length from 0 up to 2040 bits.
	- Rounds: 0 to 255 (round di cosa?).
	distributed.net (or Distributed Computing Technologies, Inc. or DCTI) is a worldwide distributed computing effort that is attempting to solve large scale problems using  otherwise idle CPU or GPU time. It is a non-profit organization
- **AES, advanced encryption standard**:  To replace 3DES, NIST held contest won by Rijndael algorithm, which became AES in 2001.
	- Block: 128 bit;
	- Key: 128, 192 or 256 bit;
	- Structure: Does NOT use Feistel. Uses Substitution-Permutation Network (SPN)
	- Round: 10, 12, or 14 depending on the key
	Very strong.

### Feistel cipher
Many block algorithms (like DES) use Feistel structure.  
Feistel proposed the use of a cipher that alternates substitutions and permutations:
- *Substitutions*: Each plaintext element or group of elements is uniquely replaced Substitutions by a corresponding ciphertext element or group of elements
- *Permutation*: No elements are added or deleted or replaced in the sequence is changed
Idea is to approximate ideal substitution cipher for very 
large blocks utilizing alternation of Confusion and Diffusion:
- *Diffusion*: Every digit of plaintext influences many digits of ciphertext. 
- *Confusion*: Relation between key and ciphertext must be complex.
![[Pasted image 20261014182837.png|479]]

#### Feistel cipher design features
- **Block size**: larger block sizes means greater security but reduced encryption/decryption speed for a given algorithm;
- **Key size**: Larger key size means greater security but may decrease encryption/decryption speeds;
- **Number of rounds**: The essence of Feistel cipher is that a single round offers inadeguate security but that multiple rounds offer increasing security;
- **Subkey generation algorithm**: greater complexity in this algorithm should lead to grater difficulty of cryptanalysis;
- **Round function F**: grater complexity
- **Fast software encryption/decryption**
- **Ease of analysis**
![[Pasted image 20261014182854.png]]

**AVERAGE TIME REQUIRED FOR EXHAUSTIVE KEY SEARCH**
![[Pasted image 20261014182942.png]]

### Block cipher design principles:
- **Number of rounds**: the greater the number of rounds, the more difficult it is  to perform cryptanalysis. In general, the criterion should be that the number of rounds is chosen so that known  cryptanalytic efforts require greater effort than a simple brute-force key search attack. If DES had 15 or fewer rounds,  differential cryptanalysis would require less effort than a brute-force key search.
- **Design of function F**: the heart of a Feistel block cipher is the function F. The more nonlinear F, the more difficult  any type of cryptanalysis will be. The SAC and BIC criteria appear to strengthen the effectiveness of the confusion function. 
	- **Strict avalanche criterion** (*SAC*): States that any output bit j of an S-box should change with probability 1/2 when any single input bit i is inverted for all i , j
	- **Bit independence criterion** (*BIC*): States that output bits j and k should change independently when any single input  bit i is inverted for all i , j , and k.
- **Key schedule algorithm**: with any Feistel block cipher, the key is used to generate one subkey for each round. In general, we would like to select subkeys to maximize the difficulty of deducing individual subkeys and the difficulty of working back to the main key. It is suggested that, at a minimum, the key schedule should guarantee key/ciphertext Strict Avalanche Criterion and Bit Independence Criterion. 
 **Iteration DES**
![[Pasted image 20261014183039.png]]
 