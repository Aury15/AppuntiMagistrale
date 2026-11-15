## AES structure

AES processes fixed-size blocks of data (plaintext) of 16 bytes (128 bits) formatted as a 4 $\cdot$ 4 matrix called **state**. AES isn't a Feistel's network but applies transformations on the entire matrix for every round. The number of round $N$ depend on the length of the key:
- **128-bit (16 byte) key**: 10 rounds, expanded to 44 words (176 bytes).
- **192-bit (24 byte) key**: 12 rounds, expanded to 52 words (208 bytes).
- **256-bit (32 byte) key**: 14 rounds, expanded to 60 words (240 bytes).
The process start with an initial transformation followed by 