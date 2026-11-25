---
cssclasses:
  - dds-notes
tags:
  - DDS
  - Blockchain
  - Consensus
  - SmartContracts
  - Bitcoin
  - Ethereum
---

# Dependable Distributed Systems — Blockchain, Consensus & Smart Contracts


## Table of Contents
1. [[#1. Blockchain Tech Ingredients]]
2. [[#2. Consensus]]
3. [[#3. Safety and Liveness of Bitcoin]]
4. [[#4. Smart Contracts: Beyond Transactions]]

---

# 1. Blockchain Tech Ingredients

## 1.1 Transactions & What Is a Blockchain? (Slides 1–7)

At the heart of any blockchain is the **transaction**: an atomic event supported by the protocol that records *who* (`FROM`), *to whom* (`TO`), *when* (`WHEN`), and *what* (`WHAT`) is being transferred or executed.

![[DDS_2_02_transaction.png]]

> [!definition] Blockchain
> A **blockchain** is a distributed ledger capable of recording transactions in an **immutable, verifiable, and permanent** way.

A blockchain system is characterized along two main orthogonal axes:
- **Who decides what transactions are recorded and in which order (Consensus)?**
  - **Permissioned**: only a known, authorized set of nodes participates in consensus.
  - **Permissionless**: any node can join and participate in consensus.
- **Who can read the transactions written on-chain or submit new proposals?**
  - **Private**: read/write access is restricted to authorized participants.
  - **Public**: anyone can read the ledger and submit transactions.

The fundamental challenge in blockchains is moving **from trust** (relying on a central authority) **to consensus** (decentralized governance). In a distributed network, nodes experience **network delays**, **message losses**, and **information asymmetry**—different nodes hear about different transactions at different times.

To build a blockchain, six core technologies work together:
1. **Asymmetric Encryption** (digital signatures & identities)
2. **Hash Functions** (integrity, chaining, puzzles)
3. **Merkle Trees** (efficient transaction verification)
4. **Key-Value Databases** (storing blocks and state)
5. **P2P Communication Protocols** (gossiping blocks and transactions)
6. **Consensus** (agreeing on the order of blocks)

In this first part, we temporarily assume consensus is magically given (an oracle "Zeus" tells everyone which pending transactions go into the next block) so we can focus on the remaining cryptographic and data-structure ingredients.

---

## 1.2 Hash Functions & Cryptographic Hashes (Slides 8–10)

A **hash function** maps an arbitrary-length input $x$ to a fixed-size $k$-bit output $y = H(x) \in Y$, where $|Y| = 2^k$.

![[DDS_2_08_hash_function.png]]

A good hash function satisfies two baseline properties:
- **P1 — Fast to compute**: computing $H(x)$ is computationally cheap.
- **P2 — Low probability of collision**: $H(x) = H(x')$ for $x \neq x'$ occurs with very low probability. To minimize collisions, $H$ must distribute outputs uniformly over $Y$ (approximating a **Random Oracle**). By the **Birthday Paradox**, the probability of at least one collision among $m$ hashes of size $k$ bits is approximately:
  $$P(\text{collision}) \approx 1 - \exp\!\left(-m^2 \cdot 2^{-k}\right)$$

A **cryptographic hash function** adds a third, crucial property:
- **P3 — Pre-image & Collision Resistance (Difficult to invert)**:
  - Given $y'$, it is computationally infeasible to find any $x'$ such that $H(x') = y'$ (the only way is brute-force exhaustive search).
  - **Collision resistance**: it is computationally infeasible to find two distinct inputs $x \neq x'$ such that $H(x) = H(x')$.
  - Output spaces must be large (typically 128-bit, **256-bit** as in SHA-256, or longer) so brute-forcing collisions is intractable.

**Main uses of cryptographic hashes in blockchains:**
- **Chain of blocks**: linking each block to its predecessor's hash.
- **Commitments**: committing to a value (e.g., a bid) without revealing it prematurely.
- **Hash pointers**: content-addressed storage (e.g., IPFS).
- **Hash puzzles**: preventing spam and enabling Proof-of-Work (e.g., requiring the first $n$ bits of an hash to be zero; even at 1 hash every $10\text{ ns} = 10^{-9}\text{ s}$, large $n$ takes significant work).
- **Tamper-proof data structures**: such as Merkle Trees.
- **Digital signatures**: hashing transactions before signing for immutability, authenticity, and verifiability.

---

## 1.3 Block Structure, Chaining & Time (Slides 11–16)

A Bitcoin **block** consists of a **block header** and a **block body** (the list of transactions). The block header contains metadata including the **Previous Block Hash**, the **Merkle Root** of the transactions, a **Timestamp**, the **Difficulty target**, and a **Nonce**.

![[DDS_2_11_block_structure.png]]

Crucially, **the block header alone is sufficient to autonomously check the integrity of the blockchain** (e.g., verifying the hash chain and PoW). By linking each block's `Previous Hash` to `Hash(header)` of the preceding block, blocks form an **immutable, ordered chain**: modifying any confirmed transaction changes its block's Merkle Root, which changes `Hash(header)`, breaking the link to every subsequent block.

![[DDS_2_13_chain_of_blocks.png]]

### Time in the Blockchain (Slide 14)
While **block ordering** is guaranteed by the hash-linked structure, **block timestamps** record when the miner *claims* to have created the block. Timestamps serve three roles:
1. **Adjusting mining difficulty** (comparing actual vs. target block intervals).
2. **Supporting time-locked transactions** (`nLockTime`).
3. **Increasing the search space** for miners during Proof-of-Work.

### No Trust & Full Nodes (Slides 15–16)
In a trustless system, the only way to be 100% certain of the blockchain's integrity is to run a **Full Node**, download the entire blockchain, and verify every block and transaction independently—delegating verification to someone else reintroduces **trust**. Once verified, you can query your local node using **RPC (Remote Procedure Call)** interfaces (e.g., `bitcoin-cli` commands like `getblockheader` or `getrawtransaction`).

---

## 1.4 Transaction IDs, Malleability & SegWit (Slides 17–21)

In Bitcoin, each transaction is uniquely identified by its **TXID**, computed as the **double SHA-256** of the serialized raw transaction bytes, with the resulting byte order reversed (little-endian to big-endian display):

```python
import hashlib
transaction_bytes = bytes.fromhex(transaction_hex)
tx_hash = hashlib.sha256(hashlib.sha256(transaction_bytes).digest()).digest()
txid = tx_hash[::-1].hex()
```

### Transaction Malleability (Slides 18–19)
Before SegWit, Bitcoin suffered from **transaction malleability**: an attacker (or relay node) could slightly modify a non-signed part of a transaction *before* it was confirmed in a block, changing its **TXID** while keeping the transaction valid and moving the exact same coins to the intended recipient.
- **Why this happened**: When signing an input, signatures themselves are stripped out (a signature cannot sign itself). However, the TXID hashed the *entire* transaction, including the signature bytes and length encodings. Adding dummy data or changing length encodings (e.g., encoding `120` bytes as `0120`) altered the TXID without invalidating the signature.
- **Impact**: Accepting chains of unconfirmed transactions became unsafe, because a later transaction referencing an earlier unconfirmed TXID would break if that TXID was mutated.

### Throughput, SegWit & the Bitcoin Cash Fork (Slides 20–21)
A blockchain's **throughput** depends on two parameters:
- **Block time**: how often a new block is added (~10 minutes in Bitcoin).
- **Block size**: how much data fits in each block (originally 1 MB in Bitcoin).

**Segregated Witness (SegWit, BIP141)** solved both malleability and capacity constraints by moving the digital signature ("witness" data, which can take up to 65% of a transaction's size) *outside* the base transaction block:
1. Modifying the signature no longer changes the TXID, **eliminating transaction malleability**.
2. Removing witness data from the 1 MB base limit allows significantly more transactions per block (effective block weight up to ~4 MB).

![[DDS_2_20_segwit.png]]

In August 2017, disagreements over how to scale Bitcoin led to a **hard fork** creating **Bitcoin Cash (BCH)**:
- **BTC**: kept smaller blocks + SegWit (soft fork) + Layer-2 solutions (e.g., Lightning Network).
- **BCH**: increased the on-chain block size directly (up to 32 MB).
- At the fork block, all existing UTXOs were duplicated on both chains; spending coins on one chain afterward does not affect the other.

---

## 1.5 Digital Signatures, Pseudonymity & Privacy (Slides 22–28)

A **digital signature** binds a transaction to its author using asymmetric cryptography: the **Signer** signs the transaction hash with their private key ($SK$), and any **Verifier** checks the signature using the corresponding public key ($PK$).

![[DDS_2_22_digital_signature.png]]

In a decentralized blockchain, **we do not need a Certification Authority (CA)**. **Blockchain addresses are derived directly from Public Keys**. Because generating a new key pair is free and requires no registration, a single user can create **multiple identities** effortlessly—a property known as **pseudo-anonymity**.

However, pseudo-anonymity is **not** full anonymity:
- All addresses and transactions are **public** on the ledger.
- While an address by itself reveals nothing about your real-world identity, your **transaction history** and graph patterns can be analyzed (**Bitcoin Transaction Graph Analysis**) to cluster addresses and link them to real people (e.g., when interacting with exchanges or merchants).

![[DDS_2_27_tx_graph.png]]

Ultimately, digital signatures provide the three pillars of transaction security: **Identity**, **Immutability**, and **Verifiability**.

---

## 1.6 Obtaining Tokens & the UTXO Model (Slides 29–37)

Users can obtain tokens in four ways:
1. Receiving transactions from others.
2. Mining (earning the **Block Reward**).
3. Earning **Transaction Fees** (as a miner).
4. Buying tokens (e.g., on exchanges).

### The UTXO (Unspent Transaction Output) Model (Slides 31–35)
Bitcoin does **not** store account balances. Instead, it uses the **UTXO model**, where value lives in discrete, unspent chunks of coins produced by previous transactions:
- **Inputs**: references to previous UTXOs being spent, accompanied by an **unlocking script** (`scriptSig` / signature) proving ownership.
- **Outputs**: newly created UTXOs specifying an amount and a **locking script** (`scriptPubKey`) defining who can spend them next.

![[DDS_2_31_utxo_model.png]]

For example, if Alice has a `1 BTC` UTXO and wants to pay Bob `0.6 BTC`, she must consume the *entire* `1 BTC` UTXO as input and create two outputs: `0.6 BTC` locked to Bob's public key ($PK_{\text{Bob}}$) and `0.4 BTC` sent back to herself ($PK_{\text{Alice}}$) as **change** (minus any transaction fee, which is implicitly `sum(inputs) - sum(outputs)`).

![[DDS_2_32_utxo_example.png]]

Verification relies on asymmetric cryptography:
$$\text{Signature} = \text{Sign}(\text{msg}, SK_{\text{User}}), \qquad \text{Verify}(\text{msg}, \text{Signature}, PK_{\text{User}}) == \text{True}$$
The private key $SK_{\text{User}}$ is never revealed—only the signature is broadcast. Over time, transactions form a **directed acyclic graph of chained UTXOs**, where the current UTXO set sits at the unspent leaves.

![[DDS_2_34_tx_chained.png]]

### UTXO vs. Account Model (Slides 35–37)

![[DDS_2_35_utxo_vs_account.png]]

**Benefits of the UTXO model:**
1. **Better Parallelization and Concurrency**: transactions spending disjoint UTXOs can be validated in parallel with no shared global state.
2. **Simpler Verification and Stateless Validation**: each transaction is self-contained; nodes only check whether the referenced UTXOs are unspent (no race conditions). *(Trade-off: complex stateful smart contracts are harder to build than in account-based systems like Ethereum.)*
3. **Improved Privacy**: encouraging fresh addresses for change outputs makes tracking slightly harder.
4. **Inherent Double-Spend Protection**: each UTXO can be consumed only once, preventing double-spending without complex locks or mutexes.

**The Role of the Wallet:** Because balances are scattered across many UTXOs, a UTXO wallet cannot simply query a single "balance" field. Wallets maintain or query an off-chain **indexer** that scans the blockchain, tracks relevant transactions, and sums all unspent outputs belonging to the user's addresses.

---

## 1.7 Node Types & Transaction Validation Rules (Slides 38–41)

- **Validators**: Every Bitcoin node acts as a validator, checking incoming transactions and blocks against consensus rules before storing or relaying them.
- **Miners**: Nodes that participate in **Proof-of-Work consensus** by assembling valid transactions into candidate blocks and competing to solve the hash puzzle. Because miners hear about transactions at different times, each miner builds a different candidate block; if another miner wins first, they discard their work and start on a new block.

Bitcoin's **transaction validation rules** (Slide 39) enforce strict checks before accepting or relaying a transaction:
- Correct syntax and non-empty input/output lists; size $\ge 100$ bytes and $< \text{MAX\_BLOCK\_SIZE}$.
- Output values and their sum within $(0, 21\text{M}]$ BTC; sum of inputs $\ge$ sum of outputs; fee sufficient for inclusion.
- No coinbase-like dummy inputs (`hash=0, N=-1`); `nLockTime <= INT_MAX`; signature operations under limit; standard scripts (`isStandard`).
- Referenced outputs must exist (in main chain or mempool—otherwise the transaction is placed in the **orphan pool**), must not already be spent (in a block or another mempool tx), and if from a **coinbase** transaction, must have at least **100 confirmations** (`COINBASE_MATURITY`).
- Unlocking scripts (`scriptSig`) must validate against the corresponding locking scripts (`scriptPubKey`).

### Full, Light, and Client Nodes (Slide 41)
- **Full Node**: Downloads and independently validates the **entire blockchain** (**No Trust**).
- **Light Node (SPV)**: Stores only **block headers** and connects to full nodes as intermediaries. When needed, it requests specific transactions along with **Merkle proofs**—requiring orders of magnitude less storage while avoiding blind trust.
- **Client Node**: Relies on third-party hosted APIs (e.g., Infura) and **trusts** their responses without cryptographic verification.

---

## 1.8 Merkle Trees & Merkle Proofs (Slides 42–51)

How can a **Light Node** verify that a transaction `txI` is genuinely included in Block 21 without downloading all transactions in the block? Using a **Merkle Proof**!

![[DDS_2_42_full_vs_light.png]]

A **Merkle Tree** is a binary hash tree where:
- **Leaves** are hashes of individual transactions: $H(\text{tx}_i)$.
- **Internal nodes** are hashes of the concatenation of their two children: $H(H_L \,\|\, H_R)$ (if the number of nodes at a level is odd, the last one is duplicated).
- The **Merkle Root** at the top is stored in the **block header**, which is immutable and known to all light nodes.

![[DDS_2_43_merkle_tree.png]]

To prove that a transaction (e.g., `9Dog`) belongs to a block and has not been modified, the full node only needs to send the **verification path** (the sibling hashes along the path from the leaf to the root). The light node hashes `9Dog` upward with those siblings and checks whether the result matches the trusted **Merkle Root** in the block header.

![[DDS_2_45_merkle_proof.png]]

### Efficiency & Bandwidth Savings (Slides 46–50)
- A naive **hash list proof** requires downloading all $n$ transaction hashes: $O(n)$ bandwidth.
- A **Merkle tree proof** requires only $O(\log_2 n)$ sibling hashes!
- For example, in Bitcoin block `562943` (containing 2,351 transactions):
  - Without a Merkle tree: $2{,}351 \times 32 = 75{,}232\text{ bytes}$.
  - With a Merkle tree: $12 \times 32 = 384\text{ bytes}$!

![[DDS_2_49_big_o.png]]

In summary, Merkle trees enable efficient verification of transaction inclusion, efficient block integrity checks, and lightweight mobile/SPV clients. (Slides 46–48 also show Python code computing the Merkle root and verifying a proof via `double_sha256` and `reverse_bytes`.)

---

## 1.9 Traditional Payments, CBDCs & Cryptocurrencies (Slides 52–59)

Traditional payment networks rely on centralized intermediaries (issuing banks, acquiring banks, card networks like Visa/Mastercard) that clear and settle transactions, whereas blockchain networks replace intermediaries with a shared P2P ledger and consensus.

![[DDS_2_52_payments_network.png]]

### Central Bank Digital Currencies (CBDCs) vs. Cryptocurrencies (Slide 53)
| Dimension | CBDC | Cryptocurrency |
| :--- | :--- | :--- |
| **Control** | Centralized, managed by a central bank | Decentralized, no single authority |
| **Legal Tender** | Official state-backed legal tender | Generally not legal tender |
| **Stability** | Stable (pegged 1:1 to fiat currency) | High price volatility |
| **Privacy** | Selective privacy; KYC/identity required | Pseudonymous; public ledger |
| **Technology** | Centralized DBs or permissioned ledgers | Public, permissionless blockchains |

### Currency vs. Cryptocurrency: Creation, Correctness & Privacy (Slides 54–59)

![[DDS_2_55_currency_vs_crypto.png]]

- **Creation & Authenticity**: Fiat currency relies on central-bank issuance, physical watermarking, and bank security systems. Cryptocurrencies mint money according to open protocol rules and prove authenticity via **digital signatures**.
- **Correctness & Auditing**: Traditional systems require trusting banks and payment gateways. Cryptocurrencies have open-source protocols, formal proofs, and public ledgers where **anyone can audit every transaction** (with accounts as public keys $PK_a$ and payments validated by consensus).
- **Privacy**: In traditional digital payments, the source bank, destination bank, and payment gateway (e.g., Visa) see the payment. In public cryptocurrencies, **everyone** sees the payment on-chain (though identities remain pseudonymous).

---

# 2. Consensus

## 2.1 Distributed Systems Foundations & Failure Models (Slides 1–15)

In Part 1 we assumed an oracle ("Zeus") magically solved consensus. In reality, a blockchain is a **distributed system** of spatially separated nodes communicating over a network, with **no global clock** (Lamport, 1978) and unavoidable **network delays** and **information asymmetry**.

To reason about consensus, we model the network as a **graph** where nodes process messages exchanged over communication links, and we must answer key questions: *Are nodes known in advance? Are they always active? Are messages guaranteed to arrive, and within what time? Can we verify message integrity, origin, and order? Can nodes behave maliciously?*

### Failure & Communication Models (Slides 12–14)
- **Links** are assumed **reliable** (e.g., TCP)—otherwise consensus is impossible.
- **Nodes** can fail in two ways:
  1. **Crash-fail**: a node stops working without warning and never recovers (sufficient for controlled environments like data centers or some permissioned ledgers).
  2. **Byzantine**: a node behaves **arbitrarily**—it may lie, alter messages, drop messages, or collude with other traitors (essential for open, adversarial, permissionless networks).
- **Message Passing**:
  - **Synchronous**: there is a fixed, known upper bound $\Delta$ on message delivery time; protocols can be organized into lock-step rounds.
  - **Asynchronous**: there is **no upper bound** on message delay; messages can be delayed arbitrarily or arrive out of order.

> [!definition] Consensus Properties (Slide 15)
> A consensus protocol allows distributed nodes to agree on a common value or state, satisfying:
> 1. **Agreement**: all honest (non-faulty) nodes eventually decide on the **same** value.
> 2. **Validity**: the decided value must satisfy protocol rules (e.g., it must have been proposed by some node).
> 3. **Termination**: every honest node eventually reaches a decision.

---

## 2.2 Synchronous Crash-Tolerant Consensus & FLP Impossibility (Slides 16–23)

### Synchronous Consensus with Crash Failures (Slides 16–18)
- **No failures, synchronous**: Every node broadcasts its local view to all others. In **1 round**, all nodes have the exact same global view and apply a deterministic function (e.g., `min`) to decide.
- **At most $f$ crash failures ($f < n$), synchronous**: Each node broadcasts its local view for **$f+1$ rounds**.
  - Why $f+1$ rounds? A crashing process might send its value to some nodes and crash before sending to others. However, in the worst case, at most one process crashes per round. By the pigeonhole principle, across $f+1$ rounds there is at least one **clean round** with no crashes, ensuring every surviving node has the exact same picture.

![[DDS_3_18_crash_rounds.png]]

### FLP Impossibility in Asynchronous Systems (Slides 19–22)
> [!important] FLP Impossibility Result (Fischer, Lynch, Paterson, 1985)
> In an **asynchronous** system, **even a single crash-faulty process** makes it **impossible** for any deterministic protocol to guarantee consensus (specifically, **Termination** cannot be guaranteed).

Why? A receiver cannot distinguish between **Case 1** (a slow sender whose message is experiencing unbounded delay) and **Case 2** (a crashed sender that will never send).

![[DDS_3_20_flp.png]]

**How real systems circumvent FLP (Slides 21–22):**
1. **Timeouts / Failure Detectors**: if no progress is made, wait for a timeout and restart the round (used by **Paxos** and **Raft**, requiring a simple majority $n > 2f$ for crash failures).
2. **Non-determinism / Randomization**: e.g., **Ben-Or's protocol** ($t < n/2$ crash failures, exponential expected rounds in the worst case).
3. **Partial Synchrony**: an upper bound $\Delta$ exists, either unknown or holding only after some Global Stabilization Time $T$.
4. **Strong Primitives**: such as shared memory.

---

## 2.3 Byzantine Fault Tolerance (BFT) & Its Limits (Slides 24–40)

When nodes can be **Byzantine** (malicious), crash-tolerant protocols like Paxos and Raft are no longer safe. Lamport, Shostak, and Pease formalized this as the **Byzantine Generals Problem (BGP)**: a Commander sends an order (*attack* or *retreat*) to Lieutenants using **oral messages** (point-to-point, unforgeable-free, content controlled by the sender).

> [!lemma] The $3m + 1$ Bound (Slides 26–28)
> With oral messages, there is **no solution** to the Byzantine Generals Problem with fewer than **$3m + 1$** generals to tolerate **$m$** traitors. Equivalently, **more than two-thirds ($> 2/3$)** of the generals must be loyal.

**Proof Intuition (Simulation, Slide 27):** If a protocol existed for $3m$ generals with $m$ traitors, we could group the $3m$ generals into 3 groups of $m$ and use it to solve the impossible base case of **3 generals with 1 traitor**—a contradiction.

![[DDS_3_27_bgp_3m1.png]]

### The $OM(m)$ Inductive Algorithm (Slides 29–34)
- **Base case $OM(0)$** (0 traitors): Commander sends the order; Lieutenants execute it.
- **Inductive step $OM(m), m > 0$**:
  1. Commander sends its order to all $n-1$ Lieutenants.
  2. Each Lieutenant records the received order and acts as a commander in **$OM(m-1)$** to relay what it heard to the other $n-2$ Lieutenants.
  3. Each Lieutenant takes the **majority vote** over the collected values.

![[DDS_3_30_om_loyal.png]]
![[DDS_3_31_om_traitor.png]]

Note why we cannot simply use a deterministic function like `min` instead of majority: a malicious node could always inject the minimum value and win (Slide 33).

**Drawback of $OM(m)$:** It requires $O(n^m)$ messages (`n`, `n^2`, `n^3`, ...) and synchronous rounds, making it completely **unpractical** for large networks.

### Signed Messages & PBFT (Slides 35–38)
If we add **digital signatures** (a loyal general's signature cannot be forged and alterations are detected), **a 3-general solution does exist** (Slide 36): Lieutenants discard conflicting signed chains from a traitorous commander and apply a deterministic choice function on the valid set.

![[DDS_3_36_signed_messages.png]]

**PBFT (Practical Byzantine Fault Tolerance)** brings BFT to partially synchronous/asynchronous networks using signed messages and retransmissions, tolerating $f < n/3$ Byzantine nodes. However, it still incurs **$O(N^2)$** message complexity per round.

![[DDS_3_37_pbft.png]]

### Why Classical BFT Fails in Permissionless Blockchains (Slides 39–40)
Classical BFT protocols do not work in open, permissionless blockchains because they assume:
1. **Known, fixed membership**: every node knows every other node; nodes cannot freely join and leave.
2. **All-to-all communication**: $O(N^2)$ overhead does not scale to thousands of nodes.
3. **One-machine, one-vote**: vulnerable to **Sybil attacks**—an attacker can spin up unlimited virtual identities.

---

## 2.4 From BFT to Blockchain Consensus (Slides 41–63)

In a blockchain, consensus means agreeing on the **order of blocks** (and thus transactions) so every honest node holds the same ledger.
- **Deterministic Finality** (classical BFT): once decided, a block is immediately irreversible.
- **Probabilistic Finality** (Nakamoto consensus): nodes agree on the *probability* that a block is permanent; each new block buried on top lowers the chance of reversal.

### The Naive Gossip + Longest-Chain Protocol (Slides 43–63)
1. Why not elect a random leader? Without a scarce resource, an attacker can launch a **Sybil attack** by creating many fake nodes to increase its chance of winning.
2. Suppose nodes **gossip** valid candidate blocks (invalid transactions are immediately discarded).
3. If two nodes propose blocks simultaneously, a **fork** occurs!

![[DDS_3_52_fork.png]]

4. **Fork Resolution**: nodes adopt the **longest chain** (most blocks) and abandon shorter branches.

![[DDS_3_55_fork_resolution.png]]

5. **The Network Delay Problem (Slides 58–63)**: If nodes produce blocks faster than the network can propagate them, nodes remain unaware of the true longest chain and constantly create new forks.
   - **Solution**: slow down block production by making each node wait a **random timer** before proposing a block, giving the network time to propagate updates.

---

## 2.5 Proof-of-Work (PoW) & Nakamoto Consensus (Slides 64–102)

A random software timer has a fatal flaw: **a malicious node can cheat on the timer** and propose blocks immediately! We need a **cheat-resistant timer**—a mathematical puzzle that is:
- **Hard/slow to solve** (requires real time and scarce computational power, deterring Sybil attacks),
- **Easy and fast to verify** by all peers.

### The Hash Puzzle & Leader Election (Slides 65–72)
In **Proof-of-Work (PoW)**, miners repeatedly hash the block data concatenated with a **nonce** until the output is below a target threshold $T$ (i.e., starts with $j$ leading zeros):
$$H(\text{data} \,\|\, \text{nonce}) < T \quad \Longleftrightarrow \quad \underbrace{00\dots0}_{j\text{ bits}} \,\|\, 2^{k-j}$$
Because cryptographic hashes have no backdoor and cannot be inverted faster than brute force, the probability of solving the puzzle in one try is proportional to $2^{-j}$, and a miner's chance of winning is proportional to their **hashrate**. Whoever solves the puzzle first becomes the **leader** for that block.

![[DDS_3_67_pow_puzzle.png]]

**Difficulty Adjustment (Slides 69–72):** Bitcoin targets an average block interval of **10 minutes**. Every **2016 blocks (~2 weeks)**, the network adjusts the difficulty (number of leading zeros / target $T$): if blocks were mined faster than 10 minutes on average, difficulty increases; if slower, it decreases.

### Partitions, Longest Chain & the 51% Attack (Slides 73–90)
- **Network Partitions (Slides 73–75)**: If the network splits, the **larger partition** (with more computational power) grows its chain faster with high probability. When the partition heals, the shorter chain is discarded.

![[DDS_3_74_partitions.png]]

- **Temporary Forks (Slide 84)**: Even without attacks, simultaneous block discoveries cause temporary forks, which are resolved naturally by the longest-chain rule.

![[DDS_3_84_longest_chain.png]]

- **51% / Double-Spend Attack (Slides 85–90)**: If an adversary controls $> 50\%$ of the network hashrate, they can privately mine an alternative chain excluding a payment (e.g., 100 BTC), wait for the victim to accept the transaction, and then publish their longer private chain to **reverse history** and double-spend.

![[DDS_3_85_51_attack.png]]

### Incentives, Sustainability, Decentralization & Scalability (Slides 91–102)
- **Miner Incentives (Slide 91)**: Miners earn (1) all **transaction fees** in the block and (2) the **block reward** of newly minted BTC. The block reward **halves every 210,000 blocks (~4 years)**, capping total supply at just under **21 million BTC**.

![[DDS_3_91_block_reward.png]]

- **Wasted Work & Sustainability (Slides 92–94)**: Every 10 minutes, the entire network performs massive computation, yet only *one* miner's work produces the winning block—raising major energy-consumption concerns (tracked by indices like Cambridge CBECI).
- **Centralization Risks (Slides 95–98)**: Mining has concentrated into large **mining pools** and specific geographic regions; moreover, infrastructure decentralization does not automatically imply decentralization of software development or governance.
- **Scalability & The Blockchain Trilemma (Slides 99–104)**: Bitcoin's throughput (~7 TPS) is far below traditional payment networks (Visa). This illustrates the **Blockchain Trilemma**: a system struggles to simultaneously maximize **Decentralization**, **Security**, and **Scalability**.

![[DDS_3_100_scalability.png]]
![[DDS_3_104_trilemma.png]]

---

## 2.6 Proof-of-Stake (PoS) & Formal Security (Slides 105–131)

**Proof-of-Stake (PoS)** replaces scarce computational power with scarce **economic stake** (locked native tokens):
- Accumulating a majority of stake is economically prohibitive.
- Stakeholders have a rational financial incentive for the system to remain secure and valuable.

### Ethereum PoS & the Beacon Chain (Slides 108–118)
Ethereum transitioned from PoW to PoS (the **Beacon Chain**):
- **Time structure**: Divided into **12-second slots** (one block proposed per slot, though slots can be empty; 12s gives enough time for gossiping) and **epochs** of 32 slots (**6.4 minutes**).

![[DDS_3_110_beacon_chain.png]]

- **Validators & Attestations (Slides 111–113)**: Stakers activate one validator per **32 ETH** staked. Pseudo-random **committees** of validators vote (**attest**) on blocks; forks are resolved by picking the branch with the most **stake-weighted votes**.
- **Finality (Slide 114)**: A block is **finalized** when a **supermajority (66% of total staked ETH)** creates a supermajority link between two consecutive epoch **checkpoints**.
- **Randomness via RANDAO (Slide 115)**: Validators collaboratively mix committed random values ($\text{mix}(\text{RND}_1, \dots, \text{RND}_n)$) so no single party controls committee selection.

![[DDS_3_115_randao.png]]

- **Centralization Risk (Slides 116–117)**: The high 32 ETH threshold pushes smaller holders into **Liquid Staking** protocols (e.g., **Lido**), creating stake concentration risks.

### From PoW to PoS: A Principled Construction (Slides 119–131)
How do we mathematically transform PoW's lottery into a secure PoS lottery?
1. **PoW essence**: $H(\text{prev hash}, \text{merkle root}, \text{nonce}) < T$.
2. **Eliminate the nonce (Slide 121)**: Weight the target by the node's stake:
   $$H(\text{prev hash}, \text{merkle root}, pk_n) < T \cdot \text{stake}_n$$
   *Flaw*: **Grinding attack**—a validator can permute transactions in the block to test millions of `merkle root` values, turning PoS back into a computation race!
3. **Eliminate the Merkle root & add timestamp $ts$ (Slides 122–125)**:
   $$H(\text{prev hash}, ts, pk_n) < T \cdot \text{stake}_n$$
   Adding a discrete timestamp $ts$ (e.g., 1 attempt per second) prevents the protocol from stalling if nobody wins at a given tick. Because the Merkle root is removed from the lottery, transactions are added and signed *after* winning, and linked back to the header via **hash pointers** to prevent tampering.

![[DDS_3_125_pos_tamper.png]]

4. **Preventing Past-Key Corruption — KES (Slide 126)**: An adversary who corrupts old honest validators could rewrite history. **Key-Evolving Signatures (KES)** rotate signing keys at regular intervals and **delete old keys**, making it cryptographically impossible to re-sign past blocks.
5. **Preventing Predictable Winners — VRFs (Slides 127–129)**: With $pk_n$ in the hash, anyone can predict future lottery winners and bribe/corrupt them in advance. Replacing $H(\dots, pk_n)$ with a **Verifiable Random Function (VRF)** evaluated with the **secret key** $sk_n$:
   $$\text{VRF}(\text{prev hash}, ts, sk_n) < T \cdot \text{stake}_n$$
   keeps the winner secret until they publish their block and VRF proof (verifiable with $pk_n$).
6. **The Nothing-at-Stake (NaS) Attack (Slides 130–131)**: Unlike PoW where mining on multiple forks splits hashrate, in PoS an adversary can cheaply sign blocks on *every* branch of the blocktree simultaneously. While the number of adversarial blocks grows exponentially, the depth of the adversarial tree grows linearly at rate $e\beta\lambda$, whereas the honest chain grows at $(1-\beta)\lambda$. Thus, the NaS (and worst-case private) attack succeeds if:
   $$e\beta\lambda > (1-\beta)\lambda \quad \Longrightarrow \quad \beta \ge \frac{1}{1+e} \approx 26.89\%$$
   Hence, longest-chain PoS tolerates $< 26.89\%$ adversarial stake (roughly half of PoW's 50% threshold).

![[DDS_3_130_nas_attack.png]]

---

## 2.7 Algorand: Committee-Based BFT in Permissionless Networks (Slides 132–153)

To achieve **deterministic finality** in a permissionless setting, **Algorand** selects a small random **committee** to run a BFT protocol for each step:
- **Random vs. Fixed Committee Size (Slides 134–135)**: Electing an *exact* fixed size $N$ in a decentralized way is hard, but **VRFs** easily elect a *random-size* committee (e.g., $\text{Binomial}(N, 0.7)$ honest members when 70% of stake is honest). Choosing a sufficiently large expected committee size keeps the failure probability negligible.

![[DDS_3_134_binomial_committee.png]]
![[DDS_3_135_vrf_committee.png]]

- **Defeating Adaptive Adversaries (Slides 136–137)**: Instead of electing one committee for the whole round (which an adversary could corrupt), Algorand uses **VRFs** to privately elect a **fresh, unpredictable committee for every single phase/step** of the BFT protocol (players only reveal they were selected when they broadcast their vote!).

![[DDS_3_137_algorand_vrf.png]]

### Algorand Protocol & Binary Byzantine Agreement (Slides 138–147)
In each round:
1. **Step 1 (Proposal)**: Committee 1 members propose candidate blocks; the proposal with the smallest VRF credential hash becomes the leader.
2. **Step 2 (Certification & Binary BA)**: Committee 2 votes on the leader's block (`0` = ok, `1` = not ok / empty block) using an efficient **Binary Byzantine Agreement** ($n = 3t+1$, supermajority $2t+1$):
   - Every player $i$ broadcasts bit $b_i$ and updates by the first rule that fires:
     - **`STRONG_0`**: if $\#_i(0) \ge 2t+1 \implies b_i = 0$
     - **`STRONG_1`**: if $\#_i(1) \ge 2t+1 \implies b_i = 1$
     - **`FALLBACK`**: otherwise $\implies b_i = c$ (a shared **common coin**).
   - **Why $2t+1$ works**: At most $t$ votes are Byzantine, so seeing $2t+1$ votes guarantees $\ge t+1$ honest voters—a true honest majority. `STRONG_0` and `STRONG_1` are **mutually exclusive**.
   - **Property A (Agreement preserved)**: if all honest nodes start with $b$, all see $\ge 2t+1$ of $b$ and keep $b$.
   - **Property B (Disagreement resolves)**: if honest nodes disagree, the common coin $c$ matches the winning `STRONG` branch with probability $1/2$ (or $1/3$ if the coin leader is Byzantine), so disagreement probability drops exponentially as $(1/2)^m$.
- **Implementing the Common Coin (Slides 144–147)**: Each player $j$ broadcasts a VRF credential $\sigma_j = \text{VRF\_sign}(sk_j, \text{round} \| \text{step})$. Every player finds the minimum-hash credential $\mathcal{L} = \arg\min_{j \in SV} H(\sigma_j)$ and sets $c = \text{lsb}(H(\sigma_{\mathcal{L}}, s))$. With probability $> 2/3$, $\mathcal{L}$ is honest and unbiased.

![[DDS_3_144_common_coin.png]]

### Algorand Architecture & Trade-offs (Slides 148–153)
To keep network delay minimal for instant finality, Algorand uses a **2-layer architecture**: high-performance **Relay nodes** (for fast routing) and cheaper **Participation nodes** (for stakeholders). Compared to Bitcoin, Algorand trades extreme simplicity and permissionless infrastructure for **speed, scalability, and instant finality** (at the cost of early token/relay concentration).

---

## 2.8 DAG-Based DLTs & The IOTA Tangle (Slides 154–183)

Beyond linear blockchains, several Distributed Ledger Technologies (DLTs) use **Directed Acyclic Graphs (DAGs)**—such as **Hedera** (Hashgraph), **Sui** (Mysticeti), **Kaspa** (GHOSTDAG), **IOTA** (The Tangle), and **Sonic** (Lachesis).

### IOTA & The Tangle (Slides 156–178)
In **IOTA**, there are no blocks and no miners—the ledger is a **DAG of transactions** called the **Tangle**, enabling higher scalability and **feeless microtransactions**:
- **Key Rule (Slide 159)**: Every newly issued transaction must **approve two previous unapproved transactions (tips)**. Approvals can be **direct** (one hop) or **indirect** (transitive path).

![[DDS_3_159_tangle_dag.png]]
![[DDS_3_160_direct_indirect.png]]

- **Tip Selection & Lazy Tips (Slides 161–164)**: Uniform random tip selection fails because **lazy tips** (transactions that approve very old transactions instead of recent ones) do not help confirm new transactions. Instead, IOTA uses a **random walk** starting from the Genesis and walking forward until reaching a tip. (If transaction rate $\lambda$ is very small, the Tangle resembles a chain; if $\lambda$ is very big, a shallow tree.)

![[DDS_3_164_unweighted_walk.png]]

- **Weighted Random Walk & Cumulative Weight (Slides 165–170)**:
  - A transaction's **cumulative weight** is $1 + (\text{number of direct and indirect approvers})$.
  - Biasing the random walk toward heavier transactions discourages lazy tips (e.g., a lazy tip with weight 1 is ignored in favor of an active branch with weight 7).
  - **Super-weighted walk risk**: if bias is infinite (always picking the heaviest child), many valid tips on the sidelines are **orphaned forever**.
  - **Parameter $\alpha$**: tunes the bias ($\alpha = 0$ is unweighted; $\alpha$ very high is super-weighted; an intermediate $\alpha$ balances security and liveness).

![[DDS_3_165_cumulative_weight.png]]
![[DDS_3_166_lazy_tip.png]]
![[DDS_3_169_super_weighted_risk.png]]

- **Confirmation Confidence & Double-Spending (Slides 171–175)**: Run tip selection 100 times; if $A$ of the 100 chosen tips approve transaction $T$, its confirmation confidence is **$A\%$**. In a double-spend conflict, the weighted walk eventually makes one branch heavier and abandons the other. However, an attacker with enough spamming power can still try to outpace the honest Tangle.

![[DDS_3_171_confirmation_confidence.png]]
![[DDS_3_175_tangle_attack.png]]

- **PoW as Anti-Spam & The Coordinator (Slides 176–178)**: IOTA uses lightweight PoW (`minWeightMagnitude`, MWM) solely for **anti-spam**, not consensus. Because a young network lacks sufficient honest traffic to prevent concentrated double-spends, the IOTA Foundation initially ran a centralized **Coordinator** issuing milestone transactions every 2 minutes (giving 100% confidence), later transitioning toward a **Validator Committee** and **IOTA 2.0** (permissionless stake-based On-Tangle voting).

### Taxonomy, Distributed Databases & Strategic Challenges (Slides 179–183)
- **Permissioned vs. Permissionless / Private vs. Public Matrix (Slide 179)**: Distinguishes who can *transact/view* from who participates in *consensus*.
- **Permissioned Blockchains vs. Distributed Databases (Slides 180–181)**:
  - Differ in **trust model** (single organization vs. mutually distrusting entities), **CRUD vs. CRAB** (Create, Read, Append, Burn—immutability), and failure assumptions (protected environments can use crash-tolerant **Raft/Paxos**; adversarial environments need Byzantine-tolerant **PBFT / Hyperledger Fabric**).
- **Strategic Challenges (Slide 183)**: Decentralized governance, transparency vs. privacy (GDPR, Zero-Knowledge proofs), shifting from personal trust to algorithmic consensus (Self-Sovereign Identity, Smart Contracts, Tokenization), and navigating the **Blockchain Trilemma**.

---

# 3. Safety and Liveness of Bitcoin

## 3.1 Dependability Attributes & Blockchain Mapping (Slides 1–6)

Classic **Dependability** is defined by five core attributes:
1. **Availability**: readiness of correct service (operational and accessible when required).
2. **Reliability**: continuity of correct service over time without failure.
3. **Safety**: absence of catastrophic consequences on users/environment (e.g., data corruption).
4. **Integrity**: absence of unauthorized system alterations.
5. **Maintainability**: ability to undergo updates and repairs (e.g., soft/hard forks, smart contract upgrades).

Evaluating dependability combines **Performance & Workload** (modeled from empirical logs as random variables/distributions) with failure models.

![[DDS_35_03_dependability_eval.png]]

In **blockchains**, these classical attributes map onto two fundamental distributed-systems properties:
- **Liveness** (*"something good eventually happens"*): valid transactions submitted by honest users are eventually included and committed. Maps to **Availability**.
- **Safety** (*"nothing bad ever happens"*): honest nodes never commit conflicting blocks at the same height (they never disagree). Together with **Integrity**, this forms **Security**.
- **Reliability** = **Security (Safety + Integrity) + Liveness** under the adversarial model.

### Probabilistic vs. Deterministic Finality (Slide 5)
- **Probabilistic Finality (Liveness-first — e.g., Bitcoin, Dogecoin)**: The chain never halts, but blocks are never 100% explicitly final. Instead, as more blocks are buried on top (0 confirmations in mempool $\to$ 1 confirmation $\to$ **6 confirmations** $\approx 99.99\%$ safe), reversing a transaction becomes exponentially harder. *Trade-off*: continuous liveness, but you must wait for certainty.
- **Deterministic / Absolute Finality (Safety-first — e.g., BFT, Cosmos, Solana)**: A block requires a supermajority (e.g., $\ge 66\%$ of validators) to sign before being added, making it **instantly final**. *Trade-off*: if $> 33\%$ of validators go offline, the chain **halts** (sacrificing liveness for safety).

---

## 3.2 Formal Proof Intuition for Nakamoto Consensus (Slides 7–17)

Following the analysis of Nakamoto consensus (from *Decentralized Thoughts*), we make three protocol assumptions:
1. **Longest chain wins**: each honest node adopts the longest PoW chain it knows (breaking ties arbitrarily) and mines on its tip.
2. **Disseminate blocks**: upon adopting a new longest chain, a node immediately broadcasts the new blocks.
3. **$k$-confirmation commit**: a node commits a block once it is buried at least $k$ blocks deep in its longest chain.

### Mining as a Poisson Process (Slide 9)
Honest PoW mining is modeled as a **memoryless Poisson process** with collective arrival rate $\alpha$ (over time $t$, expected honest blocks mined is $\alpha t$). Consequently, the inter-arrival time $T$ between consecutive honest blocks follows an **exponential distribution**:
$$P[T > \Delta] = e^{-\alpha\Delta} \triangleq g$$
where $\Delta$ is the upper bound on network propagation delay (e.g., Bitcoin averages 1 block every 10 minutes = 600 s; Ethereum PoW averaged 1 block every 13 s).

![[DDS_35_09_poisson.png]]

### Tailgaters vs. Non-Tailgaters & Liveness (Slides 10–11)
- A **Tailgater** ($T$) is an honest block mined within time $< \Delta$ of the previous honest block $N$, before $N$ has finished propagating. Both $T$ and $N$ link to the same parent $L$, creating a **fork** at the same height.
- A **Non-tailgater** is an honest block mined after a gap $T > \Delta$ since the previous block.

![[DDS_35_10_tailgaters.png]]

> [!lemma] Lemma 5(i) — Non-Tailgaters & Liveness
> Non-tailgaters **do not share the same height** with any other honest block—they strictly extend the longest chain.
> - Probability a block is a non-tailgater: $g = e^{-\alpha\Delta}$ (tailgater: $1-g$).
> - By linearity of expectation, non-tailgaters are created at expected rate **$g\alpha$**, giving a lower bound on longest-chain growth and hence **Liveness** ($g\alpha > 0$).

### Loners & Safety (Slides 12–15)
> [!lemma] Lemma 5(ii) — Loners & Safety
> A **loner** is an honest block that is the **only** honest block at its height (meaning it had a gap $> \Delta$ *both before and after* it).
> - In expectation, the number of loners grows at rate **$g^2 \alpha$**.

![[DDS_35_12_loners.png]]

Why do loners matter for **Safety**? Even if the adversary tries to create a competing private chain, each loner advances the honest chain height by 1 without any honest competition. As long as the adversary cannot mine blocks faster than honest nodes mine loners, the honest chain outpaces any adversarial fork:
$$\text{Safety: } g^2 \alpha > \beta \qquad \text{Liveness: } g\alpha > 0$$
Since $0 < g < 1$, we have $g\alpha > g^2\alpha > \beta$, so both Safety and Liveness hold whenever:
$$g^2\alpha > \beta \quad \Longleftrightarrow \quad e^{-2\alpha\Delta}\alpha > \beta$$

![[DDS_35_13_safety_loners.png]]

### Interpreting the Bound (Slides 15–17)
This explains why **Bitcoin uses a large block interval (10 minutes)** relative to network delay $\Delta$:
- When the block interval $1/\alpha \gg \Delta$, we have $\alpha\Delta \approx 0 \implies g = e^{-\alpha\Delta} \approx 1 \implies g^2 \approx 1$, recovering the ideal honest-majority threshold **$\alpha > \beta$** (50% fault tolerance).
- If the block interval shrinks to be comparable to $\Delta$, $\alpha\Delta$ grows, $g^2$ drops sharply, and Nakamoto consensus's fault tolerance degrades rapidly.

![[DDS_35_15_nakamoto_bound.png]]


---

# 4. Smart Contracts: Beyond Transactions

## 4.1 From Bitcoin to Ethereum: A State Machine (Slides 1–9)

While Bitcoin introduced decentralized value transfer using a UTXO ledger, **Ethereum** generalized the blockchain into a programmable **transaction-based state machine**:
- The global ledger at time $t$ is represented by a **World State** $\sigma_t$.
- Executing a valid transaction $T_i$ transitions the system deterministically to a new state $\sigma_{t+1}$.
- Grouping transactions into blocks produces a **chain of states** ($\sigma_t \to \sigma_{t+1} \to \sigma_{t+2} \dots$).

![[DDS_4_02_history.png]]
![[DDS_4_06_state_machine.png]]
![[DDS_4_07_state_chain.png]]

The **World State** $\sigma$ is simply a mapping from **160-bit addresses** to **Account States**.

---

## 4.2 Accounts, Nonces & Address Derivation (Slides 10–16)

Ethereum defines two types of accounts:
1. **Externally Owned Account (EOA)**: controlled by a human/external actor via a **private key**; cannot contain EVM code.
2. **Contract Account (CA)**: an autonomous object controlled by its **EVM code**; stores both immutable bytecode and persistent storage.

![[DDS_4_10_accounts.png]]

Every **Account State** contains four fields:
- `nonce`: number of transactions sent from an EOA (or number of contracts created by a CA).
- `balance`: amount of **wei** ($1\text{ ETH} = 10^{18}\text{ wei}$) owned by the address.
- `storageHash` (storage root): root hash of the account's storage Merkle Patricia Trie (empty for EOAs).
- `codeHash`: hash of the account's EVM bytecode (empty hash for EOAs).

![[DDS_4_12_account_state.png]]

### The Account Nonce (Slide 13)
Unlike the old PoW mining nonce, the **account nonce** is a strictly sequential counter of outgoing transactions from an EOA, serving two critical purposes:
1. **Ordering**: guarantees miners/validators execute a sender's transactions in exact order despite network delays.
2. **Replay protection**: makes every transaction unique—an attacker cannot copy a signed transaction from the network and replay it to double-spend, because the network rejects any transaction reusing an already-executed nonce.

### 160-Bit Addresses (Slides 15–16)
- An **EOA address** is derived from the rightmost 160 bits of the Keccak-256 hash of its **public key**.
- A **Contract Account address** is deterministically derived from the **sender's address** and the **sender's nonce** at creation time.

![[DDS_4_15_address.png]]

---

## 4.3 Transactions: Contract Creation & Message Calls (Slides 17–26)

A **transaction** is a cryptographically signed instruction submitted by an **external actor** (EOA) that triggers a state transition. There are two practical types:
1. **Contract Creation** (`to = 0`): deploys a new Contract Account using `init` bytecode, which runs once to initialize storage and returns the runtime EVM code stored at the new address $N$.
2. **Message Call**: sends ETH (`value`) and/or `input data` to an existing EOA or Contract Account, triggering its EVM code to execute and update storage/balances.

![[DDS_4_19_tx_types.png]]
![[DDS_4_20_contract_creation.png]]
![[DDS_4_23_message_call.png]]

### Fields of an Ethereum Transaction (Slide 25)
- `nonce`: sender's transaction counter.
- `gasPrice`: price per unit of gas (in wei/gwei) the sender is willing to pay.
- `gasLimit`: maximum gas units the transaction may consume.
- `to`: 160-bit recipient address (or `0` for contract creation).
- `value`: amount of wei to transfer.
- `v, r, s`: ECDSA signature components proving the sender's identity.
- `init` or `data`: initialization bytecode (creation) or function call payload (message call).

![[DDS_4_25_tx_fields.png]]

---

## 4.4 The World State & Modified Merkle Patricia Trie (Slides 27–35)

How does Ethereum store the World State (`Address -> Account State`) and commit its root hash into each block header? A standard binary **Merkle Tree** is ill-suited because account balances and storage change constantly. Ethereum needs a **State Tree** that:
1. Is **easy to edit** (frequent updates).
2. Produces the **exact same root hash** for the same key-value set, **regardless of insertion order**.
3. Has **limited depth** (using hexadecimal-encoded keys $\to$ **16 branches** per node instead of 2).

![[DDS_4_28_state_tree.png]]

To achieve this, Ethereum uses a **Modified Merkle Patricia Trie (MPT)**:
- **Naive Patricia (Radix) Trie**: stores keys character-by-character along paths to leaf values (e.g., `ANT -> 1`, `APPLE -> 2`, `CAT -> 3`, `DOG -> 4`).
- **Optimized Patricia Trie**: compresses shared prefixes/single-child chains into **extension** and **branch** nodes to reduce depth and storage, supporting clean insertion and updates.
- **Merkle Patricia Trie**: adds cryptographic hashes to every node pointer, combining the fast key-value lookup/update of a Patricia Trie with the tamper-proof root hash of a Merkle Tree.

![[DDS_4_30_patricia_naive.png]]
![[DDS_4_31_patricia_opt.png]]
![[DDS_4_34_mpt.png]]
![[DDS_4_35_mt_vs_mpt.png]]

---

## 4.5 Messages & The Ethereum Virtual Machine (EVM) (Slides 36–53)

### Messages (Slides 36–38)
A **Message** (`Data` bytes + `Value` in ETH) is passed between two accounts. Unlike a *transaction* (which only an external actor can sign and submit), a message can be triggered either by a transaction or internally **by EVM code** (a smart contract calling another contract), yielding four cases: `EOA -> EOA`, `EOA -> CA` (by transaction), and `CA -> EOA`, `CA -> CA` (by EVM code).

![[DDS_4_38_four_messages.png]]

### EVM Architecture & Machine Space (Slides 39–52)
The **Ethereum Virtual Machine (EVM)** is the sandboxed, deterministic, **stack-based** runtime environment that executes smart contract bytecode across every Ethereum node.

![[DDS_4_41_evm_arch.png]]

Its machine space is divided into:
- **Virtual ROM (immutable)**: holds the contract's **EVM bytecode** (e.g., `PUSH1 e0`, `PUSH1 02`, `EXP`, `CALLDATALOAD`, compiled to `0x60e060020a600035...`).
- **Machine State (volatile, cleared after execution)**:
  - **Program Counter (`PC`)** and **Gas available**.
  - **Stack**: 256-bit words, max depth **1024 elements**; all operations act on the top 16 elements (`PUSH`, `POP`, `COPY`, `SWAP`, arithmetic).
  - **Memory**: linear, byte-addressed volatile memory initialized to zero (`MSTORE`, `MSTORE8`, `MLOAD`).
- **World State (persistent, stored on-chain)**:
  - **Account Storage**: persistent 256-bit-to-256-bit key-value store initialized to zero (`SSTORE`, `SLOAD`), the most expensive resource in gas.

![[DDS_4_42_evm_space.png]]
![[DDS_4_52_evm_exec.png]]

### Message Call Depth (Slide 53)
A contract executing on the EVM can send a message call to another contract, spawning a nested EVM frame. To prevent infinite recursion or stack exhaustion, the **message call depth is strictly limited to $< 1024$ levels**.

![[DDS_4_53_call_depth.png]]

---

## 4.6 Gas, Fees & Execution Costs (Slides 54–58)

Because Ethereum is Turing-complete, we must prevent infinite loops and resource abuse (halting problem / DoS). Therefore, every EVM instruction consumes **Gas**—a unit measuring computational effort (analogous to *liters of fuel* for a trip from Rome to Milan), paid in ETH (analogous to *euros per liter*, quoted in **gwei** where $1\text{ gwei} = 10^{-9}\text{ ETH}$).

- When an EOA submits a transaction, it supplies a `gasLimit` (`Gas supply`).
- As the EVM executes instructions and nested message calls, gas is deducted step by step.
- If execution succeeds, any **unused gas is refunded** to the sender; if it runs out of gas, all state changes revert while the spent gas fee is still paid to the block proposer/miner.

![[DDS_4_55_gas_flow.png]]

> [!example] Gas Fee Calculation (Pre-London Upgrade, Slide 58)
> Alice sends 1 ETH to Bob with `gasLimit = 21,000` units and `gasPrice = 200 gwei`:
> $$\text{Total Fee} = 21{,}000 \times 200\text{ gwei} = 4{,}200{,}000\text{ gwei} = 0.0042\text{ ETH}$$
> Alice's account is debited `1.0042 ETH`, Bob receives `1.0000 ETH`, and the miner receives `0.0042 ETH`.

---

## 4.7 Compilation, Bytecode, ABI & Vulnerabilities (Slides 59–69)

### Code Generation & EVM Layer (Slides 59–61)
Developers write smart contracts in high-level languages (**Solidity**, **Vyper**, LLL) using IDEs like **Remix**, and compile them into **EVM bytecode**. Just as the JVM abstracts OS differences in Java, the EVM sits between the Ethereum node software (e.g., Geth) and the contract code so every node executes contracts identically.

![[DDS_4_59_code_gen.png]]
![[DDS_4_60_evm_layer.png]]

### Bytecode vs. Contract ABI (Slides 62–67)
- **EVM Bytecode**: low-level machine-readable hex instructions deployed on-chain.
- **Contract ABI (Application Binary Interface)**: a JSON specification defining the standard scheme to encode function calls (e.g., 4-byte function selector + ABI-encoded arguments, testable on `abi.hashex.org`) and decode return values. It enables **dApp-to-contract** and **contract-to-contract** interaction (e.g., calling `retrieve()` or `store(uint256 num)`).
- **Testnets (Slide 64)**: public test networks allow developers to deploy and debug contracts using free test ETH before risking real funds on mainnet.

### Smart Contract Vulnerabilities: Reentrancy (Slides 68–69)
Because smart contracts are immutable once deployed and handle real financial value, bugs can be catastrophic. A classic example is **Reentrancy**: a malicious contract calls a victim contract's `withdraw()` function, and inside its fallback/receive function recursively re-enters `withdraw()` *before* the victim updates the caller's balance, draining the contract's funds.

![[DDS_4_68_reentrancy.png]]

*(Slide 69 provides further developer references: Ethereum Execution APIs, `ethereumnodes.com`, and `etherscan.io`.)*
