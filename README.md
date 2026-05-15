# Challenge: DEX
> Build a constant-product AMM that swaps ETH ↔ ERC-20 with 0.3% fees accruing to liquidity providers

[![Solidity](https://img.shields.io/badge/Solidity-0.8.20-363636?logo=solidity&logoColor=white)]()
[![Foundry](https://img.shields.io/badge/Built_with-Foundry-red)]()
[![Next.js](https://img.shields.io/badge/Frontend-Next.js-black?logo=next.js&logoColor=white)]()
[![Sepolia](https://img.shields.io/badge/Network-Sepolia-8A2BE2)]()

🔗 [Live Demo](https://nextjs-pis0xfpy6-yuchenzhou1031-6631s-projects.vercel.app/) · 📋 [Speedrun Ethereum](https://speedrunethereum.com)

## What It Does

A fully on-chain AMM allowing users to swap ETH for Balloons (ERC-20) and back, add liquidity to earn a share of swap fees, and remove liquidity to reclaim proportional ETH and tokens. Price is determined algorithmically by the constant product formula — no order book, no intermediary.

## Real-World Relevance

- **Uniswap V2** — uses the same `x·y=k` formula and 0.3% fee structure to process billions in daily trading volume; the `price()` function implemented here is functionally identical to Uniswap V2's core swap math
- **Liquidity providing and impermanent loss** — adding liquidity here mints LP shares exactly as real DEXs do; the proportional share mechanism is how LPs earn fees and how impermanent loss occurs when prices shift
- **On-chain price oracles** — AMM reserves are the basis of Uniswap V2 TWAPs; the arbitrage that keeps DEX prices aligned with the market is the same mechanism that makes AMM reserves trustworthy as price feeds

## Contract Architecture

| Contract | Role |
|---|---|
| `Balloons.sol` | ERC-20 token (provided); mints 1000 BAL to deployer; used as the pool asset |
| `DEX.sol` | AMM with `init`, `ethToToken`, `tokenToEth`, `deposit`, `withdraw`; tracks `totalLiquidity` and per-address LP shares |

## Key Concepts

- **Constant product with 0.3% fee** — `yOut = (yRes · xIn · 997) / (xRes · 1000 + xIn · 997)`; the `997/1000` factor embeds the fee without a separate accounting step
- **Pre-deposit ETH reserve** — `ethToToken` uses `address(this).balance - msg.value` as the reserve to get the pool size *before* the incoming ETH, preventing the swap from pricing against itself
- **Proportional LP share minting** — `deposit` mints `msg.value * totalLiquidity / ethReserve` shares; this keeps each LP's ownership percentage constant regardless of when they enter the pool

## Local Setup

```bash
yarn chain    # start local Anvil blockchain
yarn deploy   # deploy Balloons + DEX with seeded liquidity
yarn start    # frontend at http://localhost:3000
```