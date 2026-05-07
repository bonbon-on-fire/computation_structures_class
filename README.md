# Computation Structures Class 2026

## Overview

### What I Built

I completed all seven laboratory exercises and the open-ended design project for **MIT 6.191 (Computation Structures)**, building a full digital system from the ground up: starting with basic combinational logic gates and progressing through arithmetic units, pipelined multipliers, a single-cycle RISC-V processor, cache memory hierarchies, a pipelined processor with hazard handling, an operating system kernel, and finally a performance-optimized processor for neural network inference.

Each lab builds directly on the one before it. The ALU from Lab 2 reappears inside the processor in Lab 4. The caches from Lab 5 plug into the pipelined processor in Lab 6. The processor from Lab 6 runs the OS kernel code written in Lab 7. The design project takes the pipelined processor from Lab 6 and pushes it to its limits with custom instructions, pipeline optimizations, and hardware-software co-design.

### Why I Built It

6.191 is MIT's core course on how computers work at every level of abstraction. The labs and project are designed to give hands-on experience with the full hardware-software stack:

- **Hardware design** in Minispec (a hardware description language) and Bluespec SystemVerilog
- **Processor architecture** implementing the RISC-V instruction set
- **Memory systems** with realistic cache hierarchies
- **Systems software** writing kernel code in C and RISC-V assembly
- **Performance optimization** co-designing hardware and software for a real workload

## Technical Overview

### System Architecture

The seven labs and design project form a bottom-up construction of a complete computer system:

```
Design Project: Optimized MNIST Processor (Minispec + C)
  |  custom SIMD instructions, pipelined multipliers, branch prediction, cache tuning
Lab 7: OS Kernel (C / RISC-V Assembly)
  |  syscalls, traps, process scheduling, semaphores
Lab 6: Pipelined Processor + Caches (Minispec)
  |  5-stage pipeline, hazard detection, forwarding, branch prediction
Lab 5: Cache Hierarchies (Minispec)
  |  direct-mapped and two-way set-associative caches, SRAM, writeback
Lab 4: Single-Cycle RISC-V Processor (Minispec)
  |  fetch, decode, execute, memory, writeback in one cycle
Lab 3: Sequential Circuits & Pipelining (Minispec)
  |  multipliers, bitonic sorting networks, pipelined arithmetic
Lab 2: ALU & Shifters (Minispec)
  |  adders, comparators, barrel shifters, full 32-bit ALU
Lab 1: Combinational Logic (Minispec)
     gates, multiplexers, encoders, boolean functions
```

### Tools and Languages

- **Minispec** (.ms files) - Hardware description language used for Labs 1-6 and Design Project
- **Bluespec SystemVerilog** (.bsv files) - Used for select modules and test infrastructure
- **C** - Kernel code (Lab 7), MNIST neural network software (Design Project)
- **RISC-V Assembly** (.S files) - Trap handlers, dispatchers, user processes, inline assembly for custom instructions
- **Python** - RISC-V ISA simulator and test harnesses
- **Synth toolchain** - Logic synthesis for area/delay analysis

## Course Summaries

### Lab 1: Combinational Digital Systems

Implements fundamental combinational circuits from primitive logic gates.

**Modules implemented:**
- `full_adder` - Single-bit full adder (sum + carry out)
- `parity4` - 4-bit parity via XOR reduction
- `is_power_of_2` - Detects powers of 2 in a 4-bit input
- `log_of_power_of_2` - Log base 2 for power-of-2 inputs
- `bit_scan_reverse` - Position of highest set bit
- `multiplexer8_4way` - Hierarchical 4-to-1 mux for 8-bit values
- `equal` / `vector_equal` - 8-bit and 32-bit equality comparators
- `seven_segment_decoder` - BCD to 7-segment display
- `population_count` - Bit count using cascaded full adders
- `is_geq` - Greater-than-or-equal via MSB-down cascading logic

**Key concepts:** Gate-level design, Boolean algebra, synthesis area vs. delay tradeoffs, gate library effects on circuit optimization.

### Lab 2: Arithmetic and Logic Unit

Builds a complete 32-bit ALU supporting the RISC-V arithmetic instruction set.

**Modules implemented:**
- `fullAdder` / `rca#(n)` - Ripple-carry adder (parameterized width)
- `addSub#(n)` - Adder/subtractor via conditional bit inversion
- `addWithCarry#(n)` / `fastAdd#(n)` - Carry-select fast adder (tree structure)
- `barrelRShift` - Logarithmic barrel right shifter (stages of 16, 8, 4, 2, 1)
- `sll32` / `sr32` / `sft32` - Left shift (via bit reversal trick), right shift, universal shifter
- `cmp` / `ltu32` / `lt32` - Cascaded comparators for signed and unsigned less-than
- `alu` - Top-level ALU: Add, Sub, And, Or, Xor, Slt, Sltu, Sll, Srl, Sra

**Key concepts:** Ripple-carry vs. carry-select adder tradeoffs, barrel shifter design, signed vs. unsigned comparison, critical path analysis.

### Lab 3: Multipliers, Sorting Networks, and Pipelined Math

Introduces sequential logic, pipelining, and parallel computation structures.

**Modules implemented:**
- `multiply_by_adding#(n)` - Combinational multiplier via shifted partial products
- `FoldedMultiplier#(n)` - Sequential multiplier (one bit per cycle)
- `FastFoldedMultiplier#(n)` - Optimized sequential multiplier (wider parallelism per cycle)
- `bitonicSort#(n)` / `bitonicMerge#(n)` - Recursive bitonic sorting network
- `BitonicSorter8` - 6-stage pipelined 8-element bitonic sorter
- `PipelineMath` - 3-stage pipelined computation of `((|x|/3) + 10) * 7`

**Key concepts:** Combinational vs. sequential tradeoffs (area, latency, throughput), pipelining for clock frequency, bitonic sorting network structure O(n(log n)^2), pipeline stage balancing.

### Lab 4: Single-Cycle RISC-V Processor

Implements a complete single-cycle 32-bit RISC-V processor.

**Modules implemented:**
- `Processor` - Top-level single-cycle processor
- `Decode` - Instruction decoder (opcode, funct3, funct7, immediates for R/I/S/B/U/J types)
- `Execute` - ALU operations, branch condition evaluation, next-PC computation
- `RegisterFile` - 32 registers with dual read ports, single write port, x0 hardwired to zero
- `MagicMemory` - Unified instruction/data memory with subword load/store support (Lw, Lh, Lhu, Lb, Lbu, Sw, Sh, Sb)

**Instruction types supported:** OP, OPIMM, LOAD, STORE, BRANCH, JAL, JALR, LUI, AUIPC

**Key concepts:** RISC-V ISA encoding, instruction fetch-decode-execute cycle, immediate generation, function call/return via JAL/JALR, critical path through memory.

### Lab 5: Cache Hierarchies

Builds direct-mapped and two-way set-associative caches with SRAM backing.

**Modules implemented:**
- `DirectMappedCache` - Single-way cache with tag/data/status SRAM arrays
- `TwoWayCache` - Two-way set-associative cache with LRU replacement
- Cache helpers: `getTag`, `getIndex`, `getWordOffset`, `getByteOffset`, `getLineAddr`, `getLoadData`
- `SRAM` / `SRAMBE` - Generic SRAM with optional byte-enable writes

**Cache parameters:** 64 sets, 16-word (512-bit) lines, 20-bit tags, 6-bit index, writeback policy

**State machine:** Ready -> Lookup -> (Writeback if dirty) -> Fill -> Ready

**Key concepts:** Address decomposition (tag/index/offset), hit vs. miss handling, dirty bit tracking, writeback protocol, LRU replacement policy, SRAM timing constraints.

### Lab 6: Pipelined Processor with Caches

Extends the single-cycle processor into a 5-stage pipeline integrated with instruction and data caches.

**Pipeline stages:** Fetch (I-cache) -> Decode (register file + hazard check) -> Execute (ALU + branch) -> Memory (D-cache) -> Writeback

**Key features implemented:**
- Pipeline registers (f2d, d2e, e2m, m2w) between all stages
- Data hazard detection in Decode stage
- Forwarding/bypassing from Execute and Writeback to Decode
- Stall logic: Decode stalls on unresolvable RAW hazards, Fetch stalls when Decode stalls
- Branch redirect: Execute signals misprediction, Fetch/Decode annul incorrect instructions
- Cache stall integration: pipeline stalls on I-cache or D-cache misses

**Key concepts:** Pipeline hazards (RAW dependencies), forwarding vs. stalling tradeoffs, branch misprediction penalty, pipeline flush on redirect, cache-processor integration.

### Lab 7: Operating System Kernel

Implements OS kernel infrastructure in C and RISC-V assembly, running on the processor from previous labs.

**Kernel modules implemented:**
- `handler.S` - Trap entry point, register save/restore using `mscratch` CSR
- `dispatcher.c` - Round-robin context switching between user processes
- `syscalls.c` - System call dispatcher (argument passing via a7/a0)
- `kernel_api.c` - Kernel services: print, read, semaphore wait/signal
- `libc_stubs.c` - Low-level stubs connecting libc to OS (`_write`, `_read`, `_sbrk`)
- `kernel.S` - Kernel boot and initialization

**User programs:** Two assembly processes (`proc1.S`, `proc2.S`) and two C processes (`proc3.c`, `proc4.c`)

**Key concepts:** User vs. kernel privilege modes, trap handling and context save/restore, system call interface (ecall), process table management, binary semaphores for synchronization, round-robin scheduling with MRU pointer, libc dependency on OS stubs.

### Design Project: MNIST Neural Network Processor Optimization

Open-ended capstone project: optimize a RISC-V processor and software stack to run an MNIST handwritten digit neural network as fast as possible. Scored on total runtime (cycle count × clock period). The neural network performs inference on 28×28 grayscale images through three fully-connected layers (784→300→100→10) using quantized 8-bit weights.

**Hardware optimizations implemented:**
- Kogge-Stone parallel prefix adder replacing default `+` operator (~150ps vs ~210ps)
- Custom `packmul` instruction: 4× parallel 8-bit multiply-accumulate (SIMD-style)
- Pipelined packmul: partial products in Execute, summation in Writeback via fastAdd tree
- Custom `mul`/`mulh` instructions: 32×32→64 signed multiply via 4× pipelined 16×16 partial products with carry-select combination in Writeback
- Specialized read-only instruction cache (no store/subword/writeback logic)
- Fetch2 pipeline stage: registered buffer between iCache and Decode to reduce tCLK
- Early redirect for JAL in Decode stage
- Static backward-branch prediction: predict taken for negative-offset branches
- Precomputed pc+4 and branch targets passed through pipeline registers to remove adders from Execute critical path
- Tuned cache geometry (256 sets, 16 words/line, 2-way set-associative)

**Software optimizations implemented:**
- Hardware `__mulsi3` (single mul instruction vs ~32-cycle software loop)
- Direct `hw_mulh` for requantize (eliminates ~130-cycle `__muldi3` calls)
- Packed 4-byte input loading with hardware offset transform
- 16× inner loop unrolling for the dominant matrix-vector multiply
- Function inlining with `__attribute__((always_inline))`

**Key concepts:** Hardware-software co-design, ISA extensions (custom RISC-V instructions via `.insn` directive), SIMD parallelism, pipeline balancing (tCLK vs CPI tradeoffs), critical path analysis with `synth`, carry-select adders for pipelined multiply, branch prediction strategies.

**Result:** ≤300,000ns runtime (17 out of 20 points)

## Project Structure

```
computation_structures_class_2026/
|
+-- lab1-nbhakar/          Lab 1: Combinational Digital Systems
|   +-- CombDigitalSystems.ms   Main module (all combinational circuits)
|   +-- Common.ms               Shared helper definitions
|   +-- discussion_questions.txt
|   +-- synthDir/               Synthesis output
|   +-- tests/                  Test vectors
|
+-- lab2-nbhakar/          Lab 2: ALU & Shifters
|   +-- ALU.ms                  ALU, adders, comparators
|   +-- Adders.ms               Adder implementations
|   +-- Muxes.ms                Multiplexer building blocks
|   +-- discussion_questions.txt
|   +-- synthDir/
|
+-- lab3-nbhakar/          Lab 3: Multipliers & Sorting Networks
|   +-- Multipliers.ms          Combinational and folded multipliers
|   +-- SortingNetworks.ms      Bitonic sorting network
|   +-- PipelineMath.ms         Pipelined arithmetic
|   +-- MathFunctions.ms        Helper math functions
|   +-- discussion_questions.txt
|   +-- synthDir/
|
+-- lab4-nbhakar/          Lab 4: Single-Cycle RISC-V Processor
|   +-- Processor.ms            Top-level processor
|   +-- Decode.ms               Instruction decoder
|   +-- Execute.ms              Execution unit
|   +-- RegisterFile.ms         32-register file
|   +-- MagicMemory.ms          Unified memory interface
|   +-- ProcTypes.ms            Processor type definitions
|   +-- sw/                     Test assembly programs
|   +-- discussion_questions.txt
|
+-- lab5-nbhakar/          Lab 5: Cache Hierarchies
|   +-- DirectMappedCache.ms    Direct-mapped cache
|   +-- TwoWayCache.ms          Two-way set-associative cache
|   +-- CacheTypes.ms           Cache type definitions
|   +-- CacheHelpers.ms         Address decomposition helpers
|   +-- SRAM.ms                 SRAM module
|   +-- SRAMPrimer.ms           SRAM initialization helper
|   +-- Beveren.ms              Cache benchmark
|   +-- discussion_questions.txt
|
+-- lab6-nbhakar/          Lab 6: Pipelined Processor + Caches
|   +-- Processor.ms            5-stage pipelined processor
|   +-- TwoWayCache.ms          Instruction and data caches
|   +-- DirectMappedCache.ms    Alternative cache configuration
|   +-- Decode.ms, Execute.ms   Reused from Lab 4 (enhanced)
|   +-- ALU.ms, RegisterFile.ms Reused from earlier labs
|   +-- MainMemory.ms           Main memory module
|   +-- discussion_questions.txt
|   +-- sw/                     Test programs
|
+-- lab7-nbhakar/          Lab 7: OS Kernel
|   +-- src/
|   |   +-- handler.S            Trap handler (assembly)
|   |   +-- kernel.S             Kernel boot code
|   |   +-- dispatcher.c         Context switching
|   |   +-- syscalls.c           Syscall dispatcher
|   |   +-- kernel_api.c         Kernel services
|   |   +-- libc_stubs.c         C library OS stubs
|   +-- include/
|   |   +-- dispatcher.h         Process state definitions
|   |   +-- kernel_api.h         Kernel API signatures
|   |   +-- syscalls.h           Syscall number definitions
|   +-- user/
|   |   +-- proc1.S, proc2.S     Assembly user processes
|   |   +-- proc3.c, proc4.c     C user processes
|   |   +-- start/start.S        User program entry point
|   +-- sim/
|   |   +-- Riscv.py             RISC-V ISA simulator
|   |   +-- run.py               Test harness
|   +-- tests/                   Test suites (handler, print, read, proc_tbl, sems)
|   +-- discussion_questions.txt
|
+-- project-nbhakar/       Design Project: MNIST Processor Optimization
    +-- Processor.ms            Optimized 5-stage pipelined processor
    +-- ALU.ms                  ALU with Kogge-Stone adder, packmul, mul/mulh
    +-- Decode.ms               Decoder with custom instruction support
    +-- Execute.ms              Execute stage with precomputed values
    +-- ProcTypes.ms            Extended type definitions (PACKMUL, MUL_OP)
    +-- ICacheTwoWay.ms         Specialized read-only instruction cache
    +-- TwoWayCache.ms          Two-way set-associative data cache
    +-- CacheTypes.ms           Tuned cache geometry (256 sets)
    +-- CacheHelpers.ms         Cache address helpers
    +-- mnist_src/
    |   +-- model.c             Optimized MNIST inference (packmul, hw_mulh)
    |   +-- mul.h               Hardware multiply interface (mul, mulh, packmul)
    +-- sw/                     MNIST test programs and weight data
    +-- test_out/               Simulation output
```

## How the Stack Connects

The labs and project are designed so that each layer feeds into the next:

```
Lab 1: Gates & Boolean Logic
  |
  v  (gates compose into arithmetic units)
Lab 2: ALU
  |
  v  (ALU used as execution unit; pipelining concepts applied)
Lab 3: Pipelining & Sequential Circuits
  |
  v  (ALU + decode + control = processor)
Lab 4: Single-Cycle Processor
  |
  v  (processor needs fast memory)
Lab 5: Caches
  |
  v  (pipeline the processor, attach caches)
Lab 6: Pipelined Processor + Caches
  |
  v  (hardware runs software)
Lab 7: OS Kernel
  |
  v  (optimize the whole stack for a real workload)
Design Project: MNIST Processor Optimization
```

Each lab reuses code from prior labs. The ALU module from Lab 2 appears in Labs 4 and 6. The cache modules from Lab 5 are integrated into the pipelined processor in Lab 6. The complete hardware platform from Lab 6 is what executes the kernel and user programs written in Lab 7. The design project takes the pipelined processor and caches from Lab 6 and optimizes them with custom instructions, pipeline restructuring, and software co-optimization to minimize neural network inference runtime.

## Challenges and How I Solved Them

**Carry-select adder optimization (Lab 2):** The ripple-carry adder has O(n) delay which limits clock frequency. I implemented a recursive carry-select structure (`addWithCarry`) that speculatively computes results for both carry-in values and selects based on the actual carry, reducing delay to O(log n).

**Pipeline stage balancing (Lab 3):** In the pipelined math unit, the `divide3` operation dominated the critical path. Balancing computation across pipeline stages required careful partitioning so no single stage bottlenecked throughput.

**RISC-V instruction decoding (Lab 4):** RISC-V has five distinct immediate formats (I, S, B, U, J) with bits scattered across the instruction word. Getting sign extension and bit placement correct for all formats required careful attention to the ISA specification.

**Cache state machine correctness (Lab 5):** The writeback protocol requires checking dirty bits before eviction, writing back dirty lines to main memory, then filling from memory. Getting the state transitions right (Ready -> Lookup -> Writeback -> Fill -> Ready) with proper SRAM timing was the trickiest part.

**Pipeline hazard handling (Lab 6):** Implementing forwarding paths from Execute and Writeback back to Decode, while also handling stalls for load-use hazards and branch misprediction flushes, required careful reasoning about which instruction is in which stage at any given cycle.

**Trap handler register save (Lab 7):** The trap handler must save all registers before using any, but needs a register to do the saving. Solved using the `mscratch` CSR: swap one register into `mscratch`, use it to locate the process control block, save all other registers, then save the original register from `mscratch`.

**Kogge-Stone adder for non-power-of-2 widths (Design Project):** The parallel prefix adder needed `ceil(log2(n))` stages, but Minispec's `log2()` returns floor for non-power-of-2 values. This caused silent carry propagation failures for 18-bit and 33-bit adders used in pipelined multiply. Fixed by computing exact stage counts per bit-width.

**Pipelining custom multiply instructions (Design Project):** Adding a 32×32→64 signed multiply (`mulh`) directly in the ALU increased tCLK dramatically. Solved by splitting into 4× 16×16 unsigned partial products computed in Execute, with carry-select combination and signed correction applied in Writeback. This keeps the largest multiplier at 16×16 (~200ps) instead of 32×32 (~500ps).

**Hardware-software co-design tradeoffs (Design Project):** Every hardware optimization (pipelining packmul, adding pipeline stages) trades CPI for tCLK. The key insight was that runtime = cycles × tCLK, so optimizations that increase cycles by 5% but decrease tCLK by 20% are worthwhile. Backward branch prediction in Decode eliminates most branch penalties from the inner loops, recovering cycles lost from the extra Fetch2 pipeline stage.

## TL;DR

Coursework for MIT 6.191 Computation Structures—seven labs and a design project that  build a complete computer system, from combinational logic gates up to a pipelined RISC-V processor with caches and an OS kernel, culminating in an open-ended optimization project that pushes the processor to run MNIST NN inference in under 300 nanoseconds.

---

**Project Duration:** Spring 2026
**Technologies:** Minispec, Bluespec SystemVerilog, C, RISC-V Assembly, Python, Make
