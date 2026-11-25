RV Day 1 — RISC-V / GNU Toolchain
1. Sum from 1 to N
A C program calculates the sum from 1 to 5. The result is 15.
![Day_1.1](./Day_1.1.png)
Execution output:
![Day_1.2](./Day_1.2.png)
2. RISC-V Disassembly and Compilation
Disassembled the compiled program to inspect the generated RISC-V instructions.
![Day_1.3](./Day_1.3.png)
Compiled the C program with the RISC-V GCC toolchain and ran it on the RISC-V environment.
![Day_1.4](./Day_1.4.png)
3. Spike Simulation and Register Debugging
Ran the program in Spike and used the debugger to step through instructions and inspect registers.
![Day_1.5](./Day_1.5.png)
4. Unsigned `long long int` Maximum
Used `pow(2, 64) - 1` to calculate the maximum unsigned `long long int` value.
Output: `18446744073709551615`
![Day_1.6](./Day_1.6.png)
![Day_1.7](./Day_1.7.png)
5. Signed `long long int` Range
Calculated the maximum and minimum signed `long long int` values using `pow(2, 63)`.
![Day_1.8](./Day_1.8.png)
Output:
Maximum: `9223372036854775807`
Minimum: `-9223372036854775808`
![Day_1.9](./Day_1.9.png)