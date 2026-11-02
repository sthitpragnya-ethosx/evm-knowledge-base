---
title: "Part 3 - Yul and assembly"
part: 3
aliases: ["Yul and assembly", "Part 3", "Yul and inline assembly"]
tags: [evm, solidity, yul, part-3, moc]
status: expanded
updated: 2026-11-02
---

# Part 3 — Yul and assembly

Yul from zero: what it is, the language, every opcode family, how it talks to Solidity, memory, storage, calls, hand-written ABI, cleanup, standalone objects, the optimizer, and what lies below Yul. Read in order; each note assumes the ones before it. Plan weeks 2–4. Every claim was checked by compiling with solc 0.8.38 (legacy and via-IR) and running the bytecode in py-evm (Prague rules); gas figures are execution gas and state their settings.

## Reading order

| # | Note | What you learn |
| --- | --- | --- |
| 1 | [[3.1 What Yul is and when to use it]] | Yul's two jobs, both pipelines, what assembly turns off, how each optimizer treats it, when it pays (measured), keeping it reviewable |
| 2 | [[3.2 Syntax and control flow]] | Literals, `let`, scope and no-shadowing, `if`/`switch`/`for`, functions and `leave`, right-to-left evaluation, how control flow becomes jumps |
| 3 | [[3.3 Opcodes by family]] | Every builtin with stack effect, gas and edge cases; signed vs unsigned; `byte`, `signextend`, `addmod`/`mulmod`; MSB with binary search vs `clz` |
| 4 | [[3.4 Interop with Solidity]] | `.slot`, `.offset`, `.length`, memory pointers, named returns, `return` vs `leave`, ERC-7201 accessors, the `memory-safe` contract |
| 5 | [[3.5 Memory management in assembly]] | Free pointer, allocation and alignment, scratch space, the zero slot, returning dynamic data, an in-place string builder |
| 6 | [[3.6 Storage, transient storage and bit packing]] | Mapping and array slots by hand, masked read-modify-write, namespaced transient slots, reentrancy locks, 2^16-flag bitmaps and popcount |
| 7 | [[3.7 External calls in assembly]] | `call`/`staticcall`/`delegatecall`, bubbling reverts, safe ERC-20 transfers, returndata bombs, 63/64 griefing, multicall, `create2` |
| 8 | [[3.8 ABI, errors and events by hand]] | Selectors and custom errors by hand, `Error`/`Panic`, `log0`–`log4`, validating calldata decoders, hand-written encoders |
| 9 | [[3.9 Cleanup, dirty bits and safety]] | What Solidity assumes about narrow types, dirty `bool`/address/`uint8` experiments, cleanup bugs from bugs.json, differential testing, an audit checklist |
| 10 | [[3.10 Yul objects and standalone contracts]] | Object structure, `datasize`/`dataoffset`/`datacopy`, immutables, constructor args, a full Yul ERC-20 vs OpenZeppelin and Solady |
| 11 | [[3.11 Compiler IR and the optimizer]] | Legacy vs via-IR, optimizer steps, `runs`, reading `irOptimized`, the stack-to-memory mover, the 0.8.37 and 0.8.38 optimizer bugs |
| 12 | [[3.12 Beyond Yul - Huff and raw bytecode]] | Huff (huff-neo), EIP-1167 opcode by opcode, PUSH0 clones, raw CREATE2 factories, verifying non-Solidity bytecode |

## Facts that hold across Part 3

| Fact | Value | Where |
| --- | --- | --- |
| Shadowing | Forbidden everywhere in Yul; functions are hoisted and see no outer variables | [[3.2 Syntax and control flow]] |
| Argument evaluation | Right to left (both pipelines and standalone Yul) | [[3.2 Syntax and control flow]] |
| Division by zero | `div`, `sdiv`, `mod`, `smod`, `addmod`, `mulmod` all return 0; `sdiv(-2^255, -1) = -2^255` | [[3.3 Opcodes by family]] |
| `clz` | Builtin since 0.8.31, opcode `0x1e`, 5 gas, `clz(0) = 256`; compile error on targets before `osaka` | [[3.3 Opcodes by family]] |
| `return` opcode in an internal function | Ends the whole call: skips the caller's code and post-`_` modifier code (a storage `nonReentrant` lock stays set) | [[3.4 Interop with Solidity]] |
| `memory-safe` | One unannotated memory-touching block disables via-IR's stack-to-memory mover for the code object it is compiled into (creation and runtime code are tracked separately) | [[3.4 Interop with Solidity]], [[3.11 Compiler IR and the optimizer]] |
| Free pointer | Starts at `0x80` (higher on via-IR when spilling); memory above it is dirty; legacy leaves it unaligned after `abi.encodePacked`/`bytes.concat`/`string.concat` | [[3.5 Memory management in assembly]] |
| Transient keyword vs `tstore` | They share one transient slot space: a `transient` state variable at slot 0 collides with `tstore(0, …)` | [[3.6 Storage, transient storage and bit packing]] |
| Calls to no-code addresses | `call`, `staticcall`, `delegatecall` all succeed with empty returndata | [[3.7 External calls in assembly]] |
| Output buffer | Not touched beyond the returned length: check `returndatasize` before trusting `mload(out)` | [[3.7 External calls in assembly]] |
| Negative inner offsets | `bytes[] calldata` accepts a negative inner offset (signed comparison); memory params and `abi.decode` reject it | [[3.8 ABI, errors and events by hand]] |
| Dirty `bool` | In 0.8.38 Solidity cleans it in `if`, `==`, `!`, `&&`, encoding and storage; only assembly reads see the raw word | [[3.9 Cleanup, dirty bits and safety]] |
| Yul ERC-20 | 851-byte runtime vs 1,764 (OpenZeppelin); first transfer 29,468 vs 29,981 gas | [[3.10 Yul objects and standalone contracts]] |
| Huff | huff-neo (`hnc`) is the maintained compiler; huff-rs is deprecated; no explorer verifies Huff | [[3.12 Beyond Yul - Huff and raw bytecode]] |

## Related parts

- Part 1 — EVM foundations: [[1.3 Execution model]], [[1.4 Memory]], [[1.5 Storage and transient storage]], [[1.7 Message calls and precompiles]], [[1.8 Contract creation]]
- Part 2 — Solidity internals: [[2.4 Memory layout]], [[2.5 ABI encoding and decoding]], [[2.8 Errors and reverts]], [[2.10 Version map of 0.8.x features]]
- Part 4 — Architecture and patterns: [[4.2 Upgradeability and proxies]], [[4.10 Gas optimization]]
- Part 5 — Security: [[5.2 Reentrancy]], [[5.7 Denial of service and griefing]], [[5.11 New attack surfaces - transient storage, EIP-7702, account abstraction]], [[5.13 Toolchain, dependencies and operations]]
