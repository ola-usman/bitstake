# BitStake Protocol Documentation

## Overview

A Bitcoin-aligned DeFi primitive offering non-custodial STX staking with tiered rewards and on-chain governance, built on Stacks Layer 2. Combines yield generation with decentralized decision-making while maintaining Bitcoin-level security guarantees.

## Key Components

### 1. Core Protocol Mechanics

- STX Staking Engine
- Tiered Reward Distribution (1-3)
- Time-Lock Multipliers (1x-1.5x)
- Governance Voting System
- Emergency Safeguards

### 2. Technical Architecture

**Token System:**

- ANALYTICS-TOKEN (Fungible Token, Decimals: 0)
- STX Pool Management

**State Management:**

```clarity
Data Variables:
- contract-paused : Bool
- emergency-mode : Bool
- stx-pool : UInt
- base-reward-rate : UInt (500 = 5%)
- bonus-rate : UInt (100 = 1%)
- minimum-stake : UInt (1,000,000 uSTX)
- cooldown-period : UInt (1440 blocks)
- proposal-count : UInt

Data Maps:
- Proposals : Stores governance proposals
- UserPositions : Tracks user staking positions
- StakingPositions : Manages active stakes
- TierLevels : Configures reward tiers
```

## Core Functions

### Staking Operations

**`stake-stx`**  
Executes STX deposit with optional time-lock  
Params:

- `amount` (uSTX, min 1M)
- `lock-period` (0/4320/8640 blocks)

Mechanics:

1. STX transfer to contract
2. Tier calculation (1-3)
3. Reward multiplier application
4. Position update

**`initiate-unstake`**  
Begins withdrawal process with cooldown  
Params:

- `amount` (uSTX)

**`complete-unstake`**  
Finalizes withdrawal after cooldown

### Governance System

**Proposal Lifecycle:**

1. `create-proposal`

   - Requires 1M+ voting power
   - 100-2880 block voting period

2. `vote-on-proposal`

   - Weighted by voting power
   - Active proposals only

3. Execution
   - Automatic when thresholds met

### Administrative Functions

- `pause-contract` (Owner-only)
- `resume-contract` (Owner-only)
- `initialize-contract` (One-time setup)

## Reward Mechanics

**Calculation Formula:**

```
Rewards = (StakedAmount * BaseRate * Multiplier * BlocksStaked) / 14,400,000
```

Multipliers:

- Tier 1: 1x | Tier 2: 1.5x | Tier 3: 2x
- Lock Period: +25% (1M), +50% (2M)

## Security Model

**Protection Mechanisms:**

- Cooldown withdrawals (1440 blocks)
- Circuit breaker pattern
- Tiered access control
- Input validation layers

**Error Codes:**

- 1000: Unauthorized access
- 1001: Invalid parameters
- 1002: Incorrect amounts
- 1003: Insufficient balance
- 1004: Cooldown active
- 1005: No stake found
- 1006: Below minimum
- 1007: Contract paused

## Governance Parameters

**Proposal Requirements:**

- Minimum description: 10 chars
- Maximum description: 256 chars
- Voting period: 100-2880 blocks
- Minimum votes: 1M voting power

## Development Guide

### Requirements

- Clarinet 2.0+
- Stacks.js 6.x
- Testnet STX

### Audit Considerations

1. Verify tier thresholds
2. Test reward calculations
3. Validate cooldown enforcement
4. Check governance thresholds
5. Verify emergency pause functionality
