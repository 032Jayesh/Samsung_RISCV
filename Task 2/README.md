# Task 2
Performing SPIKE simulation and Debugging the C code Using spike.
----------
What is Spike Simulation?
--------- 
`Spike` is the official RISC-V ISA (Instruction Set Architecture) simulator. It allows developers to simulate RISC-V programs and applications, providing an environment to run, test, and debug code designed for RISC-V-based processors. Spike is commonly used in the context of development and research related to RISC-V architecture.

`GCC (GNU Compiler Collection)` is a popular set of compilers that supports a variety of programming languages, including C and C++. In the context of RISC-V, GCC can be used to compile code for RISC-V targets, and Spike can then simulate the execution of that code on a virtual RISC-V machine.

Now we wil use the command `spike pk sum1ton.o` give the output of the C code and verifies the instructions are correct

![Screenshot from 2024-11-25 11-42-08](https://github.com/user-attachments/assets/e369d5bb-0bb3-4ec5-92a7-fdf1847afa2e)

Debugging the Assembly Language Program using ``spike -d pk sum1ton.o`` in a new terminal window.

Assembly Language Program :

![assemblylang](https://github.com/user-attachments/assets/75bdb822-76cd-4b17-8913-4f573dcbfdd4)

Debugger:

![match sp](https://github.com/user-attachments/assets/1d6583a8-e92a-4ae1-a792-40461e99a1e4)

In Debugger we Debug the Assembly Language by following the each instruction .At the address of `100b4` the register value of stack point `sp` is `0x0000003ffffffb50` and after completion of instruction`sp, sp, -16` ,the new value of register stack pointer is `0x0000003ffffffb40`

`lui` 
Load Upper Immediate:

![Screenshot 2024-11-25 154052](https://github.com/user-attachments/assets/8e55efa5-e3c8-4f87-bc37-91a4d644515a)

 Instruction: `lui a0, %hi(LC1)`
LUI is an instruction in the RISC-V architecture that loads a 20-bit immediate value into the upper 20 bits of a 32-bit or 64-bit register. The lower 12 bits of the register are set to zero.
In the example, the instruction loads the upper 20 bits of a label (LC1) into the register a0

` addi`
Add Immediate
![image](https://github.com/user-attachments/assets/e8b72f51-cee7-4706-9fec-226a7d1eb7e9)

 Instruction:` addi a0, a0, %lo(LC1)`
Purpose: The ADDI instruction adds an immediate value (12-bit constant) to the value in a source register (rs1) and stores the result in a destination register (rd).

![final](https://github.com/user-attachments/assets/f055f819-bad6-426d-a04a-05fd96a704ee)

After  finishing all the instructions in Assembly language ,At the address of `100d8` it returns the value of sum.

task : Wite a simple C program for any application and compile using RISC -V GCC/SPIKE

Application:
--

Countdown Counter :
--
The countdown counter is a program that begins from a specified value and decrements it by one at regular intervals until it reaches zero.

We want to create a program that:

1.Initializes a timer with a starting value (e.g., 10 seconds).

2.Prints the current countdown value.

3.Decrements the timer every second.

4.Stops when the timer reaches zero.

C-program using Leafpad :
-
![counterdown leafpad](https://github.com/user-attachments/assets/5eb13062-6041-481f-9971-946d5169e903)

output of C code is :

![c out counterdown](https://github.com/user-attachments/assets/9753cb5a-5902-45a3-959a-8a3c3c8ac925)

compilation using gcc :

![counterdown using gcc](https://github.com/user-attachments/assets/6c1c059b-38ec-45b6-98ee-10562faaaff7)

Assembly Language program for the above C code: 


![Assembly language counterdown](https://github.com/user-attachments/assets/e42643af-fc6c-4bcb-9d62-acd4bdd7d234)

Debugging all the instructions in the Assembly language program using spike 

![debug using spike](https://github.com/user-attachments/assets/421d1e4f-ab7c-42fe-8ca9-2897a80975da)

Debugging the Assembly Language instructions :

| **Address** | **Instruction**          | **Explanation**                                    |
|-------------|--------------------------|----------------------------------------------------|
| `101c4`     | `addi sp, sp, -32`       | Allocate 32 bytes on the stack.                   |
| `101c8`     | `sd ra, 24(sp)`          | Save return address (`ra`) on the stack.          |
| `101ca`     | `sd s0, 16(sp)`          | Save register `s0` on the stack.                  |
| `101cc`     | `sd s1, 8(sp)`           | Save register `s1` on the stack.                  |
| `101ce`     | `sd s2, 0(sp)`           | Save register `s2` on the stack.                  |
| `101d0`     | `lui s2, 0x21`           | Load upper immediate `0x21` into `s2`.            |
| `101d4`     | `addi s2, s2, 720`       | Add `720` to `s2`.                                |
| `101d8`     | `mv a0, s2`              | Move the value of `s2` to `a0` (argument for `printf`). |
| `101dc`     | `jal ra, 1047c <printf>` | Call the `printf` function.                       |
| `101e0`     | `addi ra, 10184 <delay>` | Load the address of `delay` into `ra`.            |
| `101e4`     | `addiw s0, s0, -1`       | Decrement `s0` by 1.                              |
| `101e6`     | `bne s0, s1, 101e4`      | Branch to `101e4` if `s0` is not equal to `s1` (loop condition). |
| `101e8`     | `lui a0, 0x21`           | Load upper immediate `0x21` into `a0`.            |
| `101ea`     | `addi a0, a0, 744`       | Add `744` to `a0`.                                |
| `101ec`     | `jal a0, 105a0 <puts>`   | Call the `puts` function.                         |
| `10200`     | `ld ra, 24(sp)`          | Restore return address (`ra`) from the stack.     |
| `10202`     | `ld s0, 16(sp)`          | Restore register `s0` from the stack.             |
| `10204`     | `ld s1, 8(sp)`           | Restore register `s1` from the stack.             |
| `10206`     | `ld s2, 0(sp)`           | Restore register `s2` from the stack.             |
| `1021e`     | `addi sp, sp, 32`        | Deallocate 32 bytes from the stack.               |
| `10220`     | `ret`                    | Return to the caller.                             |

## Purpose:
The purpose of this folder is to document the progress and results of Task 2.

