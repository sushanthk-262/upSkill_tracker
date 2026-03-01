# Your 36-Week Web3 Journey: Week-by-Week Build Guide

A practical roadmap to becoming a production-ready Web3 developer in 8-10 hours per week.

---

## How to Use This Guide

Each week includes:
- **What you're building** - The concrete deliverable
- **Why it matters** - Industry relevance
- **How to build it** - Step-by-step breakdown
- **Time estimate** - Realistic hours (Learn + Build)
- **Success criteria** - How you know you're done

**Big projects (2+ weeks) are grouped into blocks.**

---

# Phase 1: Foundation (Weeks 1-4)
*Get your hands dirty with blockchain basics and wallets*

---

## Week 1: Your First Blockchain Script

### 🎯 Deliverable
**Script that generates 10 Ethereum wallets and checks their balances on Etherscan**

### Why This Matters
Every Web3 app needs wallet generation. You're building the foundation of MetaMask, Phantom, and every crypto wallet.

### What You'll Build
```javascript
// generate-wallets.js
const ethers = require('ethers');

// Generate 10 random wallets
for (let i = 0; i < 10; i++) {
  const wallet = ethers.Wallet.createRandom();
  console.log(`Wallet ${i + 1}:`);
  console.log(`Address: ${wallet.address}`);
  console.log(`Private Key: ${wallet.privateKey}\n`);
}

// Check balance
const provider = new ethers.providers.AlchemyProvider("mainnet", "YOUR_KEY");
const balance = await provider.getBalance("0x742d35...");
console.log(`Balance: ${ethers.utils.formatEther(balance)} ETH`);
```

### How to Build (6 hours)

**Monday (2 hrs):**
- Read: Ethereum.org basics (1 hr)
- Install Node.js + Ethers.js (30 min)
- Run: Generate your first address (30 min)

**Wednesday (2 hrs):**
- Read: How addresses are derived from private keys (30 min)
- Code: Loop to generate 10 wallets (1 hr)
- Test: Verify addresses on Etherscan (30 min)

**Weekend (2 hrs):**
- Add: Balance checking with Alchemy API (1 hr)
- Polish: Error handling, clean output (30 min)
- Ship: Push to GitHub with README (30 min)

### Success Criteria
- ✅ Script generates 10 valid addresses
- ✅ Can check balance of any Ethereum address
- ✅ Code on GitHub with README
- ✅ Verified addresses exist on Etherscan

### DSA Task (2.5 hrs)
**Solve 3 LeetCode problems using two pointers:**
- Two Sum
- Valid Palindrome  
- Best Time to Buy and Sell Stock

---

## Week 2: Gas Price Alert Bot

### 🎯 Deliverable
**Script that checks Ethereum gas prices and alerts when gas drops below 30 gwei**

### Why This Matters
Understanding gas is crucial for production apps. Users want to know when to transact. You're building what GasNow and ETH Gas Station do.

### What You'll Build
```javascript
// gas-monitor.js
const ethers = require('ethers');

async function checkGas() {
  const provider = new ethers.providers.AlchemyProvider("mainnet", "YOUR_KEY");
  const gasPrice = await provider.getGasPrice();
  const gwei = ethers.utils.formatUnits(gasPrice, "gwei");
  
  console.log(`Current gas: ${gwei} gwei`);
  
  if (parseFloat(gwei) < 30) {
    console.log("🚨 LOW GAS ALERT! Good time to transact!");
    // Could send email/SMS here
  }
}

setInterval(checkGas, 60000); // Check every minute
```

### How to Build (6 hours)

**Monday (2 hrs):**
- Read: What is gas, why it matters (1 hr)
- Read: Etherscan Gas API docs (30 min)
- Code: Basic gas price fetch (30 min)

**Thursday (2 hrs):**
- Add: Alert logic when gas < 30 gwei (1 hr)
- Add: Continuous monitoring (setInterval) (30 min)
- Test: Verify it runs for 10+ minutes (30 min)

**Weekend (2 hrs):**
- Bonus: Deploy to free cloud (Render/Railway) (1 hr)
- Bonus: Add notification (console → email/Discord webhook) (1 hr)
- Ship: README with setup instructions

### Success Criteria
- ✅ Fetches real-time gas prices
- ✅ Alerts when gas is low
- ✅ Runs continuously without crashing
- ✅ (Bonus) Deployed to cloud

### DSA Task (2.5 hrs)
**Solve 3 hash map problems:**
- Group Anagrams
- Longest Substring Without Repeating Characters
- Contains Duplicate

---

## Week 3: CLI Wallet MVP

### 🎯 Deliverable
**Command-line wallet that generates seeds, derives 5 accounts, and sends ETH on testnet**

### Why This Matters
This is how MetaMask works under the hood. You're building a production wallet.

### What You'll Build
```bash
$ node wallet.js generate
Mnemonic: abandon ability able about above absent absorb abstract absurd abuse access accident
Account 0: 0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb

$ node wallet.js derive "your mnemonic" 5
Account 0: 0x742d...
Account 1: 0x8Ab3...
Account 2: 0x1Cd4...
Account 3: 0x9Ef5...
Account 4: 0x2Fa6...

$ node wallet.js send <private_key> 0x123... 0.01
Sending 0.01 ETH to 0x123...
Transaction hash: 0xabc123...
✅ Confirmed!
```

### How to Build (6 hours)

**Tuesday (2 hrs):**
- Read: BIP39 (mnemonics), BIP44 (derivation paths) (1 hr)
- Code: Generate mnemonic → private key (1 hr)

**Thursday (2 hrs):**
- Code: Derive multiple addresses from one seed (1.5 hrs)
- Test: Verify derivation matches MetaMask (30 min)

**Weekend (2 hrs):**
- Code: Send transaction function (1 hr)
- Test: Send on Sepolia testnet (get free ETH from faucet) (30 min)
- Polish: Add help menu, error messages (30 min)

### Success Criteria
- ✅ Generates BIP39 mnemonics
- ✅ Derives 5 accounts from one seed
- ✅ Successfully sends ETH on Sepolia
- ✅ Transaction visible on Sepolia Etherscan
- ✅ Code on GitHub

### DSA Task (2.5 hrs)
**Solve 3 linked list problems:**
- Reverse Linked List
- Merge Two Sorted Lists
- Linked List Cycle

---

## Week 4: React Wallet UI (Start)

### 🎯 Deliverable
**React app that connects to MetaMask, shows balance, and sends ETH**

### Why This Matters
Every Web3 app needs wallet connection. This is the #1 skill companies look for.

### What You'll Build
```jsx
// App.jsx
import { ConnectButton } from '@rainbow-me/rainbowkit';
import { useAccount, useBalance, useSendTransaction } from 'wagmi';

function App() {
  const { address } = useAccount();
  const { data: balance } = useBalance({ address });
  const { sendTransaction } = useSendTransaction();

  return (
    <div>
      <ConnectButton />
      {address && (
        <div>
          <p>Balance: {balance?.formatted} ETH</p>
          <button onClick={() => sendTransaction({
            to: '0x123...',
            value: parseEther('0.01')
          })}>
            Send 0.01 ETH
          </button>
        </div>
      )}
    </div>
  );
}
```

### How to Build (6 hours)

**Tuesday (2 hrs):**
- Setup: Vite + React (30 min)
- Install: Wagmi + RainbowKit (30 min)
- Code: Basic wallet connection (1 hr)

**Thursday (2 hrs):**
- Code: Display address and balance (1 hr)
- Code: Send ETH form (1 hr)

**Weekend (2 hrs):**
- Test: Full flow on Sepolia (30 min)
- Add: Loading states, error handling (1 hr)
- Deploy: Vercel (30 min)

### Success Criteria
- ✅ MetaMask connects
- ✅ Shows ETH balance
- ✅ Can send ETH on testnet
- ✅ Deployed live on Vercel
- ✅ Mobile responsive

### DSA Task (2.5 hrs)
**Solve 2 stack/queue problems:**
- Valid Parentheses
- Min Stack

---

# Phase 2: Tokens & Smart Contracts (Weeks 5-12)
*Deploy your first contracts and build token interfaces*

---

## Week 5: Deploy Your First ERC20 Token

### 🎯 Deliverable
**ERC20 token deployed to testnet and verified on Etherscan**

### Why This Matters
ERC20 is the standard for tokens. USDC, DAI, UNI all use it. This is fundamental.

### What You'll Build
```solidity
// MyToken.sol
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor() ERC20("MyToken", "MTK") {
        _mint(msg.sender, 1000000 * 10**18);
    }
}
```

### How to Build (6 hours)

**Monday (2 hrs):**
- Read: OpenZeppelin ERC20 docs (1 hr)
- Setup: Hardhat project (30 min)
- Code: Basic ERC20 contract (30 min)

**Wednesday (2 hrs):**
- Code: Deploy script (30 min)
- Deploy: To Sepolia testnet (30 min)
- Verify: On Etherscan (1 hr - can be tricky)

**Weekend (2 hrs):**
- Add: Burn function (30 min)
- Add: Pause mechanism (OpenZeppelin Pausable) (30 min)
- Test: Write 3 tests with Hardhat (1 hr)

### Success Criteria
- ✅ Token deployed to Sepolia
- ✅ Verified on Etherscan (green checkmark)
- ✅ Can mint and transfer tokens
- ✅ 3+ tests passing

### DSA Task (2.5 hrs)
**Solve 3 binary tree problems:**
- Maximum Depth of Binary Tree
- Invert Binary Tree
- Symmetric Tree

---

## Week 6: NFT Contract

### 🎯 Deliverable
**ERC721 NFT contract with 5 minted NFTs on IPFS**

### Why This Matters
NFTs are everywhere. Understanding ERC721 opens doors to marketplaces, gaming, identity.

### What You'll Build
- Contract that mints NFTs
- Metadata hosted on IPFS
- Token URIs pointing to decentralized storage

### How to Build (6 hours)

**Tuesday (2 hrs):**
- Read: EIP-721 standard (1 hr)
- Code: Basic ERC721 contract (1 hr)

**Thursday (2 hrs):**
- Upload: 5 images to IPFS via NFT.Storage (30 min)
- Create: metadata.json for each (30 min)
- Code: Minting function with tokenURI (1 hr)

**Weekend (2 hrs):**
- Mint: 5 NFTs on testnet (30 min)
- Verify: See them on Testnets OpenSea (30 min)
- Add: Whitelist or mint limit (1 hr)

### Success Criteria
- ✅ NFT contract deployed
- ✅ 5 NFTs minted with metadata
- ✅ Visible on OpenSea testnet
- ✅ Metadata on IPFS

### DSA Task (2.5 hrs)
**Solve 3 binary search problems:**
- Binary Search
- Search in Rotated Sorted Array
- Find Minimum in Rotated Sorted Array

---

## Week 7: Token Balance Display in Wallet

### 🎯 Deliverable
**Wallet UI now shows all ERC20 token balances**

### Why This Matters
Every wallet (MetaMask, Phantom) shows token balances. You're building real wallet functionality.

### How to Build (6 hours)

**Monday (2 hrs):**
- Read: Alchemy Token Balance API (1 hr)
- Code: Fetch token balances for address (1 hr)

**Wednesday (2 hrs):**
- Code: Display token list with logos (1 hr)
- Add: USD value for each token (CoinGecko API) (1 hr)

**Weekend (2 hrs):**
- Polish: Loading states, empty states (30 min)
- Add: Refresh button (30 min)
- Deploy: Update live version (1 hr)

### Success Criteria
- ✅ Shows ETH + all ERC20 balances
- ✅ Displays USD values
- ✅ Updates on refresh
- ✅ Handles wallets with 0 tokens

### DSA Task (2.5 hrs)
**Solve 3 graph problems:**
- Number of Islands
- Clone Graph
- Course Schedule

---

## Week 8: Transaction History

### 🎯 Deliverable
**Wallet shows last 10 transactions with details**

### Why This Matters
Users need to see their activity. This is core wallet functionality.

### How to Build (6 hours)

**Tuesday (2 hrs):**
- Read: Etherscan API for transactions (1 hr)
- Code: Fetch transaction history (1 hr)

**Thursday (2 hrs):**
- Code: Display tx list (hash, to/from, amount, time) (1 hr)
- Add: Click to view on Etherscan (30 min)
- Add: Filter (sent vs received) (30 min)

**Weekend (2 hrs):**
- Polish: Time formatting ("2 hours ago") (30 min)
- Add: Pagination or "load more" (1 hr)
- Test: Various wallet scenarios (30 min)

### Success Criteria
- ✅ Shows last 10 transactions
- ✅ Correct data (amount, timestamp, direction)
- ✅ Links to Etherscan work
- ✅ Handles empty history

### DSA Task (2.5 hrs)
**Solve 2 heap problems:**
- Kth Largest Element
- Top K Frequent Elements

---

## Weeks 9-10: MINI PROJECT - Portfolio Tracker

### 🎯 Deliverable
**Full-stack app that tracks crypto holdings across multiple wallets with charts**

### Why This Matters
This is your first complete product. Backend + frontend + database. Companies love this.

### What You're Building
- **Backend:** Express API that fetches balances from Alchemy
- **Database:** PostgreSQL to cache data
- **Frontend:** React dashboard with charts
- **Features:** Add wallets, view holdings, total portfolio value

---

### Week 9: Backend API

**What to Build (7 hours):**

**Setup (1 hr):**
```bash
mkdir portfolio-tracker-api
npm init -y
npm install express pg axios dotenv
```

**Database Schema (30 min):**
```sql
CREATE TABLE wallets (
  id SERIAL PRIMARY KEY,
  address VARCHAR(42) UNIQUE,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE balances (
  id SERIAL PRIMARY KEY,
  wallet_id INT REFERENCES wallets(id),
  token_address VARCHAR(42),
  balance VARCHAR(100),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

**API Endpoints (4 hrs):**
```javascript
// GET /api/wallets - List all tracked wallets
// POST /api/wallets - Add new wallet to track
// GET /api/wallets/:address/balances - Get token balances
// DELETE /api/wallets/:address - Remove wallet

app.post('/api/wallets', async (req, res) => {
  const { address } = req.body;
  
  // Fetch balances from Alchemy
  const balances = await fetchBalances(address);
  
  // Store in DB
  await saveToDatabase(address, balances);
  
  res.json({ success: true });
});
```

**Caching Logic (1.5 hrs):**
- Cache balances for 5 minutes
- Only fetch from Alchemy if cache expired
- Background job to refresh every 5 min

**Success Criteria:**
- ✅ API returns balances for any address
- ✅ Data cached in PostgreSQL
- ✅ Can track multiple wallets
- ✅ Postman tests pass

---

### Week 10: Frontend Dashboard

**What to Build (7 hours):**

**Setup (30 min):**
```bash
npx create-react-app portfolio-tracker
npm install recharts axios react-query
```

**Components (3 hrs):**
- **AddWallet:** Input to add new wallet address
- **WalletList:** Shows all tracked wallets
- **BalanceChart:** Pie chart of holdings
- **TokenTable:** Table of all tokens with USD values

**Data Fetching (2 hrs):**
```jsx
import { useQuery } from 'react-query';

function Portfolio() {
  const { data } = useQuery('wallets', 
    () => fetch('/api/wallets').then(r => r.json())
  );
  
  return (
    <div>
      {data.wallets.map(wallet => (
        <WalletCard key={wallet.address} wallet={wallet} />
      ))}
    </div>
  );
}
```

**Charts (1 hr):**
```jsx
<PieChart width={400} height={400}>
  <Pie data={holdings} dataKey="value" nameKey="token" />
</PieChart>
```

**Polish & Deploy (30 min):**
- Responsive design
- Loading states
- Deploy frontend to Vercel
- Deploy backend to Railway

**Success Criteria:**
- ✅ Can add/remove wallets via UI
- ✅ Shows all holdings with USD value
- ✅ Pie chart displays allocation
- ✅ Live at public URL
- ✅ Works on mobile

---

### DSA (Weeks 9-10)
**Week 9:** Solve 3 DP problems (Climbing Stairs, House Robber, Max Subarray)
**Week 10:** Solve 2 DP problems (Unique Paths, Coin Change)

---

## Week 11: Swap Integration

### 🎯 Deliverable
**Add token swap feature to wallet using 1inch API**

### Why This Matters
Swaps are core DeFi. You're integrating with production DEX aggregators.

### How to Build (6 hours)

**Monday (2 hrs):**
- Read: 1inch API docs (1 hr)
- Get: 1inch API key (30 min)
- Test: Quote endpoint in Postman (30 min)

**Wednesday (2 hrs):**
- Code: Swap form (select tokens, enter amount) (1 hr)
- Code: Fetch quote from 1inch (30 min)
- Code: Show expected output + gas (30 min)

**Weekend (2 hrs):**
- Code: Execute swap transaction (1 hr)
- Add: Slippage tolerance setting (30 min)
- Test: Real swap on testnet (30 min)

### Success Criteria
- ✅ Can swap any token for another
- ✅ Shows real-time quotes
- ✅ Displays gas estimate
- ✅ Successful swap on testnet

### DSA Task (2.5 hrs)
**Solve 2 backtracking problems:**
- Subsets
- Combination Sum

---

## Week 12: Smart Contract with Hardhat

### 🎯 Deliverable
**ERC20 token with burn + pause, full test suite, deployed**

### Why This Matters
Production contracts need testing and safety features. This is professional-level work.

### How to Build (6 hours)

**Tuesday (2 hrs):**
- Setup: Hardhat + OpenZeppelin (30 min)
- Code: Token with burn and pause (1 hr)
- Code: Ownership controls (30 min)

**Thursday (2 hrs):**
- Write: 5+ tests (transfers, burns, pause, ownership) (2 hrs)

**Weekend (2 hrs):**
- Run: Test coverage report (30 min)
- Deploy: With deployment script (30 min)
- Document: Contract functions in README (1 hr)

### Success Criteria
- ✅ Contract has burn and pause
- ✅ 100% test coverage on core functions
- ✅ Deployed and verified
- ✅ README explains all functions

### DSA Task (2.5 hrs)
**Solve 2 greedy problems:**
- Jump Game
- Meeting Rooms

---

# Phase 3: Production DApps (Weeks 13-16)
*Build and deploy complete applications*

---

## Week 13: Multi-Sig Wallet Contract

### 🎯 Deliverable
**2-of-3 multi-signature wallet contract with tests**

### Why This Matters
Multi-sigs secure millions in crypto. Gnosis Safe uses this pattern.

### How to Build (6.5 hours)

**What You're Building:**
- Contract that requires 2 out of 3 owners to approve transactions
- Propose transaction → Sign → Execute when threshold met

**Monday (2 hrs):**
- Read: Gnosis Safe architecture (1 hr)
- Code: Basic multi-sig structure (1 hr)

**Wednesday (2.5 hrs):**
- Code: Submit transaction proposal (1 hr)
- Code: Approve/reject logic (1 hr)
- Code: Execute when threshold reached (30 min)

**Weekend (2 hrs):**
- Write: 6+ tests (proposal, approval, execution, edge cases) (2 hrs)

### Success Criteria
- ✅ Requires 2 signatures to execute
- ✅ Can't execute with only 1 signature
- ✅ Tests cover happy path + edge cases
- ✅ Deployed to testnet

### DSA Task (2 hrs)
**Solve 2 interval problems:**
- Merge Intervals
- Meeting Rooms II

---

## Weeks 14-15: MINI PROJECT - NFT Minting DApp

### 🎯 Deliverable
**Complete NFT minting site with whitelist, live on testnet**

---

### Week 14: Smart Contract

**What to Build (6.5 hours):**

**Contract Features:**
- Whitelist using Merkle tree
- Mint limit per wallet
- Pausable minting
- Reveal mechanism

**Monday (2 hrs):**
- Read: Merkle tree for whitelists (1 hr)
- Code: ERC721 contract base (1 hr)

**Wednesday (2.5 hrs):**
- Install: merkletreejs library (30 min)
- Code: Whitelist verification (1 hr)
- Code: Mint limits (max per wallet) (1 hr)

**Weekend (2 hrs):**
- Code: Pausable + reveal logic (1 hr)
- Write: Tests for all features (1 hr)

**Success Criteria:**
- ✅ Only whitelist can mint
- ✅ Enforces mint limits
- ✅ Can pause/unpause
- ✅ All tests pass

---

### Week 15: Frontend

**What to Build (6.5 hours):**

**Features:**
- Connect wallet
- Check if user is whitelisted
- Mint NFT button
- Show owned NFTs

**Tuesday (2.5 hrs):**
- Setup: React + Wagmi (30 min)
- Code: Wallet connection (30 min)
- Code: Check whitelist status (1 hr)
- Code: Mint button (30 min)

**Thursday (2 hrs):**
- Code: Display owned NFTs (fetch tokenIds) (1 hr)
- Add: Metadata display (images from IPFS) (1 hr)

**Weekend (2 hrs):**
- Polish: Loading states, success/error messages (1 hr)
- Deploy: Vercel (30 min)
- Test: Full minting flow (30 min)

**Success Criteria:**
- ✅ Users can connect and mint
- ✅ Whitelist check works
- ✅ Shows owned NFTs with images
- ✅ Live at public URL
- ✅ Mobile responsive

---

### DSA (Weeks 14-15)
**Week 14:** Solve 2 bit manipulation (Single Number, Counting Bits)
**Week 15:** Complete 1 mock interview on Pramp

---

## Week 16: Production Deploy

### 🎯 Deliverable
**NFT DApp deployed to production with monitoring and docs**

### Why This Matters
Shipping to production is different. You need monitoring, error tracking, and docs.

### How to Build (6.5 hours)

**Tuesday (2 hrs):**
- Add: Sentry for error tracking (1 hr)
- Add: Analytics (Mixpanel or Plausible) (1 hr)

**Thursday (2 hrs):**
- Add: Rate limiting on backend (30 min)
- Add: CORS, security headers (30 min)
- Test: Load testing with 100 concurrent requests (1 hr)

**Weekend (2.5 hrs):**
- Write: Comprehensive README (1 hr)
- Write: Architecture diagram (30 min)
- Record: 2-min demo video (30 min)
- Polish: Meta tags, OG images (30 min)

### Success Criteria
- ✅ Error tracking active (Sentry catches errors)
- ✅ Analytics tracking mints
- ✅ README has architecture + setup
- ✅ Demo video shows full flow
- ✅ Handles errors gracefully

### DSA Task (2 hrs)
**Watch 2 system design videos:**
- URL Shortener
- Rate Limiter

---

# Phase 4: Solana Development (Weeks 17-24)
*Learn Rust and build Solana programs*

---

## Week 17: Rust Basics

### 🎯 Deliverable
**Complete Rustlings exercises 1-50 + build CLI tool**

### Why This Matters
Solana programs are written in Rust. You need Rust to build on Solana.

### How to Build (6.5 hours)

**Monday (2 hrs):**
- Read: Rust Book chapters 1-3 (1 hr)
- Install: Rust + cargo (30 min)
- Do: Rustlings 1-20 (30 min)

**Wednesday (2 hrs):**
- Read: Rust Book chapters 4-6 (ownership!) (1 hr)
- Do: Rustlings 21-40 (1 hr)

**Weekend (2.5 hrs):**
- Do: Rustlings 41-50 (1 hr)
- Build: Simple CLI tool (reads file, processes data) (1.5 hrs)

### Success Criteria
- ✅ Rustlings 1-50 complete
- ✅ CLI tool runs successfully
- ✅ Understand ownership + borrowing

### DSA Task (2 hrs)
**System Design:** Design Twitter feed (high-level architecture)

---

## Week 18: Rust Intermediate

### 🎯 Deliverable
**HTTP client in Rust using reqwest crate**

### Why This Matters
Working with external crates is essential. HTTP clients are everywhere.

### How to Build (6.5 hours)

**Tuesday (2.5 hrs):**
- Read: Rust by Example (1 hr)
- Read: reqwest docs (30 min)
- Code: Basic GET request (1 hr)

**Thursday (2 hrs):**
- Code: POST request with JSON (1 hr)
- Code: Error handling with Result<T, E> (1 hr)

**Weekend (2 hrs):**
- Code: Fetch from API, parse JSON, save to file (1.5 hrs)
- Test: Different endpoints (30 min)

### Success Criteria
- ✅ Can fetch and parse JSON
- ✅ Proper error handling
- ✅ Saves data to file
- ✅ Code is idiomatic Rust

### DSA Task (2 hrs)
**System Design:** Design Instagram (storage, CDN, feed)

---

## Week 19: Solana CLI

### 🎯 Deliverable
**Create SPL token, airdrop, and transfer using Solana CLI**

### Why This Matters
Understanding Solana CLI is foundational. This is how you interact with the blockchain.

### How to Build (6.5 hours)

**Monday (2 hrs):**
- Install: Solana CLI (30 min)
- Read: Solana CLI docs (1 hr)
- Create: Keypair, airdrop SOL on devnet (30 min)

**Wednesday (2.5 hrs):**
- Create: SPL token mint (1 hr)
- Create: Token account (30 min)
- Mint: Tokens to your account (1 hr)

**Weekend (2 hrs):**
- Transfer: Tokens to another address (1 hr)
- Burn: Some tokens (30 min)
- Document: All commands in README (30 min)

### Success Criteria
- ✅ SPL token created on devnet
- ✅ Minted and transferred tokens
- ✅ Can verify on Solana Explorer
- ✅ README has all commands

### DSA Task (2 hrs)
**Complete 1 hard mock interview**

---

## Week 20: First Solana Program

### 🎯 Deliverable
**Counter program with Anchor: initialize, increment, deployed to devnet**

### Why This Matters
This is your first on-chain program. Foundation for all Solana development.

### How to Build (6.5 hours)

**Tuesday (2.5 hrs):**
- Install: Anchor framework (30 min)
- Read: Anchor tutorial (1 hr)
- Code: Basic counter program (1 hr)

**Thursday (2 hrs):**
- Code: Increment instruction (1 hr)
- Write: Tests with Anchor (1 hr)

**Weekend (2 hrs):**
- Deploy: To devnet (1 hr)
- Test: Call from CLI or script (30 min)
- Document: Program structure (30 min)

### Success Criteria
- ✅ Program deployed to devnet
- ✅ Can increment counter on-chain
- ✅ Tests pass locally
- ✅ Verified on Solana Explorer

### DSA Task (2 hrs)
**Solve 5 company-tagged problems**

---

## Week 21: Anchor Todo App

### 🎯 Deliverable
**Todo list program with create, update, delete + tests**

### Why This Matters
CRUD operations are fundamental. This pattern applies to all Solana apps.

### How to Build (6.5 hours)

**Monday (2 hrs):**
- Design: Account structure (todos stored in PDA) (30 min)
- Code: Initialize todo list account (1.5 hrs)

**Wednesday (2.5 hrs):**
- Code: Add todo instruction (1 hr)
- Code: Update todo instruction (1 hr)
- Code: Delete todo instruction (30 min)

**Weekend (2 hrs):**
- Write: Tests for all instructions (1.5 hrs)
- Deploy: To devnet (30 min)

### Success Criteria
- ✅ Can create, update, delete todos
- ✅ Tests cover all operations
- ✅ Deployed to devnet
- ✅ PDAs working correctly

### DSA Task (2 hrs)
**Solve 2 hard DP problems**

---

## Weeks 22-23: MINI PROJECT - Solana Escrow

### 🎯 Deliverable
**Trustless escrow program for token swaps**

### Why This Matters
Escrows power marketplaces, OTC trading, and trust-minimized exchanges.

---

### Week 22: Escrow Program

**What to Build (6.5 hours):**

**Program Flow:**
1. Alice wants to swap Token A for Bob's Token B
2. Alice initializes escrow, deposits Token A
3. Bob accepts, deposits Token B
4. Exchange happens atomically

**Tuesday (2.5 hrs):**
- Read: PaulX escrow tutorial (1 hr)
- Code: Initialize escrow instruction (1.5 hrs)

**Thursday (2 hrs):**
- Code: Exchange instruction (1 hr)
- Code: Cancel instruction (1 hr)

**Weekend (2 hrs):**
- Code: PDA for escrow authority (1 hr)
- Add: Token transfer logic (1 hr)

**Success Criteria:**
- ✅ Escrow initializes correctly
- ✅ Exchange transfers both tokens
- ✅ Cancel refunds to initiator
- ✅ PDA signs transfers

---

### Week 23: Tests + CLI

**What to Build (6.5 hours):**

**Monday (2 hrs):**
- Write: Test for initialize (1 hr)
- Write: Test for exchange (1 hr)

**Wednesday (2.5 hrs):**
- Write: Test for cancel (1 hr)
- Write: Test for edge cases (1.5 hrs)

**Weekend (2 hrs):**
- Build: CLI interface to interact with program (1 hr)
- Deploy: To devnet (30 min)
- Test: Full flow from CLI (30 min)

**Success Criteria:**
- ✅ 100% test coverage
- ✅ CLI can init/exchange/cancel
- ✅ Deployed to devnet
- ✅ README with examples

---

### DSA (Weeks 22-23)
**Week 22:** Join 1 LeetCode contest
**Week 23:** Complete 1 full mock interview

---

## Week 24: NFT Collection on Solana

### 🎯 Deliverable
**Deploy NFT collection using Metaplex Candy Machine**

### Why This Matters
Candy Machine is how 99% of Solana NFTs launch. Industry standard.

### How to Build (6.5 hours)

**Tuesday (2 hrs):**
- Install: Sugar CLI (Candy Machine tool) (30 min)
- Prepare: 10 images + metadata JSON (1 hr)
- Upload: To IPFS/Arweave (30 min)

**Thursday (2.5 hrs):**
- Configure: Candy Machine settings (1 hr)
- Deploy: Candy Machine to devnet (1 hr)
- Test: Mint 3 NFTs (30 min)

**Weekend (2 hrs):**
- Build: Simple minting UI (React) (1.5 hrs)
- Test: Full minting flow (30 min)

### Success Criteria
- ✅ Candy Machine deployed
- ✅ Can mint NFTs via UI
- ✅ NFTs visible on Solana Explorer
- ✅ Metadata on decentralized storage

### DSA Task (2 hrs)
**System Design:** Design Uber/Lyft

---

# Phase 5: Advanced Projects (Weeks 25-28)
*Build production-grade features*

---

## Weeks 25-26: MINI PROJECT - Token Launch (ICO)

### 🎯 Deliverable
**Complete token presale platform on Solana**

### Why This Matters
Token launches raise millions. You're building what Launchpads use.

---

### Week 25: Backend Program

**What to Build (7 hours):**

**Program Features:**
- Accept SOL contributions
- Mint project tokens to contributors
- Vesting schedule (tokens unlock over time)
- Admin controls

**Monday (2 hrs):**
- Design: Program accounts structure (30 min)
- Code: Initialize sale instruction (1.5 hrs)

**Wednesday (2.5 hrs):**
- Code: Contribute SOL instruction (1 hr)
- Code: Mint tokens to contributor (1.5 hrs)

**Weekend (2.5 hrs):**
- Code: Claim tokens instruction (with vesting check) (1.5 hrs)
- Write: Tests (1 hr)

**Success Criteria:**
- ✅ Can contribute SOL
- ✅ Receives tokens proportionally
- ✅ Vesting logic works
- ✅ Tests pass

---

### Week 26: Frontend

**What to Build (7 hours):**

**Features:**
- Connect Solana wallet
- Show sale stats (raised, remaining)
- Contribute form
- Claim tokens button

**Tuesday (2.5 hrs):**
- Setup: React + Wallet Adapter (30 min)
- Code: Connect wallet (30 min)
- Code: Display sale info (1 hr)
- Code: Contribute form (30 min)

**Thursday (2 hrs):**
- Code: Contribute transaction (1 hr)
- Code: Claim tokens transaction (1 hr)

**Weekend (2.5 hrs):**
- Add: Progress bar, countdown timer (1 hr)
- Polish: Mobile responsive (1 hr)
- Deploy: Vercel + devnet (30 min)

**Success Criteria:**
- ✅ Users can contribute SOL
- ✅ Shows allocation
- ✅ Can claim vested tokens
- ✅ Live at public URL

---

### DSA (Weeks 25-26)
**Week 25:** Write 5 STAR behavioral stories
**Week 26:** Solve 3 hard problems

---

## Week 27: Blockchain Indexer

### 🎯 Deliverable
**Index Solana program transactions to PostgreSQL using webhooks**

### Why This Matters
DApps need to query historical data fast. Indexers power every major protocol.

### How to Build (7 hours)

**Tuesday (2.5 hrs):**
- Setup: Helius account + webhook (30 min)
- Setup: Express + PostgreSQL (30 min)
- Code: Webhook endpoint (1 hr)
- Code: Parse transaction data (30 min)

**Thursday (2 hrs):**
- Code: Store in database (1 hr)
- Code: Query endpoints (GET /transactions) (1 hr)

**Weekend (2.5 hrs):**
- Add: Filters (by user, by instruction) (1 hr)
- Test: Generate test transactions (1 hr)
- Deploy: Railway (30 min)

### Success Criteria
- ✅ Captures all program transactions
- ✅ Stores in PostgreSQL
- ✅ API returns filtered results
- ✅ Deployed and running

### DSA Task (2 hrs)
**Solve 10 company-tagged problems**

---

## Week 28: Payment Detection System

### 🎯 Deliverable
**Detect SOL deposits and sweep to cold wallet**

### Why This Matters
Exchanges, marketplaces, and platforms need payment detection. Core infrastructure.

### How to Build (7 hours)

**Monday (2 hrs):**
- Code: Monitor wallet for deposits (1 hr)
- Code: Detect incoming transactions (1 hr)

**Wednesday (2.5 hrs):**
- Code: Confirm transaction finality (1 hr)
- Code: Sweep to cold wallet (1.5 hrs)

**Weekend (2.5 hrs):**
- Add: Database logging (30 min)
- Add: Notification (Discord webhook) (30 min)
- Test: Full flow with test deposits (1 hr)
- Deploy: Keep running with PM2 (30 min)

### Success Criteria
- ✅ Detects deposits within 1 minute
- ✅ Sweeps to cold wallet automatically
- ✅ Logs all transactions
- ✅ Sends notifications

### DSA Task (2 hrs)
**Complete coding + system design mock**

---

# Phase 6: CAPSTONE - DEX Clone (Weeks 29-32)

### 🎯 FINAL GOAL
**Full-featured DEX live on Solana mainnet with real liquidity**

This is your magnum opus. The project that gets you hired.

---

## Week 29: AMM Program

**What to Build (8 hours):**

### Program Architecture
- Liquidity pools (store token pairs)
- Add/remove liquidity
- Swap tokens using x*y=k formula

**Monday (2.5 hrs):**
- Read: Uniswap V2 whitepaper (1 hr)
- Design: Pool account structure (30 min)
- Code: Initialize pool instruction (1 hr)

**Wednesday (2.5 hrs):**
- Code: Add liquidity instruction (1.5 hrs)
- Code: Calculate LP tokens (1 hr)

**Weekend (3 hrs):**
- Code: Remove liquidity instruction (1.5 hrs)
- Write: Tests for liquidity operations (1.5 hrs)

**Success Criteria:**
- ✅ Can create pools
- ✅ Can add/remove liquidity
- ✅ LP tokens minted correctly
- ✅ Tests pass

---

## Week 30: Swap Logic

**What to Build (8 hours):**

### Swap Implementation
- Calculate output amount (x*y=k)
- Execute token swaps
- Collect fees (0.3%)

**Tuesday (2.5 hrs):**
- Code: Calculate swap amounts (1.5 hrs)
- Code: Slippage protection (1 hr)

**Thursday (2.5 hrs):**
- Code: Execute swap instruction (1.5 hrs)
- Code: Fee collection (1 hr)

**Weekend (3 hrs):**
- Write: Tests for swaps (1.5 hrs)
- Test: Price impact calculations (1 hr)
- Deploy: To devnet (30 min)

**Success Criteria:**
- ✅ Swaps work correctly
- ✅ Prices match x*y=k formula
- ✅ Fees collected properly
- ✅ Slippage protection works

---

## Week 31: Frontend

**What to Build (8 hours):**

### UI Features
- Connect Solana wallet
- Create new pool
- Add/remove liquidity
- Swap tokens
- Show pool stats

**Monday (2.5 hrs):**
- Setup: Next.js + Wallet Adapter (30 min)
- Code: Pool creation UI (1 hr)
- Code: Connect to program (1 hr)

**Wednesday (2.5 hrs):**
- Code: Add liquidity form (1.5 hrs)
- Code: Remove liquidity form (1 hr)

**Weekend (3 hrs):**
- Code: Swap interface (1.5 hrs)
- Code: Real-time pool stats (1 hr)
- Polish: Mobile responsive (30 min)

**Success Criteria:**
- ✅ Full UI for all operations
- ✅ Real-time data updates
- ✅ Mobile works
- ✅ Error handling

---

## Week 32: Production Launch

**What to Build (8 hours):**

### Ship to Mainnet
- Audit checklist
- Deploy to mainnet
- Monitoring
- Marketing

**Monday (2 hrs):**
- Security: Run through checklist (1 hr)
- Code: Add rate limiting (30 min)
- Code: Add transaction retry logic (30 min)

**Wednesday (3 hrs):**
- Deploy: Program to mainnet (1 hr)
- Deploy: Frontend to Vercel (30 min)
- Setup: Monitoring (Sentry, analytics) (1 hr)
- Setup: CI/CD pipeline (30 min)

**Weekend (3 hrs):**
- Document: Complete README, architecture diagram (1.5 hrs)
- Record: 3-min demo video (30 min)
- Launch: Tweet, Product Hunt, Discord announcements (1 hr)

**Success Criteria:**
- ✅ Live on mainnet
- ✅ At least $100 in liquidity (seed yourself)
- ✅ First swap executed
- ✅ Monitoring active
- ✅ Complete documentation
- ✅ Public announcement

---

### DSA (Weeks 29-32)
**Week 29:** Update resume
**Week 30:** Network on Twitter
**Week 31:** Revise patterns
**Week 32:** Edge case review

---

# Phase 7: Job Search (Weeks 33-36)

---

## Weeks 33-34: CAPSTONE - NFT Marketplace

**Build while applying to jobs**

---

### Week 33: Contracts

**What to Build (8 hours):**

**Marketplace Features:**
- List NFT for sale
- Buy NFT
- Make offer (bid)
- Accept/reject offers
- Royalties

**Monday (2.5 hrs):**
- Code: List NFT instruction (1.5 hrs)
- Code: Delist instruction (1 hr)

**Wednesday (2.5 hrs):**
- Code: Buy NFT instruction (1.5 hrs)
- Code: Royalty calculation (1 hr)

**Weekend (3 hrs):**
- Code: Bid system (make, accept, reject) (2 hrs)
- Write: Tests (1 hr)

**+ Career (2 hours):**
Apply to 15 companies this week

---

### Week 34: Frontend

**What to Build (8 hours):**

**UI Features:**
- NFT gallery (all listings)
- NFT detail page
- List your NFT
- Make offer
- Accept offer

**Tuesday (2.5 hrs):**
- Code: Gallery with filters (1.5 hrs)
- Code: NFT detail page (1 hr)

**Thursday (2.5 hrs):**
- Code: List NFT form (1 hr)
- Code: Buy button (1 hr)
- Code: Offer system (30 min)

**Weekend (3 hrs):**
- Polish: Images, loading states (1 hr)
- Deploy: Vercel (30 min)
- Test: Full buying flow (1.5 hrs)

**+ Career (2 hours):**
Prepare for scheduled interviews

---

## Week 35: Open Source + Negotiation

### 🎯 Deliverable
**3 PRs to Solana ecosystem projects**

**What to Do (6 hours):**

**Monday (2 hrs):**
- Find: Good first issues in Anchor, Metaplex (1 hr)
- Clone: Repo, set up locally (1 hr)

**Wednesday (2 hrs):**
- Fix: First issue, submit PR (2 hrs)

**Weekend (2 hrs):**
- Fix: 2 more issues/improvements (2 hrs)

**+ Career (2 hours):**
Evaluate offers, negotiate salary

---

## Week 36: Final Polish

### 🎯 Deliverable
**Portfolio site with all projects, demos, docs**

**What to Do (6 hours):**

**Tuesday (2 hrs):**
- Create: Portfolio site (Next.js) (1 hr)
- Add: All project links (30 min)
- Add: About section (30 min)

**Thursday (2 hrs):**
- Record: Demo video for each major project (2 hrs)

**Weekend (2 hrs):**
- Polish: READMEs for all projects (1 hr)
- Add: Architecture diagrams (1 hr)

**🎉 Prepare for your new Web3 job!**

---

# Quick Reference

## Time Estimates Per Week
- **Phase 1-3 (Weeks 1-16):** 8-9 hrs/week
- **Phase 4 (Weeks 17-24):** 8.5-9 hrs/week  
- **Phase 5-6 (Weeks 25-32):** 9-10 hrs/week
- **Phase 7 (Weeks 33-36):** 10 hrs/week

## Major Projects Summary
1. **CLI Wallet** (Weeks 3-4)
2. **Portfolio Tracker** (Weeks 9-10)
3. **NFT Minting DApp** (Weeks 14-15)
4. **Solana Escrow** (Weeks 22-23)
5. **Token Launch** (Weeks 25-26)
6. **DEX Clone** (Weeks 29-32) - MAINNET
7. **NFT Marketplace** (Weeks 33-34)

## Success Metrics
- ✅ 7 complete projects
- ✅ 2 mainnet deployments
- ✅ 100+ GitHub commits
- ✅ Production-ready code
- ✅ Job offer(s) by Week 36

---

**Start Week 1 tomorrow. Ship something every week. Get hired in 8 months.** 🚀
