## AES structure

AES (Advanced Encryption Standard) is a symmetric block encryption algorithm: it takes a readable message (Plaintext), transforms it using a secret key (Key), and produces an unreadable ciphertext (Ciphertext), allowing anyone with the exact same key to reconstruct the original message. So this algorithm does not cypher letter by letter but it splits the message into fixed-size packets of 16 bytes ($in_0, in_1, \dots, in_{15}$) (i.e., 128 bits, since $1\text{ byte} = 8\text{ bit}$). If a file is larger, it is split into many 16-byte blocks that are processed separately. AES put this 16 byte block in a matrix $4 \times 4$  called **state**. The matrix is called state because represent the actual state of data while they go through the various mixing steps.

>[!Observation] Filling order of the matrix
>**Notice**: The 16 bytes are inserted into the matrix by columns (from top to bottom, starting from the left), not by rows.
>$$\text{State} = \begin{pmatrix} s_{0,0} & s_{0,1} & s_{0,2} & s_{0,3} \\ s_{1,0} & s_{1,1} & s_{1,2} & s_{1,3} \\ s_{2,0} & s_{2,1} & s_{2,2} & s_{2,3} \\ s_{3,0} & s_{3,1} & s_{3,2} & s_{3,3} \end{pmatrix} = \begin{pmatrix} in_0 & in_4 & in_8 & in_{12} \\ in_1 & in_5 & in_9 & in_{13} \\ in_2 & in_6 & in_{10} & in_{14} \\ in_3 & in_7 & in_{11} & in_{15} \end{pmatrix}$$

Every column is called **word**. The secret key is also arranged in exactly the same way, in columns.
Shuffling a matrix just once would not be sufficient to guarantee security, because an attacker could guess the inverse transformation. For this reason, AES repeats a shuffling cycle called Round multiple times. While the data block size is always fixed at 16 bytes (128 bits), the key can have three different lengths that determine the number $N$ of rounds to be performed:
- **128-bit (16 byte) key**: 10 rounds, expanded to 44 words (176 bytes).
- **192-bit (24 byte) key**: 12 rounds, expanded to 52 words (208 bytes).
- **256-bit (32 byte) key**: 14 rounds, expanded to 60 words (240 bytes).


#### The process of the algorithm 
1. **Initial transformation: (`AddRoundKey`):**  before round 1 even starts, the **state** matrix is combined with the original key.
2. **Standard round `round 1 to round N-1`**: in every round, the state matrix undergoes 4 consecutive transformations. 
3. **Final round `N`**: the last cycle execute only 3 transformations (skip the `MixColumns` step to maintain structural symmetry with decryption).
4. **Output**: after the last round, the 16 byte of the final state matrix are read back column-wise and form the Ciphertext.

#### The 4 transformations in a round
Every standard round applies four operations on the state matrix:
1. `SubBytes`: every single bytes $s_{r,c}$ of the matrix is replaced with another byte by reading a fixed table $16 \times 16$ *S-Box*. The first 4 binary digits (first hexadecimal character) of the byte indicate the row of the S-box, while the last 4 (second hexadecimal character) indicate the column. 
	1. **Why it's done**: It prevents simple (linear) mathematical relationships between the input and output bits. The S-Box is constructed by computing the multiplicative inverse in the finite field $GF(2^8)$ followed by a bitwise transformation, making the algorithm immune to known cryptanalytic attacks.
2. `ShiftRows`: The bytes of the array are cyclically shifted to the left by a different number of positions for each row. Before this stage, the four bytes of a column had always been isolated from each other. By shifting the rows, the four bytes belonging to a single column are scattered across four different columns.
3. `MixColumns`: After `ShiftRows` distributes the bytes horizontally, `MixColumns` shuffles them vertically. Each column of the matrix is ​​multiplied by a constant matrix $4 \times 4$:After `ShiftRows` distributes the bytes horizontally, `MixColumns` shuffles them vertically. Each column of the matrix is ​​multiplied by a constant matrix $4 \times 4$:

$$\begin{pmatrix} 02 & 03 & 01 & 01 \\ 01 & 02 & 03 & 01 \\ 01 & 01 & 02 & 03 \\ 03 & 01 & 01 & 02 \end{pmatrix} \times \begin{pmatrix} s_{0,c} \\ s_{1,c} \\ s_{2,c} \\ s_{3,c} \end{pmatrix} = \begin{pmatrix} s'_{0,c} \\ s'_{1,c} \\ s'_{2,c} \\ s'_{3,c} \end{pmatrix}$$
The operations (multiplication and sum) are in the $g$