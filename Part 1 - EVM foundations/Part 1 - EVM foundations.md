---
title: "Part 1 - EVM foundations"
part: 1
aliases: ["EVM foundations", "Part 1"]
tags: [evm, part-1, moc]
status: expanded
updated: 2026-10-30
---

# Part 1 — EVM foundations

The machine underneath Solidity: accounts, transactions, the execution model, the three data areas (memory, storage, calldata and returndata), calls, contract creation, gas, block context and the forks that changed all of it. Plan weeks 1–2.

> [!abstract] State of mainnet as of 30 Oct 2026
> - Mainnet runs the **Osaka** EVM (Fusaka, 3 Dec 2025). Block gas limit 60,000,000. Per-transaction gas cap 16,777,216 (2^24, EIP-7825).
> - Solidity **0.8.38** is the latest compiler. Its default `evmVersion` is `osaka`. The `amsterdam` target exists but is experimental (needs `settings.experimental`).
> - **Glamsterdam** (Amsterdam EVM) is live on Sepolia (6 Oct 2026). Its Hoodi activation (26 Oct 2026) was cancelled on 22 Oct 2026 while developers study how EIP-8037 interacts with deployed contracts (details in [[1.11 Hard forks that changed contract code]]). It has no mainnet date. Its EIPs are "Scheduled for Inclusion" in EIP-7773 and in Last Call. These notes call them *scheduled*, never live.

## Reading order

| # | Note | What you learn | Key EIPs |
| --- | --- | --- | --- |
| 1 | [[1.1 Accounts and world state]] | The four account fields, EOA vs contract vs 7702 account, EXTCODEHASH, tries and storage proofs | 161, 1052, 1186, 2681, 7702 |
| 2 | [[1.2 Transactions and fees]] | Transaction types 0–4, EIP-1559 fees, intrinsic gas, calldata floor, blobs, gas cap | 155, 1559, 2930, 4844, 7623, 7702, 7825 |
| 3 | [[1.3 Execution model]] | Stack machine, jumpdest analysis, halts, stack too deep, a compiled function traced | 150, 8024 |
| 4 | [[1.4 Memory]] | Expansion cost, MCOPY vs identity vs loops, `msize`, how much memory a frame can touch | 5656 |
| 5 | [[1.5 Storage and transient storage]] | SSTORE cost and refund table, sentry rule, transient storage semantics | 1153, 2200, 2929, 3529 |
| 6 | [[1.6 Calldata, returndata and logs]] | Calldata padding, the returndata buffer, LOG costs, indexed topics, short-address attack | 211 |
| 7 | [[1.7 Message calls and precompiles]] | CALL / DELEGATECALL / STATICCALL, call gas, stipend, all 18 precompiles | 150, 2537, 7951 |
| 8 | [[1.8 Contract creation]] | Initcode vs runtime, CREATE / CREATE2 / CREATE3, immutables, size limits | 170, 3541, 3860, 6780, 7954 |
| 9 | [[1.9 Gas accounting]] | Gas lifecycle, access sets, ERC-20 transfer gas line by line, proxy costs, repricing history | 150, 1884, 2929, 3529 |
| 10 | [[1.10 Block and chain context]] | Every `block.*` value, PREVRANDAO bias, EIP-2935 history, L2 differences, randomness design | 2935, 4399, 7843 |
| 11 | [[1.11 Hard forks that changed contract code]] | Every fork's contract-facing changes, `evmVersion` map, multi-chain opcode support | 7607, 7773 |

## How to study a note

1. Read the "In one minute" box, then the body.
2. Work through the question ladder with answers folded. Answer out loud, then open the callout and score yourself 0–3.
3. A note is done when you can answer its E questions without opening them.

## Facts that hold across Part 1

| Fact | Value | Where |
| --- | --- | --- |
| Cold account access / cold slot / warm access | 2,600 / 2,100 / 100 gas | [[1.9 Gas accounting]] |
| SSTORE clean zero → nonzero / clean nonzero change | 20,000 / 2,900 gas (+2,100 if cold) | [[1.5 Storage and transient storage]] |
| Refund cap | One fifth of gas used (EIP-3529) | [[1.5 Storage and transient storage]] |
| Refunds vs calldata floor | Refunds are subtracted first, then the EIP-7623 floor applies | [[1.2 Transactions and fees]] |
| Pre-warmed at transaction start | `tx.origin`, `tx.to`, precompiles, coinbase, access-list entries, 7702 authorities | [[1.9 Gas accounting]] |
| Memory expansion | 3 × words + words² / 512 | [[1.4 Memory]] |
| Code size / initcode limits | 24,576 / 49,152 bytes (EIP-7954 would raise to 65,536 / 131,072) | [[1.8 Contract creation]] |
| 7702 account code | `0xef0100 ‖ address`, 23 bytes; EXTCODESIZE returns 23 | [[1.1 Accounts and world state]] |
| `block.timestamp` after the Merge | Exactly `genesis_time + 12 × slot`; a proposer can only skip its slot | [[1.10 Block and chain context]] |
| `msize` in inline assembly | Compile error when the Yul optimizer is on (0.8.38) | [[1.4 Memory]] |

## Related parts

- Part 2 — Solidity internals: [[2.3 Storage layout]], [[2.4 Memory layout]], [[2.5 ABI encoding and decoding]]
- Part 3 — Yul and assembly: [[3.3 Opcodes by family]], [[3.5 Memory management in assembly]], [[3.7 External calls in assembly]]
- Part 5 — Security: [[5.11 New attack surfaces - transient storage, EIP-7702, account abstraction]], [[5.7 Denial of service and griefing]]
