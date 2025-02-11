
<summary><b>Task 4:</b> Functional simulation of RISC-V Core Verilog netlist and Testbench </summary>   
<br>

In this task, we will perform functional simulation of RISC-V instructions modeled as a Verilog netlist and observe the output waveforms using GTKWave.

We will use iverilog and Gtkwave tools

How it works?
-
You write Verilog code and run simulations using Icarus Verilog. The simulator generates a Value Change Dump (VCD) or other waveform file formats.
These waveform files are then loaded into GTKWave for graphical analysis of the signal behavior, helping you verify your design's functionality and timing.

Installing iverilog using command ``sudo apt install iverilog gtkwave``

![iverilog install](https://github.com/user-attachments/assets/9450ad57-a250-4055-bdb8-511628e50a0a)

Steps to perform functional simulation of RISC-V:
-
1.create a new directory using your name as ``mkdir harshith1``.

2.create two files using command ``touch`` and give them names as ``harshith1_rv32i.v``and ``harshith1_rv32i_tb.v``.These are used for verilog and testbench files respectively.

As part of this internship, we are not writing the Verilog code ourselves instead, we are cloning the Verilog and testbench code from a reference GitHub repository.

Refernce Github Repository: [iiitb_rv32i](https://github.com/vinayrayapati/rv32i/tree/main)

3.using leafpad copy the verilog and testbench code in respective files in the directory

4.Simulate the Verilog code using

``iverilog -o harshith1_rv32i harshith1_rv32i.v harshith_rv32i_tb.v`` 

it will create iiitb_rv32i.vcd file which is used for gtkwave.

![vcd file command](https://github.com/user-attachments/assets/18b922c3-3d36-4079-8e77-f6f7387bd409)

5.Open the gtkwave using command ``gtkwave iiitb_rv32i.vcd``


The given Verilog file contains instructions that are hard-coded, meaning the designer has used custom bit patterns for each instruction instead of following the standard RISC-V specification for bit encoding.

| **Operation**      | **Description**                                       | **Standard RISC-V ISA** | **Hard-Coded ISA** |
|---------------------|-------------------------------------------------------|-------------------------|---------------------|
| **ADD R6, R2, R1**  | Adds the values in R2 and R1, stores result in R6     | `32'h00110333`         | `32'h02208300`      |
| **SUB R7, R1, R2**  | Subtracts the value in R2 from R1, stores result in R7| `32'h402083b3`         | `32'h02209380`      |
| **AND R8, R1, R3**  | Performs bitwise AND between R1 and R3, stores in R8  | `32'h0030f433`         | `32'h0230a400`      |
| **OR R9, R2, R5**   | Performs bitwise OR between R2 and R5, stores in R9   | `32'h005164b3`         | `32'h02513480`      |
| **XOR R10, R1, R4** | Performs bitwise XOR between R1 and R4, stores in R10 | `32'h0040c533`         | `32'h0240c500`      |
| **SLT R1, R2, R4**  | Sets R1 to 1 if R2 < R4, else sets to 0               | `32'h0045a0b3`         | `32'h02415580`      |
| **ADDI R12, R4, 5** | Adds immediate value 5 to R4, stores result in R12    | `32'h004120b3`         | `32'h00520600`      |
| **BEQ R0, R0, 15**  | Branches to offset 15 if R0 equals R0                 | `32'h00000f63`         | `32'h00f00002`      |
| **SW R3, R1, 2**    | Stores word from R3 to memory address (R1 + 2)        | `32'h0030a123`         | `32'h00209181`      |
| **LW R13, R1, 2**   | Loads word from memory address (R1 + 2) into R13      | `32'h0020a683`         | `32'h00208681`      |
| **SRL R16, R14, R2**| Shifts R14 right by the value in R2, stores in R16    | `32'h0030a123`         | `32'h00271803`      |
| **SLL R15, R1, R2** | Shifts R1 left by the value in R2, stores in R15      | `32'h002097b3`         | `32'h00208783`      |

1. **Standard RISC-V ISA**: Instructions follow the official RISC-V specification for 32-bit instruction encoding.
2. **Hard-Coded ISA**: Instructions deviate from the RISC-V standard and follow a custom encoding defined by the designer.
3. To ensure correct simulation, you must use the hard-coded instruction encodings when working with the provided Verilog netlist and testbench.

Veifying instructions using Gtkwave :
-
1.``ADD R6, R2, R1``

![add gtk](https://github.com/user-attachments/assets/19a0d4f4-a836-4880-8fbc-0b359449b384)

2.``SUB R7, R1, R2``

![sub gtkwave](https://github.com/user-attachments/assets/0aa6192b-4f81-4604-b09d-e1f1d7617324)

3.``AND R8, R1, R3``

![and gtkwave](https://github.com/user-attachments/assets/fac6962c-000a-4f88-b7ba-61d542f1087c)

4.``OR R9, R2, R5``

![or gtkwave](https://github.com/user-attachments/assets/f1bad13c-7453-48cf-b5c8-89fecd460315)

5.``XOR R10, R1, R4``

![xor gtkwave](https://github.com/user-attachments/assets/159a1b56-4811-4261-9102-7cf9d3819b69)

6.``SLT R1, R2, R4``

![slt gtkwave](https://github.com/user-attachments/assets/8191d7b8-c73f-4a66-9b23-ad62c6e00f80)

7.``ADDI R12, R4, 5``

![addi gtkwave](https://github.com/user-attachments/assets/faae3915-9462-42b6-9231-0a36180c63f6)

8.``SW R3, R1, 2``

![sw gtkwave](https://github.com/user-attachments/assets/70c6306d-4a62-483f-8cc3-9260607e417c)
value in register R3 is stored into memory at an address calculated as the sum of the base address in register R1 and the offset 2.
offset 2 is added to the base address from R1 to compute the target memory address (visible in the EX_MEM_ALU_OUT signal).

9.``SRL r16, r11, r2``

![Srl gtkwave](https://github.com/user-attachments/assets/0f7c2e0e-f3dc-4a11-846d-2da9b78f5260)

Perform a logical right shift on the value in r11 by the number of bits specified in r2, and store the result in r16.

10.``BEQ R0, R0, 15``

![beq gtkwave](https://github.com/user-attachments/assets/850966c3-da9f-457c-bdb8-1351daf8cb8c)

This instruction compares the values in registers R0 and R0.
If the values are equal, the program counter (PC) is updated to branch to a target address determined by the offset

Target Address=Current PC+Offset


11.``BNE R0, R1, 15``

![bne gtkwave](https://github.com/user-attachments/assets/30fbe5e7-732a-4dd3-8e2a-201cf548fbaa)

BNE (Branch Not Equal):

Compares the values in R0 and R1.

If R0 ≠ R1, the program counter (PC) branches to the target address.

If R0 = R1, no branch occurs, and the PC continues sequentially.

Offset = 20 (decimal).

Branch Target Address=PC+Offset

12.`` SLL R15, R1, R2``

![sll gtkwave](https://github.com/user-attachments/assets/ecff238a-ecc6-4a39-8714-bcfdbef912e0)

Shift Left Logical (SLL) instruction, which shifts the bits in R1 to the left by the amount specified in R2, with the result stored in R15.

R1: original value to be shifted.

R2: Specifies the shift amount.

Output:

The result (R15) is observed as 0x00000019 in EX_MEM_ALUOUT.
