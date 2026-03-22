# Blockchain Track

> Understanding blockchain at the level needed to build FabricBloc.

---

## What You Need to Know

You don't need to be a crypto expert. You need to understand the data structures and APIs well enough to index, query, and serve blockchain data.

---

## Blockchain Fundamentals

### The Chain
A blockchain is an append-only linked list of blocks. Each block contains transactions.

```
Block 0 ← Block 1 ← Block 2 ← Block 3 (latest)
  │          │          │          │
  └─ txs     └─ txs     └─ txs     └─ txs
```

Each block references the hash of the previous block. Changing any block invalidates all subsequent blocks.

### Key Concepts
| Concept | Meaning |
|---------|---------|
| **Block** | Container of transactions, linked to previous block |
| **Transaction** | State change (transfer tokens, call contract, deploy code) |
| **Address** | Public identifier (like an account number) |
| **Hash** | Cryptographic fingerprint (identifies blocks and transactions) |
| **Gas** | Computational cost of a transaction (EVM chains) |
| **Consensus** | How the network agrees on the next block (PoW, PoS) |

---

## EVM Chains (Ethereum, Polygon, Arbitrum, Base)

The Ethereum Virtual Machine is the most common smart contract platform.

### Key APIs
```bash
# Get latest block number
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' \
  https://eth-mainnet.rpc.example.com

# Get block by number
{"method":"eth_getBlockByNumber","params":["0x10d4f",true]}

# Get transaction receipt
{"method":"eth_getTransactionReceipt","params":["0xabc..."]}
```

### Data Model
```go
type Block struct {
    Number       uint64
    Hash         string
    ParentHash   string
    Timestamp    uint64
    Transactions []Transaction
    GasUsed      uint64
}

type Transaction struct {
    Hash     string
    From     string
    To       string
    Value    *big.Int
    GasPrice *big.Int
    Input    []byte   // Smart contract call data
}
```

---

## Solana

Different architecture from EVM. Accounts-based, not transactions-based.

### Key Differences from EVM
| Feature | EVM | Solana |
|---------|-----|--------|
| Speed | ~12s blocks | ~400ms slots |
| Throughput | ~15 TPS | ~4,000 TPS |
| State model | Account balance | Account data |
| Smart contracts | Solidity/Vyper | Rust (programs) |
| Fees | Gas (variable) | Fixed + priority |

---

## Smart Contracts

Programs deployed on the blockchain. Once deployed, the code is immutable.

### Solidity (EVM)
```solidity
// Simple token transfer
contract Token {
    mapping(address => uint256) public balances;
    
    function transfer(address to, uint256 amount) public {
        require(balances[msg.sender] >= amount, "Insufficient balance");
        balances[msg.sender] -= amount;
        balances[to] += amount;
    }
}
```

### ABI (Application Binary Interface)
The interface specification for a smart contract. Tells you what functions exist and their parameters.

---

## FabricBloc — What We're Building

FabricBloc enables creators to launch Web3 products without blockchain expertise — NFTs, tokens, DAOs, and smart accounts through simple APIs.

**What it does:**
1. Provides API-first access to blockchain operations (mint, deploy, govern)
2. Manages smart accounts and gasless transactions (ERC-4337)
3. Indexes blockchain data in real-time for queryable access
4. Handles multi-chain deployment (Ethereum, Polygon, Base)
5. Abstracts wallet management, gas, and contract interaction

**Why it matters:**
Building on blockchain is complex — key management, gas optimization, contract deployment, multi-chain support. FabricBloc handles the infrastructure so creators and developers focus on their product, not the plumbing.

---

## Resources

- [Ethereum.org — Developer Docs](https://ethereum.org/developers)
- [Solana Docs](https://docs.solana.com/)
- [CryptoZombies](https://cryptozombies.io/) — interactive Solidity tutorial
- [Etherscan](https://etherscan.io/) — explore Ethereum data
- [Solscan](https://solscan.io/) — explore Solana data

---

*Part of the [BlocLabs Developer Onboarding Program](../README.md)*
