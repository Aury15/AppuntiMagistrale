## AES structure

AES processes fixed-size blocks of data (plaintext) of 16 bytes (128 bits) formatted as a 4 $\cdot$ 4 matrix called **state**. AES isn't a Feistel's network but applies transformations on the entire matrix for every round. The number of round $N$ depend on the length of the key:
- **Chiave a 128 bit (16 byte)**: 10 round, espansa a 44 word (176 byte).
- **Chiave a 192 bit (24 byte)**: 12 round, espansa a 52 word (208 byte).
- **Chiave a 256 bit (32 byte)**: 14 round, espansa a 60 word (240 byte).