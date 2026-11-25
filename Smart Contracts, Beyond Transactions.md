Smart Contracts: Beyond Transactions

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
