---
title: Home
aliases: ["Index", "Vault home"]
tags: [moc]
updated: 2026-11-02
---

# EVM, Solidity and Yul — vault home

A guide from basics to expert level, written for relearning Solidity after a break and learning Yul from zero. Six parts, 66 notes. Each note teaches the topic, then ends with a question ladder (**B** basic · **I** intermediate · **A** advanced · **E** expert) with folded worked answers.

> [!info] Status
> Part 1 is expanded (29 Oct 2026). Part 2 is expanded (30 Oct 2026). Part 3 is expanded (2 Nov 2026). Parts 4–6 are still outline only; their links are placeholders until each note is written. The original outline and interview round live in the Claude Doc "Expert Solidity Interview Questions & Learning Plan".


## Part 1 — EVM foundations

Index: [[Part 1 - EVM foundations]]

- [[1.1 Accounts and world state]] — expanded
- [[1.2 Transactions and fees]] — expanded
- [[1.3 Execution model]] — expanded
- [[1.4 Memory]] — expanded
- [[1.5 Storage and transient storage]] — expanded
- [[1.6 Calldata, returndata and logs]] — expanded
- [[1.7 Message calls and precompiles]] — expanded
- [[1.8 Contract creation]] — expanded
- [[1.9 Gas accounting]] — expanded
- [[1.10 Block and chain context]] — expanded
- [[1.11 Hard forks that changed contract code]] — expanded

## Part 2 — Solidity internals

Index: [[Part 2 - Solidity internals]]

- [[2.1 Types]] — expanded
- [[2.2 Data locations]] — expanded
- [[2.3 Storage layout]] — expanded
- [[2.4 Memory layout]] — expanded
- [[2.5 ABI encoding and decoding]] — expanded
- [[2.6 Functions, modifiers and special functions]] — expanded
- [[2.7 Inheritance, interfaces and libraries]] — expanded
- [[2.8 Errors and reverts]] — expanded
- [[2.9 Arithmetic and numeric pitfalls]] — expanded
- [[2.10 Version map of 0.8.x features]] — expanded

## Part 3 — Yul and assembly

Index: [[Part 3 - Yul and assembly]]

- [[3.1 What Yul is and when to use it]] — expanded
- [[3.2 Syntax and control flow]] — expanded
- [[3.3 Opcodes by family]] — expanded
- [[3.4 Interop with Solidity]] — expanded
- [[3.5 Memory management in assembly]] — expanded
- [[3.6 Storage, transient storage and bit packing]] — expanded
- [[3.7 External calls in assembly]] — expanded
- [[3.8 ABI, errors and events by hand]] — expanded
- [[3.9 Cleanup, dirty bits and safety]] — expanded
- [[3.10 Yul objects and standalone contracts]] — expanded
- [[3.11 Compiler IR and the optimizer]] — expanded
- [[3.12 Beyond Yul - Huff and raw bytecode]] — expanded

## Part 4 — Architecture and patterns

- [[4.1 Access control and governance]]
- [[4.2 Upgradeability and proxies]]
- [[4.3 Deterministic and cross-chain deployment]]
- [[4.4 Token standards]]
- [[4.5 Account abstraction and smart wallets]]
- [[4.6 On-chain cryptography]]
- [[4.7 Oracles and off-chain data]]
- [[4.8 L2s and cross-chain]]
- [[4.9 DeFi math and primitives]]
- [[4.10 Gas optimization]]

## Part 5 — Security

- [[5.1 Threat modeling and invariants]]
- [[5.2 Reentrancy]]
- [[5.3 Access control and initialization]]
- [[5.4 Arithmetic, rounding and precision]]
- [[5.5 Price manipulation and flash loans]]
- [[5.6 MEV and transaction ordering]]
- [[5.7 Denial of service and griefing]]
- [[5.8 Signatures and replay]]
- [[5.9 Proxies, delegatecall and storage]]
- [[5.10 Token integration]]
- [[5.11 New attack surfaces - transient storage, EIP-7702, account abstraction]]
- [[5.12 Cross-chain and bridges]]
- [[5.13 Toolchain, dependencies and operations]]
- [[5.14 Case studies]]

## Part 6 — Tooling and operations

- [[6.1 Foundry essentials]]
- [[6.2 Unit, fuzz and invariant testing]]
- [[6.3 Static analysis]]
- [[6.4 Property-based fuzzers]]
- [[6.5 Formal verification]]
- [[6.6 Debugging and tracing]]
- [[6.7 Deployment and verification]]
- [[6.8 Operations and incident response]]
- [[6.9 Audit process]]

## Conventions

- One note per subtopic, named `N.M Title`. Links use the exact note name.
- Tags: `eip/<n>`, `opcode/<name>`, `fork/<name>`, `topic/<name>`. Use the tag pane to see every note touching, say, `eip/7702`.
- Glamsterdam EIPs are described as *scheduled*, never live, until mainnet activation.
- Code snippets compile with solc 0.8.38 unless marked otherwise.
