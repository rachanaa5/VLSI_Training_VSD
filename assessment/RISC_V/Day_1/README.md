# RISC-V Day 1 — ISA Basics and the GNU Toolchain

> Condensed lab notes. Day 1 tracks: RV32I/RV64I terminology → software-to-hardware flow → GCC compile → disassembly → Spike simulation → integer limits.

---

## 1. ISA Terminology and Design Philosophy

RISC-V is an **open-standard, royalty-free ISA** from UC Berkeley. No licensing barrier; clean hardware/software separation.

Design principles:

- **RISC:** small instruction set, fixed-length 32-bit encodings, simple decode, pipeline-friendly
- **Modular:** a tiny mandatory base plus optional extensions
- **Orthogonal:** clean separation of computation from memory access

### Base ISA and extensions

| Extension | Name | Description |
| :--- | :--- | :--- |
| **RV32I / RV64I** | Base Integer | Mandatory integer arithmetic, logic, control flow, load/store |
| **M** | Mul/Div | Hardware integer multiply, divide, remainder |
| **A** | Atomic | Atomic read-modify-write for multi-core sync |
| **F** | Single-precision float | 32-bit IEEE 754, registers `f0`–`f31` |
| **D** | Double-precision float | 64-bit IEEE 754 |
| **C** | Compressed | 16-bit encodings to cut code size |

### Register model

- 32 general-purpose integer registers, `x0`–`x31`
- **`x0` is hardwired to 0** — writes ignored, reads always 0
- Arithmetic and logic instructions touch **registers only**
- Memory only through explicit loads (`lb`, `lh`, `lw`, `ld`) and stores (`sb`, `sh`, `sw`, `sd`)

This is a **load-store architecture**: separate compute from memory.

---

## 2. Software to Hardware Flow

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


The ISA is the contract between the compiler and the silicon. Everything above it is translated down to instructions the hardware executes.

---

## 3. Workshop Roadmap (MYTH, 5 days)

| Day | Focus |
| :--- | :--- |
| 1 | RISC-V ISA and GNU toolchain |
| 2 | ABI, register conventions, memory allocation |
| 3 | Digital logic in TL-Verilog and Makerchip |
| 4 | Basic RISC-V CPU microarchitecture |
| 5 | Complete pipelined RISC-V CPU |

Progression: software → assembly → ISA → digital logic → datapath → pipeline.

---

## 4. Lab — Sum from 1 to N

Goal: write the C loop, compile it for RISC-V, watch the optimizer collapse it, then inspect execution in Spike.

### Source (`sum1ton.c`)

```c
#include <stdio.h>

int main(){
    int i, sum=0,n=5;
    for (i=1; i<=n; ++i){
        sum +=i;
    }
    printf("Sum of numbers from 1 to %d is %d\n", n, sum);
    return 0;
}

Native build and run
Console

$ ./a.out
Sum of numbers from 1 to 5 is 15

Cross-compile and disassemble
Console

$ riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c -lm
-O1 plus constant n = 5 lets the compiler fold the whole loop into immediates:
Text


0000000080000070 <main>:
    80000070:   ff010113            addi    sp,sp,-16
    80000074:   00113423            sd      ra,8(sp)
    80000078:   00f00613            li      a2,15      # sum = 15, computed at compile time
    8000007c:   00500593            li      a1,5       # n = 5
    80000080:   00002517            auipc   a0,0x2
    80000084:   dc050513            addi    a0,a0,-576 # format string
    80000088:   170000ef            jal     800001f8 <printf>
    8000008c:   00000513            li      a0,0
    80000090:   00813083            ld      ra,8(sp)
    80000094:   01010113            addi    sp,sp,16
    80000098:   00008067            ret

Note the prologue/epilogue (sp adjust, ra save/restore) — the calling convention is present even though no loop remains.
Simulate under Spike
Console

$ spike pk sum1ton.o
bbl loader
Sum of numbers from 1 to 5 is 15

Debugger stepping
spike -d accepts commands. until pc 0 <addr> runs to an address, reg 0 <name> prints a register before single-stepping with Enter.
Console

(spike) until pc 0 100b0
(spike) reg 0 a2
0x00000003ffffffaf8
(spike) reg 0 sp
0x00000003ffffffad0

5. Integer Widths
unsigned long long int covers 64 bits, so its maximum is 2^64 − 1. Built via pow(2,64)-1 and printed with %llu.
C

#include <stdio.h>
#include <math.h>

int main(){
    unsigned long long int max = (unsigned long long int)(pow(2,64)-1);
    printf("highest number represented by unsigned long long int is %llu\n",max);
    return 0;
}

Signed/unsigned interpretation and width are set by the ISA — RV64I fixes the widths the compiler can target.
Key takeaways
Base ISA is RV32I/RV64I; extensions are added only as needed
Load-store discipline: registers compute, loads/stores move data
x0 is a permanent zero
The toolchain turns a loop into li immediates under -O1; Spike + pk runs the ELF against a proxy kernel
spike -d gives until, reg, and single-step for instruction-level inspection
Text

