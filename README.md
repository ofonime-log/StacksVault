# StacksVault - Decentralized Asset Management Protocol

[![Built with Clarity](https://img.shields.io/badge/Built%20with-Clarity-blue)](https://clarity-lang.org/)
[![Stacks L2](https://img.shields.io/badge/Stacks-Layer%202-orange)](https://www.stacks.co/)

A non-custodial, governance-driven fund management system built on Stacks L2, enabling secure Bitcoin-native DeFi operations through transparent proposal mechanisms and collective asset control.

## Overview

StacksVault is a sophisticated DeFi protocol that combines Bitcoin's security with Stacks L2 scalability to create an institutional-grade asset management solution. The protocol enables collective control of digital assets through a transparent governance system while maintaining non-custodial principles and Bitcoin-native compliance.

## Key Features

### Core Functionality

- **Trustless Asset Pooling**
  - Secure STX deposits with time-locked withdrawals
  - 1:1 minting/burning of governance tokens
  - Minimum deposit requirements (1 STX)

### Governance Engine

- **Proposal Lifecycle Management**
  - Creation with description, amount, and target
  - Configurable voting durations (1-14 days)
  - Weighted voting based on token holdings
  - Timelock-protected execution

### Security Architecture

- **Bitcoin-finalized Transactions**
  - STX-native operations
  - Anti-frontrunning block-height locking
  - Multi-layer validation checks
  - Withdrawal cooldown periods

### Compliance Features

- **Transparent Accounting**
  - On-chain audit trails
  - SIP-009 compatible interfaces
  - Immutable proposal history
  - Public vote records

## Technical Specifications

### Contract Architecture

- **Language**: Clarity (version 2.1)
- **Standard**: SIP-009 (NFT-compatible structure)
- **Constants**:
  - Minimum Duration: 144 blocks (~1 day)
  - Maximum Duration: 20,160 blocks (~14 days)
  - Lock Period: 1,440 blocks (~10 days)
  - Minimum Deposit: 1,000,000 µSTX (1 STX)

### Data Structures

- **Balances**: Principal → uint
- **Deposits**: Principal → {amount, lock-until, last-reward-block}
- **Proposals**: uint → {proposer, description, amount, target, expires-at, executed, votes}
- **Votes**: (proposal-id, voter) → bool

## Smart Contract Functions

### User Operations

| Function   | Parameters     | Description                                 |
| ---------- | -------------- | ------------------------------------------- |
| `deposit`  | `amount: uint` | Lock STX to mint governance tokens          |
| `withdraw` | `amount: uint` | Burn tokens to unlock STX after lock period |

### Governance Operations

| Function           | Parameters                              | Description               |
| ------------------ | --------------------------------------- | ------------------------- |
| `create-proposal`  | `description, amount, target, duration` | Initiate funding proposal |
| `vote`             | `proposal-id, vote-for`                 | Cast weighted vote        |
| `execute-proposal` | `proposal-id`                           | Execute approved proposal |

### Read Operations

| Function       | Returns         | Description              |
| -------------- | --------------- | ------------------------ |
| `get-balance`  | uint            | User's governance tokens |
| `get-proposal` | proposal struct | Full proposal details    |
| `get-vote`     | bool            | Individual voting record |

## Error Codes

| Code | Description              | Resolution                      |
| ---- | ------------------------ | ------------------------------- |
| u100 | Owner-only function      | Verify sender is contract owner |
| u101 | Contract not initialized | Call `initialize` first         |
| u103 | Insufficient balance     | Check token balance             |
| u107 | Expired proposal         | Check block height              |
| u110 | Funds locked             | Wait until lock period ends     |

## Security Features

### Protocol Safeguards

1. **Time-locked Withdrawals**

   - 10-day cooling period after deposit
   - Block-height based locking

2. **Governance Protections**

   - Minimum 50%+1 vote differential
   - Execution deadline enforcement
   - Anti-reentrancy patterns

3. **Transaction Security**
   - STX-native transfers (no wrapping)
   - Balance checks before state changes
   - Proposal amount validation

## Usage Examples

### Deposit STX

```clarity
(contract-call? .stacks-vault deposit u1000000)
```

### Create Funding Proposal

```clarity
(contract-call? .stacks-vault create-proposal
  "Marketing campaign"
  u5000000
  SP3ABC...
  u10080) ;; 7 days
```

### Vote on Proposal

```clarity
(contract-call? .stacks-vault vote u42 true)
```

### Execute Approved Proposal

```clarity
(contract-call? .stacks-vault execute-proposal u42)
```

### Check Balance

```clarity
(contract-call? .stacks-vault get-balance tx-sender)
```

## Operational Considerations

### Governance Parameters

- **Voting Power**: Proportional to deposited STX
- **Quorum**: Simple majority (yes > no)
- **Timing**:
  - Minimum proposal duration: 1 day
  - Maximum proposal duration: 14 days

### Economic Model

- **Tokenomics**: 1:1 backed by STX
- **Fees**: Native STX transfer costs only
- **Slashing**: No penalty system implemented

## Audit & Verification

### Security Recommendations

1. **Formal Verification**

   - Validate contract invariants
   - Check reentrancy possibilities

2. **Test Coverage**

   - 100% branch coverage
   - Edge case testing for proposals

3. **Monitoring**
   - Track proposal success rates
   - Monitor withdrawal patterns
