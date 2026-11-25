Markdown# RV Day 1 — Introduction to RISC-V ISA and GNU Compiler Toolchain

---

# 1-RV_D1SK1_L1_Introduction_To_RISC-V_Basic_Keywords

## Overview

This lecture introduces the fundamental architectural terminology and design philosophy behind the open-source **RISC-V Instruction Set Architecture (ISA)**.

The lecture establishes the foundation for:
- Reduced Instruction Set Computer (RISC) design principles
- modular ISA architecture (base + extensions)
- register organization and load-store execution
- integer data widths (RV32I vs. RV64I)

---

## Key Concepts

### Why RISC-V?

RISC-V is an open-standard ISA originally developed at UC Berkeley that eliminates proprietary licensing barriers while maintaining clean hardware-software separation.

Characteristics:
- **Open-source & royalty-free:** Anyone can design, manufacture, and sell RISC-V chips.
- **Modular:** Starts with a small mandatory base integer ISA and adds optional extensions only when needed.
- **Clean & orthogonal:** Fixed-length 32-bit base instruction encoding simplifies instruction decoding and pipelining.

---

### Base ISA & Standard Extensions

| Extension | Name | Description |
| :--- | :--- | :--- |
| **RV32I / RV64I** | Base Integer | Mandatory integer arithmetic, logic, control flow, and load/store instructions (32-bit or 64-bit) |
| **M** | Multiplication & Division | Hardware integer multiply, divide, and remainder instructions |
| **A** | Atomic | Atomic read-modify-write memory operations for multi-core synchronization |
| **F** | Single-Precision Float | 32-bit IEEE 754 floating-point instructions and registers (`f0`–`f31`) |
| **D** | Double-Precision Float | 64-bit IEEE 754 floating-point instructions |
| **C** | Compressed | 16-bit compressed instruction encodings to reduce code size |

---

### Load-Store Architecture

- RISC-V provides **32 general-purpose integer registers** (`x0` to `x31`).
- Register **`x0`** is hardwired to constant **`0`** (writes to `x0` are ignored; reads always return `0`).
- Arithmetic and logic instructions (`add`, `sub`, `and`, `or`) operate **only on registers**.
- Memory is accessed exclusively through explicit **load** (`lb`, `lh`, `lw`, `ld`) and **store** (`sb`, `sh`, `sw`, `sd`) instructions.

---

## Key Learning Outcome

After this lecture, the learner understands:
- the modular structure of RV32I and RV64I
- the role of standard ISA extensions (`M`, `A`, `F`, `D`, `C`)
- how a load-store register architecture separates computation from memory access

---

# 2-RV_D1SK1_L2_From_Apps_To_Hardware

## Overview

This lecture explains how software applications are ultimately translated into hardware-level execution inside a processor system.

The lecture establishes the connection between:
- high-level applications
- operating systems
- compilers
- assembly language
- hardware implementation

---

## Key Concepts

### Software to Hardware Flow

```text
Applications
    ↓
Operating System
    ↓
Compiler
    ↓
Assembly Language
    ↓
Instruction Set Architecture (ISA)
    ↓
Hardware
Applications: High-level programs written in C, C++, or Python that cannot execute directly on silicon.Operating System (OS): Manages process scheduling, memory allocation, file systems, and hardware resources (Linux, Windows, macOS).Compiler (GCC): Translates high-level C code into ISA-specific assembly instructions while performing optimizations.Assembly Language: Human-readable mnemonic representation of machine instructions (e.g., add x5, x1, x2).Instruction Set Architecture (ISA): The formal contract between software and hardware defining supported instructions, registers, and encodings.Key Learning OutcomeAfter this lecture, the learner understands:how high-level software reaches hardware executionthe role of compilers and the OSwhy an open ISA like RISC-V bridges software engineering and RTL hardware design3-RV_D1SK2_L1_Labwork_For_RISC-V_Software_ToolchainOverviewThis lecture introduces the hands-on software lab workflow:writing a C program (sum1ton.c) to compute the summation of integers from 1 to Ncompiling and executing the C code on the native host machine using gccverifying baseline functional correctness before cross-compiling for RISC-VLab Execution & ScreenshotsStep 1: Writing the C Program (sum1ton.c)The program initializes n = 5 and sum = 0, loops from i = 1 to n to accumulate sum, and prints the output.C#include <stdio.h>

int main() {
    int i, sum = 0, n = 5;
    for (i = 1; i <= n; ++i) {
        sum += i;
    }
    printf("Sum of numbers from 1 to %d is %d\n", n, sum);
    return 0;
}
Explanation: sum1ton.c is created in nano on Ubuntu WSL (~) with n = 5.Step 2: Native Host Compilation & ExecutionBashgcc sum1ton.c
./a.out
Explanation: Running ./a.out prints Sum of numbers from 1 to 5 is 15, verifying baseline functionality ($1 + 2 + 3 + 4 + 5 = 15$) before cross-compiling for RISC-V.Key Learning OutcomeAfter this lecture, the learner understands:how to write and verify a C benchmark program on the host machine prior to RISC-V cross-compilation4-RV_D1SK2_L2_RISC-V_GCC_Compile_And_DisassembleOverviewThis lecture demonstrates:cross-compiling a C program for the 64-bit RISC-V architecture (RV64I) using riscv64-unknown-elf-gccapplying compiler optimization flags (-O1 vs. -Ofast)disassembling the resulting ELF binary using riscv64-unknown-elf-objdump to inspect RISC-V assembly instructionsCompilation & Disassembly CommandsBash# Cross-compile targeting RV64I with LP64 ABI and -Ofast optimization
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i --specs=picolibc.specs -nostartfiles -T ~/pk.ld -o sum1ton.o ~/pk_stub.c sum1ton.c

# Disassemble the compiled ELF binary
riscv64-unknown-elf-objdump -d sum1ton.o | less
Disassembly Explanation (<main> at 0x80000070)addi sp, sp, -16 / sd ra, 8(sp): Allocates a 16-byte stack frame and saves the return address ra.li a2, 15 / li a1, 5: Under -Ofast, the compiler pre-computed the loop result (15) at compile time and loaded it directly into argument register a2 alongside n = 5 in a1.auipc / addi a0, ... / jal 800001f8 <printf>: Loads the format string pointer into a0 and calls printf.li a0, 0 / ld ra, 8(sp) / addi sp, sp, 16 / ret: Restores stack state and returns 0.Key Learning OutcomeAfter this lecture, the learner understands:how to cross-compile C code for rv64i and lp64 using riscv64-unknown-elf-gcchow to inspect machine encodings and assembly instructions using riscv64-unknown-elf-objdump5-RV_D1SK2_L3_Spike_Simulation_And_DebugOverviewThis lecture introduces:Spike RISC-V ISA simulatorprogram execution via the Proxy Kernel (pk)interactive instruction-level debugging (spike -d)live register and Program Counter (pc) inspectionExecuting a Program Using SpikeBashspike pk sum1ton.o
Explanation: spike pk sum1ton.o executes the cross-compiled RISC-V binary on the simulated RV64I processor and prints Sum of numbers from 1 to 5 is 15.Interactive Debug Mode in SpikeBashspike -d pk sum1ton.o
Spike CommandPurposeuntil pc 0 <addr>Run until core 0 reaches target Program Counter addressreg 0 <reg>View current 64-bit contents of a register on core 0(Enter)Single-step one instruction at a timerunContinue execution to completionqQuit interactive debuggerExplanation: Using until pc 0 100b0, execution paused at PC 0x100b0. Inspecting reg 0 a2 (0x0000003ffffffaf8) and reg 0 sp (0x0000003ffffffad0) demonstrates real-time processor state observation during execution.Key Learning OutcomeAfter this lecture, the learner understands:how to run RISC-V ELF binaries on Spikehow to step through instructions and inspect pc and general-purpose registers in debug mode6-RV_D1SK3_Integer_Number_RepresentationOverviewThis lecture explains how 64-bit integers are represented in hardware registers using unsigned binary and signed 2's complement encoding, and verifies numerical boundaries on Spike.RepresentationWidthMinimum ValueMaximum ValueUnsigned (unsigned long long int)64-bit$0$$2^{64} - 1 = 18,446,744,073,709,551,615$Signed 2's Complement (long long int)64-bit$-2^{63} = -9,223,372,036,854,775,808$$2^{63} - 1 = +9,223,372,036,854,775,807$Lab Execution & ScreenshotsPart 1: Highest 64-Bit Unsigned Integer (unsignedHighest.c)Explanation: C program computing $(2^{64} - 1)$ cast to unsigned long long int.Explanation: Running spike pk unsignedHighest.o prints highest number represented by unsigned long long int is 18446744073709551615.Part 2: Highest & Lowest 64-Bit Signed Integer (signedHighest.c)Explanation: C program computing $2^{63} - 1$ (max) and $-2^{63}$ (min) as signed long long int values.Explanation: Running spike pk signedHighest.o prints 9223372036854775807 as the maximum and -9223372036854775808 as the minimum signed 64-bit integer.Key Learning OutcomeAfter this lecture, the learner understands:how 64-bit unsigned and 2's complement signed numbers are stored in RV64I registerswhy 2's complement represents one additional negative value ($-2^{63}$) compared to positive ($2^{63}-1$)