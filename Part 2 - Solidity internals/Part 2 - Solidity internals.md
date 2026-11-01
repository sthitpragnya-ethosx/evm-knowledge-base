---
title: "Part 2 - Solidity internals"
part: 2
aliases: ["Solidity internals", "Part 2"]
tags: [evm, solidity, part-2, moc]
status: expanded
updated: 2026-10-30
---

# Part 2 — Solidity internals

How Solidity maps onto the EVM: types, data locations, storage and memory layout, the ABI, functions and dispatch, inheritance and libraries, errors, arithmetic, and what changed across 0.8.x. Plan weeks 1 and 4. Every claim was checked by compiling with solc 0.8.38 (legacy and via-IR) and running the bytecode in py-evm.

## Reading order

| # | Note | What you learn |
| --- | --- | --- |
| 1 | [[2.1 Types]] | Value vs reference types, UDVTs and operators, function types at runtime, enums, literals, dirty bits |
| 2 | [[2.2 Data locations]] | Copy vs alias rules, calldata vs memory gas, dangling storage pointers, `delete` per type, transient variables |
| 3 | [[2.3 Storage layout]] | Packing, mapping and array slot formulas, short/long strings, ERC-7201, `layout at`, upgrade-safe changes |
| 4 | [[2.4 Memory layout]] | Reserved words, array/bytes/struct layout, nested pointers, bump allocation, via-IR spilling and `memory-safe` |
| 5 | [[2.5 ABI encoding and decoding]] | Selectors, head/tail encoding, packed collisions, what the decoder checks, selector collisions |
| 6 | [[2.6 Functions, modifiers and special functions]] | Visibility, mutability, modifiers, receive/fallback matrix, getters, the dispatcher |
| 7 | [[2.7 Inheritance, interfaces and libraries]] | C3 linearization, `super`, constructor order, library linking and call protection |
| 8 | [[2.8 Errors and reverts]] | require/revert/assert, custom errors, every Panic code, try/catch limits, bubbling, forged errors |
| 9 | [[2.9 Arithmetic and numeric pitfalls]] | Checked vs unchecked, signed division, downcasts, 512-bit `mulDiv`, rounding proofs |
| 10 | [[2.10 Version map of 0.8.x features]] | Every 0.8.x release, default `evmVersion` history, known bugs, how to decide a compiler upgrade |

## Facts that hold across Part 2

| Fact | Value | Where |
| --- | --- | --- |
| Default `evmVersion` in 0.8.38 | `osaka` (since 0.8.31); `amsterdam` needs experimental mode | [[2.10 Version map of 0.8.x features]] |
| Enum size | Always `uint8`; more than 256 members is a compile error | [[2.1 Types]] |
| ABI decoder failures | Empty revert, never a Panic; trailing bytes accepted | [[2.5 ABI encoding and decoding]] |
| Calldata vs memory params | `external` and `public` produce identical code for `calldata` params | [[2.6 Functions, modifiers and special functions]] |
| Dispatcher | Legacy splits into a tree when runs × 6 × (n − 4) > 3,400; via-IR never splits | [[2.6 Functions, modifiers and special functions]] |
| State-variable init order | Differs between pipelines: the docs example gives `y` = 0 on legacy, 42 on via-IR; switching pipelines needs a re-test | [[2.7 Inheritance, interfaces and libraries]] |
| `delete` on a struct with a mapping | Compiles without warning; mapping entries survive | [[2.2 Data locations]] |
| ERC-7201 slot of `example.main` | `0x183a…b500`; `erc7201()` builtin since 0.8.35 | [[2.3 Storage layout]] |
| `layout at` | Rejects `keccak256(...)`; use `erc7201()` or a literal | [[2.3 Storage layout]] |
| Auto unchecked `++i` | Since 0.8.22, only for `i < n` style loops with `++i`/`i++` | [[2.9 Arithmetic and numeric pitfalls]] |
| `mulDiv` inverse | Seed correct to 5 bits, 6 Newton steps reach 256 | [[2.9 Arithmetic and numeric pitfalls]] |

## Related parts

- Part 1 — EVM foundations: [[1.4 Memory]], [[1.5 Storage and transient storage]], [[1.6 Calldata, returndata and logs]]
- Part 3 — Yul and assembly: [[3.4 Interop with Solidity]], [[3.8 ABI, errors and events by hand]], [[3.9 Cleanup, dirty bits and safety]], [[3.11 Compiler IR and the optimizer]]
- Part 5 — Security: [[5.4 Arithmetic, rounding and precision]], [[5.9 Proxies, delegatecall and storage]], [[5.13 Toolchain, dependencies and operations]]
