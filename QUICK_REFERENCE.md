# Web3 Theory Reference Guide
*Quick lookup for every concept in your 36-week journey*

---

## Table of Contents
1. [Blockchain Fundamentals](#blockchain-fundamentals)
2. [Ethereum & EVM](#ethereum--evm)
3. [Smart Contracts (Solidity)](#smart-contracts-solidity)
4. [Tokens & NFTs](#tokens--nfts)
5. [DeFi Concepts](#defi-concepts)
6. [Solana Architecture](#solana-architecture)
7. [Rust Programming](#rust-programming)
8. [Data Structures](#data-structures)
9. [Algorithms](#algorithms)
10. [System Design](#system-design)

---

# Blockchain Fundamentals

## What is a Blockchain?
**Definition:** Distributed ledger of transactions, stored across many computers (nodes), secured by cryptography.

**Key Properties:**
- **Decentralized:** No single authority controls it
- **Immutable:** Once added, data can't be changed
- **Transparent:** Anyone can view all transactions
- **Trustless:** Don't need to trust other parties

**Structure:**
- **Block:** Container of transactions + metadata
- **Chain:** Blocks linked via cryptographic hashes
- **Hash:** Unique fingerprint of data (SHA-256, Keccak-256)

---

## Cryptography Basics

### Hashing
**Purpose:** Convert data of any size into fixed-size string (one-way function).

**Common Hash Functions:**
- **SHA-256:** Bitcoin uses this (256-bit output)
- **Keccak-256:** Ethereum uses this (256-bit output)
- **Ed25519:** Solana signatures

**Properties:**
- Deterministic (same input = same output)
- Fast to compute
- One-way (can't reverse)
- Collision-resistant

**Example:**
```
Input: "Hello World"
SHA-256: a591a6d40bf420404a011733cfb7b190d62c65bf0bcda32b57b277d9ad9f146e
```

---

### Public-Private Key Cryptography

**Concept:** Math-based pair of keys.

**Private Key:** 
- Secret number (keep safe!)
- Used to sign transactions
- Proves ownership
- 256 bits of randomness

**Public Key:**
- Derived from private key (one-way)
- Shared publicly
- Others use it to verify your signatures

**Address:**
- Derived from public key
- What you give people to send you money
- Ethereum: 0x + 40 hex chars (e.g., 0x742d35Cc...)
- Solana: Base58 encoded (e.g., 7Np41oeYqP...)

**Flow:**
```
Private Key → Public Key → Address
    ↓
  Sign Transaction
    ↓
Others verify with Public Key
```

---

### Digital Signatures

**Purpose:** Prove you own private key without revealing it.

**Process:**
1. Hash the message
2. Sign hash with private key → Signature
3. Others verify: signature + message + public key → Valid/Invalid

**In Ethereum:**
- ECDSA (Elliptic Curve Digital Signature Algorithm)
- secp256k1 curve

**In Solana:**
- Ed25519 (EdDSA)
- Faster, smaller signatures

---

## Wallets

### Types of Wallets

**Hot Wallet:**
- Connected to internet
- Convenient but less secure
- Examples: MetaMask, Phantom

**Cold Wallet:**
- Offline storage
- Very secure
- Examples: Ledger, Trezor (hardware wallets)

**Custodial vs Non-Custodial:**
- **Custodial:** Someone else holds your keys (Coinbase, Binance)
- **Non-Custodial:** You control your keys (MetaMask)

---

### HD Wallets (Hierarchical Deterministic)

**Concept:** One seed phrase → unlimited addresses.

**Mnemonic Phrase (BIP-39):**
- 12 or 24 words
- Human-readable backup
- Converts to seed (512 bits)

**Example:**
```
abandon ability able about above absent absorb abstract absurd abuse access accident
```

**Derivation Paths (BIP-44):**
```
m / purpose' / coin_type' / account' / change / address_index

Ethereum: m/44'/60'/0'/0/0
          m/44'/60'/0'/0/1
          m/44'/60'/0'/0/2
          
Solana: m/44'/501'/0'/0'
```

**Why useful:**
- Generate infinite addresses from one backup
- All addresses recoverable from seed phrase
- Same seed works across wallets

---

## Consensus Mechanisms

### Proof of Work (PoW)
**How:** Miners solve math puzzles to add blocks.

**Pros:**
- Very secure
- Battle-tested (Bitcoin)

**Cons:**
- Energy intensive
- Slow

**Example:** Bitcoin

---

### Proof of Stake (PoS)
**How:** Validators stake coins to propose blocks. Random selection weighted by stake.

**Pros:**
- Energy efficient
- Faster than PoW

**Cons:**
- "Rich get richer" potential

**Example:** Ethereum (post-Merge)

---

### Proof of History (PoH)
**How:** Cryptographic clock proves time passed between events.

**Why:** Solana uses this for high throughput.

**Benefit:** Validators agree on time without communication.

---

# Ethereum & EVM

## Ethereum Basics

**Ether (ETH):**
- Native cryptocurrency
- Used to pay transaction fees (gas)

**Units:**
- 1 ETH = 10^18 wei
- 1 Gwei = 10^9 wei
- Gas prices quoted in gwei

**Accounts:**
- **EOA (Externally Owned Account):** Controlled by private key (your wallet)
- **Contract Account:** Controlled by code (smart contracts)

---

## Gas

**What is Gas?**
Computational effort required to execute operations.

**Components:**
- **Gas Limit:** Max gas you're willing to spend
- **Gas Price:** How much you pay per unit (in gwei)
- **Base Fee:** Minimum gas price (EIP-1559)
- **Priority Fee:** Tip to miners/validators

**Total Cost:**
```
Fee = Gas Used × (Base Fee + Priority Fee)
```

**Why it matters:**
- Prevents infinite loops (DOS protection)
- Incentivizes validators
- Complex operations cost more

**Common Gas Costs:**
- ETH transfer: ~21,000 gas
- ERC20 transfer: ~50,000 gas
- Uniswap swap: ~100,000-150,000 gas

---

## RPC (Remote Procedure Call)

**What:** API to interact with blockchain.

**Common Methods:**
```javascript
// Get balance
eth_getBalance(address, blockNumber)

// Get block info
eth_getBlockByNumber(blockNumber, fullTx)

// Send transaction
eth_sendRawTransaction(signedTx)

// Call contract (read-only)
eth_call(transaction, blockNumber)

// Get transaction receipt
eth_getTransactionReceipt(txHash)

// Get logs (events)
eth_getLogs(filterObject)
```

**Providers:**
- **Alchemy:** Most popular, generous free tier
- **Infura:** Enterprise-grade
- **QuickNode:** Fast, global network
- **Your own node:** Full control but expensive

---

## Transactions

**Structure:**
```
{
  from: "0x742d...",        // Sender
  to: "0x8a3b...",          // Recipient
  value: "1000000000000000", // Amount in wei
  data: "0x...",            // Smart contract call data
  gas: 21000,               // Gas limit
  gasPrice: "20000000000",  // Price in wei
  nonce: 5,                 // Transaction count
  chainId: 1                // Network ID
}
```

**Nonce:**
- Transaction counter for your address
- Prevents replay attacks
- Must be sequential

**Transaction Lifecycle:**
1. Create transaction
2. Sign with private key
3. Broadcast to network
4. Miners include in block
5. Block confirmed
6. Transaction finalized

**Finality:**
- Ethereum: ~12-64 blocks (~2-13 minutes)
- Faster on L2s (Arbitrum, Optimism)

---

## Block Explorers

**What:** Websites to view blockchain data.

**Etherscan (ethereum.org):**
- View transactions, addresses, contracts
- Verify smart contracts
- Check gas prices
- API for developers

**How to use:**
- Search by address → see all transactions
- Search by tx hash → see details
- Search by block → see all txs in block
- Contract → read/write functions

---

# Smart Contracts (Solidity)

## Basics

**What:** Code that runs on blockchain (immutable programs).

**Solidity:** Language for Ethereum smart contracts (like JavaScript).

**Structure:**
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MyContract {
    // State variables (stored on blockchain)
    uint256 public count;
    
    // Events
    event Incremented(uint256 newCount);
    
    // Functions
    function increment() public {
        count += 1;
        emit Incremented(count);
    }
}
```

---

## Data Types

**Value Types:**
```solidity
bool public isActive;              // true/false
uint256 public number;             // Unsigned int (0 to 2^256-1)
int256 public signedNumber;        // Signed int
address public owner;              // Ethereum address
bytes32 public data;               // Fixed-size bytes
string public name;                // Dynamic string
```

**Reference Types:**
```solidity
uint[] public numbers;             // Dynamic array
mapping(address => uint) balances; // Key-value store
struct User { 
    string name; 
    uint age; 
}
```

---

## Functions

**Visibility:**
- `public`: Callable from anywhere
- `external`: Only from outside contract
- `internal`: Only within contract & derived contracts
- `private`: Only within this contract

**State Mutability:**
- `view`: Reads state, doesn't modify
- `pure`: No state access at all
- `payable`: Can receive ETH

**Examples:**
```solidity
// Read-only
function getBalance() public view returns (uint) {
    return balance;
}

// Modifies state
function setBalance(uint _amount) public {
    balance = _amount;
}

// Accepts ETH
function deposit() public payable {
    balance += msg.value;
}
```

---

## Special Variables

```solidity
msg.sender      // Address calling the function
msg.value       // ETH sent with transaction
block.timestamp // Current block timestamp
block.number    // Current block number
tx.origin       // Original transaction sender (avoid using!)
```

---

## Modifiers

**Purpose:** Reusable checks before function execution.

```solidity
modifier onlyOwner() {
    require(msg.sender == owner, "Not owner");
    _;  // Function body goes here
}

function withdraw() public onlyOwner {
    // Only owner can call this
}
```

---

## Events

**Purpose:** Log important actions (cheap to store, easy to query).

```solidity
event Transfer(address indexed from, address indexed to, uint amount);

function transfer(address to, uint amount) public {
    balances[msg.sender] -= amount;
    balances[to] += amount;
    
    emit Transfer(msg.sender, to, amount);
}
```

**Indexed parameters:** Searchable in logs (max 3).

---

## Common Patterns

### Access Control
```solidity
address public owner;

constructor() {
    owner = msg.sender;
}

modifier onlyOwner() {
    require(msg.sender == owner);
    _;
}
```

### Pausable
```solidity
bool public paused = false;

modifier whenNotPaused() {
    require(!paused);
    _;
}

function pause() public onlyOwner {
    paused = true;
}
```

### Reentrancy Protection
```solidity
bool private locked;

modifier noReentrant() {
    require(!locked);
    locked = true;
    _;
    locked = false;
}
```

---

## Security Best Practices

### Checks-Effects-Interactions
```solidity
// ✅ Good
function withdraw(uint amount) public {
    // Checks
    require(balances[msg.sender] >= amount);
    
    // Effects
    balances[msg.sender] -= amount;
    
    // Interactions
    payable(msg.sender).transfer(amount);
}

// ❌ Bad (vulnerable to reentrancy)
function withdraw(uint amount) public {
    payable(msg.sender).transfer(amount);
    balances[msg.sender] -= amount; // Too late!
}
```

### Use OpenZeppelin
Don't reinvent the wheel. Use audited contracts:
```solidity
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
```

---

# Tokens & NFTs

## ERC20 (Fungible Tokens)

**Standard Interface:**
```solidity
interface IERC20 {
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address to, uint256 amount) external returns (bool);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address from, address to, uint256 amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint256);
    
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
}
```

**Approval Pattern:**
1. Owner approves spender: `approve(spender, amount)`
2. Spender transfers: `transferFrom(owner, recipient, amount)`
3. Used by DEXes (you approve Uniswap to spend your tokens)

**Examples:** USDC, DAI, UNI, LINK

---

## ERC721 (NFTs)

**Standard Interface:**
```solidity
interface IERC721 {
    function balanceOf(address owner) external view returns (uint256);
    function ownerOf(uint256 tokenId) external view returns (address);
    function transferFrom(address from, address to, uint256 tokenId) external;
    function approve(address to, uint256 tokenId) external;
    function tokenURI(uint256 tokenId) external view returns (string memory);
}
```

**Key Differences from ERC20:**
- Each token is unique (tokenId)
- Transfer by tokenId, not amount
- Metadata via tokenURI

**Metadata Structure:**
```json
{
  "name": "Cool NFT #1",
  "description": "This is a cool NFT",
  "image": "ipfs://QmX...",
  "attributes": [
    {"trait_type": "Rarity", "value": "Rare"},
    {"trait_type": "Background", "value": "Blue"}
  ]
}
```

**Examples:** CryptoPunks, BAYC, Azuki

---

## ERC1155 (Multi-Token)

**What:** Single contract can have both fungible and non-fungible tokens.

**Use Cases:**
- Gaming (1000x Gold Coin + 1x Legendary Sword)
- Tickets (General Admission + VIP)

**Benefits:**
- Gas efficient (batch transfers)
- One contract for entire collection

---

# DeFi Concepts

## AMM (Automated Market Maker)

**Traditional Exchange:** Order book (buyers and sellers match).

**AMM:** Liquidity pools + math formula.

**Constant Product Formula (Uniswap V2):**
```
x × y = k

x = Token A reserves
y = Token B reserves
k = Constant
```

**Example:**
- Pool has 100 ETH and 200,000 USDC
- k = 100 × 200,000 = 20,000,000
- You swap 10 ETH for USDC
- New x = 110
- New y = 20,000,000 / 110 = 181,818
- You get: 200,000 - 181,818 = 18,182 USDC

---

## Liquidity Provision

**How it works:**
1. Deposit equal value of both tokens (e.g., $1000 ETH + $1000 USDC)
2. Receive LP (Liquidity Provider) tokens
3. Earn fees from swaps (0.3% per swap)
4. Burn LP tokens to withdraw + earned fees

**Impermanent Loss:**
- Loss compared to just holding tokens
- Happens when price ratio changes
- Called "impermanent" because it can revert

**Example:**
- You deposit 1 ETH + 2000 USDC (ETH = $2000)
- ETH pumps to $4000
- If you held: 1 ETH ($4000) + 2000 USDC = $6000
- If you LP'd: You have less ETH, more USDC ≈ $5657
- Impermanent loss: $343

**When to LP:**
- Stable pairs (USDC/DAI) - minimal IL
- Earn fees > impermanent loss
- Long-term holding anyway

---

## DEX Aggregators

**Problem:** Different DEXes have different prices.

**Solution:** Aggregators find best price across multiple DEXes.

**How:**
1. You want to swap 1 ETH for USDC
2. Aggregator checks Uniswap, Sushiswap, Curve, etc.
3. Splits order for best price (0.5 ETH on Uniswap, 0.5 ETH on Curve)
4. Executes all in one transaction

**Examples:** 1inch, Matcha, Jupiter (Solana)

---

## Slippage

**Definition:** Difference between expected price and execution price.

**Causes:**
- Large trades moving the price
- Someone else trades before you
- Low liquidity pools

**Protection:**
```
Slippage Tolerance: 1%
Expected: 2000 USDC for 1 ETH
Minimum: 1980 USDC (1% less)
If price worse than 1980, transaction reverts
```

---

# Solana Architecture

## Key Differences from Ethereum

| Feature | Ethereum | Solana |
|---------|----------|--------|
| **TPS** | ~15-30 | ~3000-5000 |
| **Block Time** | ~12 seconds | ~400ms |
| **Finality** | ~13 minutes | ~6.4 seconds |
| **Language** | Solidity | Rust |
| **Account Model** | Account balance | UTXO-like |
| **Fees** | Gas (varies) | Fixed (low) |

---

## Account Model

**Everything is an Account:**

**Account Structure:**
```
{
  lamports: 1000000,      // Balance (1 SOL = 10^9 lamports)
  data: [...],            // Arbitrary data
  owner: "Program ID",    // Which program owns this
  executable: false,      // Is this a program?
  rent_epoch: 361         // Rent collection
}
```

**Account Types:**

1. **System Account:** Holds SOL, owned by System Program
2. **Program Account:** Executable code (smart contract)
3. **Data Account:** Stores program data (like contract storage)
4. **Token Account:** Holds SPL tokens

---

## Programs vs Smart Contracts

**Ethereum:** Contract = code + storage together.

**Solana:** Program (code) is separate from data (accounts).

**Example:**
```
Ethereum Token Contract:
- Code: transfer(), balanceOf()
- Storage: mapping balances

Solana Token Program:
- Code: transfer(), etc. (one program for ALL tokens)
- Storage: Separate token accounts for each holder
```

**Why:** Efficiency. One program serves millions of tokens.

---

## PDAs (Program Derived Addresses)

**Problem:** Programs need to "own" accounts but don't have private keys.

**Solution:** Derive addresses from program ID + seeds.

**PDA Properties:**
- Deterministic (same seeds = same address)
- No private key exists (off the curve)
- Only the program can sign for it

**Usage:**
```rust
// Find PDA
let (pda, bump) = Pubkey::find_program_address(
    &[b"escrow", user.key().as_ref()],
    program_id
);

// Use PDA as authority
invoke_signed(
    &instruction,
    accounts,
    &[&[b"escrow", user.key().as_ref(), &[bump]]]
)?;
```

**Use Cases:**
- Escrow authority
- Token vaults
- Program-owned wallets

---

## Cross-Program Invocation (CPI)

**What:** One program calling another program.

**Example:** Your NFT marketplace calls Token Program to transfer NFT.

**Code:**
```rust
// Call Token Program from your program
invoke(
    &spl_token::instruction::transfer(
        token_program.key,
        source.key,
        destination.key,
        authority.key,
        &[],
        amount,
    )?,
    &[source, destination, authority, token_program],
)?;
```

**With PDA signing:**
```rust
invoke_signed(
    &instruction,
    accounts,
    &[&[b"vault", &[bump]]]  // PDA signature
)?;
```

---

## Anchor Framework

**What:** Solana's version of Hardhat/OpenZeppelin.

**Benefits:**
- Less boilerplate
- Automatic serialization/deserialization
- Built-in account validation
- Better error messages

**Basic Structure:**
```rust
use anchor_lang::prelude::*;

#[program]
pub mod my_program {
    use super::*;
    
    pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
        let counter = &mut ctx.accounts.counter;
        counter.count = 0;
        Ok(())
    }
}

#[derive(Accounts)]
pub struct Initialize<'info> {
    #[account(init, payer = user, space = 8 + 8)]
    pub counter: Account<'info, Counter>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}

#[account]
pub struct Counter {
    pub count: u64,
}
```

**Constraints:**
- `init`: Create new account
- `mut`: Account can be modified
- `has_one`: Validation check
- `seeds`, `bump`: For PDAs

---

## SPL Token Program

**Solana's token standard (like ERC20).**

**Account Types:**

1. **Mint Account:** Token metadata (supply, decimals)
2. **Token Account:** Individual holder's balance
3. **Associated Token Account (ATA):** Deterministic token account for a wallet

**Operations:**
```rust
// Create token mint
spl_token::instruction::initialize_mint(...)

// Create token account
spl_token::instruction::initialize_account(...)

// Mint tokens
spl_token::instruction::mint_to(...)

// Transfer
spl_token::instruction::transfer(...)

// Burn
spl_token::instruction::burn(...)
```

**ATA (Associated Token Account):**
```
ATA = derive(wallet_address, mint_address, token_program)
```
- One ATA per wallet per token
- Automatic, predictable address

---

# Rust Programming

## Ownership

**Core Concept:** Every value has one owner. When owner goes out of scope, value is dropped.

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;  // s1 is MOVED to s2
    
    // println!("{}", s1);  // ❌ Error! s1 no longer valid
    println!("{}", s2);     // ✅ Works
}
```

**Why:** No garbage collector. Memory safety without runtime cost.

---

## Borrowing & References

**Immutable Borrow:**
```rust
fn print_length(s: &String) {  // & = borrow
    println!("{}", s.len());
}

let s = String::from("hello");
print_length(&s);  // s still valid after
println!("{}", s); // ✅ Works
```

**Mutable Borrow:**
```rust
fn append(s: &mut String) {
    s.push_str(" world");
}

let mut s = String::from("hello");
append(&mut s);
println!("{}", s);  // "hello world"
```

**Rules:**
- Can have any number of immutable borrows OR one mutable borrow
- Not both at the same time
- References must always be valid

---

## Error Handling

**Option<T>:**
```rust
fn find_user(id: u32) -> Option<User> {
    if user_exists(id) {
        Some(user)
    } else {
        None
    }
}

// Using Option
match find_user(1) {
    Some(user) => println!("Found: {}", user.name),
    None => println!("Not found"),
}

// Or use ?
let user = find_user(1)?;  // Returns None if not found
```

**Result<T, E>:**
```rust
fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err("Division by zero".to_string())
    } else {
        Ok(a / b)
    }
}

// Using Result
match divide(10.0, 2.0) {
    Ok(result) => println!("{}", result),
    Err(e) => println!("Error: {}", e),
}

// Or use ?
let result = divide(10.0, 2.0)?;  // Propagates error
```

---

## Traits

**Like interfaces in other languages:**

```rust
trait Animal {
    fn speak(&self) -> String;
}

struct Dog;
struct Cat;

impl Animal for Dog {
    fn speak(&self) -> String {
        "Woof!".to_string()
    }
}

impl Animal for Cat {
    fn speak(&self) -> String {
        "Meow!".to_string()
    }
}

fn make_sound<T: Animal>(animal: T) {
    println!("{}", animal.speak());
}
```

---

# Data Structures

## Arrays & Strings

**Two Pointers:**
```
Two Sum: left/right pointers
Valid Palindrome: Compare from both ends
Container With Most Water: Move pointer with smaller height
```

**Sliding Window:**
```
Longest Substring: Expand right, contract left when duplicate
Max Subarray: Keep track of current sum
```

---

## Hash Maps

**O(1) lookup, insert, delete (average).**

**Common Patterns:**
- Frequency counting: `map[char]++`
- Check existence: `if char in map`
- Two Sum: Store complement

**Problems:**
- Group Anagrams: Hash by sorted chars
- Longest Substring: Track last seen index

---

## Linked Lists

**Operations:**
- Reverse: 3 pointers (prev, curr, next)
- Detect cycle: Fast/slow pointers
- Merge: Compare values, adjust pointers

**Fast & Slow Pointers:**
```
Slow: +1 each step
Fast: +2 each step
Meet = cycle exists
```

---

## Stacks & Queues

**Stack (LIFO):**
- Valid Parentheses: Push open, pop on close
- Min Stack: Parallel stack tracking min

**Queue (FIFO):**
- BFS: Use queue
- Implement with 2 stacks

---

## Trees

**Traversals:**
- **Inorder:** Left, Root, Right (BST → sorted)
- **Preorder:** Root, Left, Right (copy tree)
- **Postorder:** Left, Right, Root (delete tree)
- **Level Order:** BFS with queue

**BST Properties:**
- Left subtree < root < right subtree
- Inorder traversal is sorted

---

## Graphs

**Representations:**
- Adjacency List: `{node: [neighbors]}`
- Adjacency Matrix: `matrix[i][j] = 1 if edge`

**DFS (Depth-First):**
```python
def dfs(node, visited):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(neighbor, visited)
```

**BFS (Breadth-First):**
```python
from collections import deque

def bfs(start):
    queue = deque([start])
    visited = {start}
    
    while queue:
        node = queue.popleft()
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

---

# Algorithms

## Binary Search

**O(log n) search in sorted array.**

```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = (left + right) // 2
        
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1
```

**Variations:**
- Find first/last occurrence
- Search in rotated array
- Find peak element

---

## Dynamic Programming

**Concept:** Break problem into subproblems, store results.

**1D DP (Climbing Stairs):**
```python
def climb_stairs(n):
    if n <= 2:
        return n
    
    dp = [0] * (n + 1)
    dp[1] = 1
    dp[2] = 2
    
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    
    return dp[n]
```

**2D DP (Unique Paths):**
```python
def unique_paths(m, n):
    dp = [[1] * n for _ in range(m)]
    
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    
    return dp[m-1][n-1]
```

**Patterns:**
- Knapsack
- Longest Common Subsequence
- Edit Distance

---

## Backtracking

**Template:**
```python
def backtrack(path, choices):
    if is_solution(path):
        result.append(path[:])
        return
    
    for choice in choices:
        # Make choice
        path.append(choice)
        
        # Recurse
        backtrack(path, remaining_choices)
        
        # Undo choice
        path.pop()
```

**Problems:**
- Subsets
- Permutations
- Combination Sum

---

## Greedy

**Concept:** Make locally optimal choice at each step.

**When it works:**
- Problem has optimal substructure
- Greedy choice leads to global optimum

**Problems:**
- Jump Game: Jump to farthest reachable
- Meeting Rooms: Sort by end time, take earliest

---

# System Design

## Scalability

**Vertical Scaling:** Add more power (CPU, RAM) to one machine.
- **Pros:** Simple
- **Cons:** Hardware limits, single point of failure

**Horizontal Scaling:** Add more machines.
- **Pros:** No limits, fault tolerant
- **Cons:** Complexity

---

## Load Balancing

**What:** Distribute traffic across multiple servers.

**Algorithms:**
- Round Robin
- Least Connections
- IP Hash

**Types:**
- L4 (Transport): Based on IP/port
- L7 (Application): Based on HTTP headers, URLs

---

## Caching

**Why:** Reduce database load, faster responses.

**Strategies:**
- **Cache Aside:** App checks cache, then DB
- **Write Through:** Write to cache and DB simultaneously
- **Write Behind:** Write to cache, async to DB

**Eviction Policies:**
- LRU (Least Recently Used)
- LFU (Least Frequently Used)
- FIFO (First In First Out)

---

## Databases

**SQL (Relational):**
- Structured data
- ACID transactions
- Examples: PostgreSQL, MySQL

**NoSQL:**
- Flexible schema
- Horizontal scaling
- Types: Key-Value (Redis), Document (MongoDB), Column (Cassandra), Graph (Neo4j)

**When to use:**
- SQL: Complex queries, transactions
- NoSQL: High write load, flexible schema, scaling

---

## CAP Theorem

**You can only have 2 of 3:**

- **Consistency:** All nodes see same data
- **Availability:** Every request gets response
- **Partition Tolerance:** System works despite network issues

**Reality:** Network partitions happen, so choose C or A.

**Examples:**
- CP: Bank systems (consistency over availability)
- AP: Social media feeds (availability over consistency)

---

## Microservices

**Monolith:** One big application.

**Microservices:** Small, independent services.

**Benefits:**
- Independent deployment
- Technology flexibility
- Scalability

**Challenges:**
- Complexity
- Data consistency
- Network latency

---

## Message Queues

**What:** Async communication between services.

**Use Cases:**
- Decouple services
- Handle traffic spikes
- Retry failed operations

**Examples:** RabbitMQ, Kafka, AWS SQS

**Pattern:**
```
Producer → Queue → Consumer
```

---

## Common Designs

**URL Shortener:**
- Hash input URL or use counter
- Store in key-value DB
- Redirect on lookup

**Twitter Feed:**
- Fanout on write: Pre-compute feeds
- Cache recent tweets
- Pagination

**Rate Limiter:**
- Token bucket algorithm
- Track requests per user/IP
- Return 429 if exceeded

---

# Quick Reference Cheat Sheet

## Ethereum

```
1 ETH = 10^18 wei
1 Gwei = 10^9 wei

Private Key → Public Key → Address
SHA-256, Keccak-256 (Ethereum)

Gas = Gas Used × (Base Fee + Priority Fee)
```

## Solana

```
1 SOL = 10^9 lamports

Account = {lamports, data, owner, executable}
PDA = Pubkey::find_program_address(seeds, program_id)
```

## Data Structures

```
Array/String: Two Pointers, Sliding Window
Hash Map: O(1) lookup, frequency counting
Linked List: Fast/slow pointers, reverse
Stack: LIFO, valid parentheses
Queue: FIFO, BFS
Tree: DFS (inorder/preorder/postorder), BFS
Graph: DFS, BFS, adjacency list
```

## Algorithms

```
Binary Search: O(log n), sorted arrays
DP: Memoization, tabulation
Backtracking: Choose, explore, unchoose
Greedy: Local optimum → global optimum
```

---

**Use this as your quick reference while building. Refer back whenever you need a concept refresher!** 📚
