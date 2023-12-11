# 5. Instruction Set

## 5.1 INTRODUCTION

The Z280 CPU's instruction set is a superset of the Z80's; the Z280 CPU is opcode compatible with the Z80 CPU. Thus, a Z80 program can be executed on a Z280 MPU without modification. The instruction set is divided into ten groups by function:

* 8-bit load
* 16-bit load and exchange
* Block transfer and search
* 8-bit arithmetic and logical
* 16-bit arithmetic
* Rotate, shift, and bit manipulation
* Program control
* Input/Output
* CPU control
* Extended instructions

Thie chapter describes the instruction set of the Z280 CPUs. First, flags and condition codes are discussed in relation to the instruction set. Then, interruptibility of instructions is discussed and traps are described. The last part of this chapter is a detailed description of each instruction, listed in alphabetic order by mnemonic. This section is intended to be used as a reference for Z280 MPU programmers. The entry for each instruction contains a complete description of the instruction, including addressing modes, assembly language mnemonics, instruction opcode formats, and simple examples illustrating the use of the instruction.


## 5.2 PROCESSOR FLAGS

The Flag register contains six bits of status information, that are set or cleared by CPU operations (Figure 5-1). Four of these bits are testable (C, P/V, Z, and S) for use with conditional jump, call, or return instructions. Two flags are not testable (H, N) and are used for binary-coded decimal (BCD) arithmetic.

![Figure 5-1. Flag Register](Images/Figure5.1.png) <br/>
_Figure 5-1. Flag Register_

The flags provide a link between sequentially executed instructions, in that the result of executing one instruction may alter the flags, and the resulting value of the flags can be used to determine the operation of a subsequent instruction. The program control instructions whose operation depends on the state of the flags are the Jump, Jump Relative, subroutine Call, and subroutine Return instructions; these instructions are referred to as conditional instructions.


### 5.2.1 Carry Flag (C)

The Carry flag is set or cleared depending on the operation being performed. For add instructions that generate a carry and subtract instructions that generate a borrow, the Carry flag is set to 1. The Carry flag is cleared to 0 by an add that does not generate a carry or a subtract that generates no borrow. This saved carry facilitates software routines for extended precision arithmetic. The multiply and divide instructions use the Carry flag to signal information about the precision of the result. Also, the Decimal Adjust Accumulator instruction leaves the Carry flag set to 1 if a carry occurs when adding BCD quantities.

For the rotate instructions, the Carry flag is used as a link between the least significant and most significant bits for any register or memory location. During shift instructions, the Carry flag contains the last value shifted out of any register or memory location. For logical instructions the Carry flag is cleared. The Carry flag can also be set and complemented with explicit instructions.


### 5.2.2 Add/Subtract Flag (N)

The Add/Subtract flag is used for BCD arithmetic. Since the algorithm for correcting BCD operations is different for addition and subtraction, this flag is used to record whether an add or subtract was last executed, allowing a subsequent Decimal Adjust Accumulator instruction to perform correctly. See the discussion of the DAA instruction for further information.


### 5.2.3 Parity/Overflow Flag (P/V)

This flag is set to a particular state depending on the operation being performed.

For signed arithmetic, this flag, when set to 1, indicates that the result of an operation on twos-complement numbers has exceeded the largest number, or is less than the smallest number, that can be represented using twos-complement notation. This overflow condition can be determined by examining the sign bits of the operands and the result.

The P/V flag is also used with logical operations and rotate instructions to indicate the parity of the result. The number of bits set to 1 in a byte are counted. If the total is odd, odd parity (P = 0) is flagged. If the total is even, even parity is flagged (P = 1).

During block search and block transfer instructions, the P/V flag monitors the state of the byte count register (BC). When decrementing the byte counter results in a zero value, the flag is cleared to 0, otherwise the flag is set to 1.

During the Load Accumulator with I or R register instructions, the P/V flag is loaded with the contents of the Interrupt A enable bit in the Master Status register.

When inputting a byte to a register from an I/O device addressed by the C register, the flag is adjusted to indicate the parity of the data.


### 5.2.4 Half-Carry Flag (H)

The Half-Carry flag (H) is set to 1 or cleared to 0 depending on the carry and borrow status between bits 3 and 4 of an 8-bit arithmetic operation and between bits 11 and 12 of a 16-bit arithmetic operation. This flag is used by the Decimal Adjust Accumulator instruction to correct the result of an addition or subtraction operation on packed BCD data.


### 5.2.5 Zero Flag (Z)

The Zero flag (Z) is set to 1 if the result generated by the execution of certain instructions is a zero.

For arithmetic and logical operations, the Zero flag is set to 1 if the result is zero. If the result is not zero, the Zero flag is cleared to 0.

For the block search instructions, the Zero flag is set to 1 if a comparison is found between the value in the Accumulator and the memory location pointed to by the contents of the register pair HL.

When testing a bit in a register or memory location, the Zero flag contains the complemented state of the tested bit (i.e., the Zero flag is set to 1 if the tested bit is a 0, and vice-versa).

For the block I/O instructions, if the result of decrementing B is zero, the Zero flag is set to 1; otherwise, it is cleared to 0. Also for byte inputs to registers from I/O devices addressed by the C register, the Zero flag is set to 1 to indicate a zero byte input.


### 5.2.6 Sign Flag (S)

The Sign flag (S) stores the state of the most significant bit of the result. When the Z280 CPU performs arithmetic operations on signed numbers, binary twos-complement notation is used to represent and process numeric information. A positive number is identified by a zero in the most significant bit. A negative number is identified by a 1 in the most significant bit.

When inputting a byte from an I/O device addressed by the C register to a CPU register, the Sign flag indicates either positive (S = 0) or negative (S = 1) data.

For the Test and Set instruction, the Sign bit is set to 1 if the tested bit is 1, otherwise it is cleared to 0.


### 5.2.7 Condition Codes

The Carry, Zero, Sign, and Parity/Overflow flags are used to control the operation of the conditional instructions. The operation of these instructions is a function of the state of one of the flags. Special mnemonics called condition codes are used to specify the flag setting to be tested during execution of a conditional instruction; the condition codes are encoded into a 3-bit field in the instruction opcode itself.

Table 5-1 lists the condition code mnemonic, the flag setting it represents, and the binary encoding for each condition code.

<br/>

**Condition Codes for Jump, Call, and Return Instructions:**

| Mnemonic | Meaning | Flag Setting | Binary Code |
|-|-|-|-|
|NZ | Not Zero | Z = 0 | 000 |
|Z  | Zero     | Z = 1 | 001 |
|NC | No Carry | C = 0 | 010 |
|C  | Carry    | C = 1 | 011 |
|NV | No Overflow | V = 0 | 100
|PO | Parity Odd  | V = 0 | 100
|V  | Overflow    | V = 1 | 101
|PE | Parity Even | V = 1 | 101
|NS | No Sign  | S = 0 | 110
|P  | Plus     | S = 0 | 110
|S  | Sign     | S = 1 | 111
|M  | Minus    | S = 1 | 111

<br/>

**Condition Codes for Jump Relative Instruction:**

| Mnemonic | Meaning | Flag Setting | Binary Code
|-|-|-|-
|NZ | Not Zero | Z = 0 | 100
|Z  | Zero     | Z = 1 | 101
|NC | No Carry | C = 0 | 110
|C  | Carry    | C = 1 | 111

_Table 5-1. Condition Codes_


## 5.3 INSTRUCTION EXECUTION AND EXCEPTIONS

Two types of exception conditions, interrupts and traps, can alter the normal flow of program execution. Interrupts are asynchronous events generated by a device external to the CPU; peripheral devices use interrupts to request service from the CPU. Traps are synchronous events generated internally in the CPU by particular conditions that occur during instruction execution. Interrupts and traps are discussed in detail in Chapter 6. This section examines the relationship between instructions and the exception conditions.


### 5.3.1 Instruction Execution and Interrupts

When the CPU receives an interrupt request, and it is enabled for interrupts of that class, the interrupt is normally processed at the end of the current instruction. However, the block transfer and search instructions are designed to be interruptible so as to minimize the length of time it takes the CPU to respond to an interrupt. If an interrupt request is received during a block move, block search, or block I/O instruction, the instruction is suspended after the current iteration. The address of the instruction itself, rather than the address of the following instruction, is saved on the system stack, so that the same instruction is executed again when the interrupt handler executes an interrupt return instruction. The contents of the repetition counter and the registers that index into the block operands are such that, after each iteration, when the instruction is reissued upon returning from an interrupt, the effect is the same as if the instruction were not interrupted. This assumes, of course, that the interrupt
handler preserved the registers.


### 5.3.2 Instruction Execution and Traps

Traps are synchronous events that result from the execution of an instruction. The action of the CPU in response to a trap condition is similar to the case of an interrupt in interrupt mode 3 (see Chapter 6). All traps except for Extended Instruction, System Stack Overflow Warning, Single Step and Breakpoint-on-Halt are nonmaskable.

The Z280 MPU supports eight kinds of traps:

* Division Exception
* Extended Instruction
* Privileged Instruction
* System Call
* Access Violation (page fault and write protect)
* System Stack Overflow Warning
* Single Step
* Breakpoint-on-Halt

The Division Exception trap occurs when executing a divide instruction if either the divisor is zero or the result cannot be represented in the destination (overflow).

The Extended Instruction trap occurs when an extended instruction is encountered, but the Extended Processor Architecture is disabled, (the EPA bit in the Trap Control register should be cleared to 0 if there is no EPU in the system or if the Z280 MPU is configured with an 8-bit bus). This allows the same software to be run on Z280 MPU system configurations with or without Extended Processing Units (EPUs). For systems without EPUs, the desired extended instructions can be emulated by software that is invoked by the Extended Instruction trap. For systems with an 8-bit data bus that also have an EPU, the software invoked by the Extended Instruction trap can use I/O instructions to access the EPU. The information saved on the system stack during this trap is designed to facilitate the 8-bit I/O interface to an EPU by providing address
calculation for the operands and by pushing addresses onto the system stack in the reverse order from which they will be used by an I/O interface trap handler.

The Privileged Instruction trap serves to protect the integrity of a system from erroneous or unauthorized actions of user mode processes. Certain instructions, called privileged instructions, can be executed only in system mode. An attempt to execute one of these instructions in user mode causes a Privileged Instruction trap.

The System Call instruction always causes a trap. This instruction is used to transfer control to system mode software in a controlled way, typically to request operating system services.

The Access Violation trap occurs whenever the Z280 MPU's on-chip MMU detects an illegal memory access. Access Violation traps cause instructions to be aborted. When Access Violation traps occur, the logical address of the instruction is pushed onto the system stack along with the Master Status register; part of the logical address that caused the page fault is latched in the MMU to indicate which page frame caused the fault; and the CPU registers are unmodified, i.e., their contents are the same as just before the instruction execution began. (For block move, block search, or block I/O instructions, the registers are the same as just before the iteration in which the page fault occurred.)

The System Stack Overflow Warning trap arises when pushing information onto the system stack causes the Stack Pointer to reference a specified 16-byte area of memory. Use of this facility protects the system from system stack overflow errors.

The Single Step trap occurs with the execution of each instruction, provided the Single-Step control bit in the Master Status register is set to 1. This facilitates software debugging of programs.

The Breakpoint-on-Halt trap occurs whenever the Halt instruction is encountered and the Breakpoint-on-Halt control bit in the Master Status register is set to 1. This facilitates software debugging of programs.


## 5.4 INSTRUCTION SET FUNCTIONAL GROUPS

This section presents an overview of the Z280 instruction set, arranged by functional groups. (See Section 5.5 for an explanation of the notation used in Tables 5-2 through 5-11.)


### 5.4.1 8-Bit Load Group

This group of instructions (Table 5-2) includes load instructions for transferring data between byte registers, transferring data between a byte register and memory, and loading immediate data into byte registers or memory. All addressing modes are supported for loading between the accumulator and memory or for loading immediate values into memory. Loads between other registers and memory use the IR and SX addressing modes. An exchanqe instruction is available for swapping the contents of the accumulator with another register or with memory.

The LDUD and LDUP instructions are available for loading to or from the user-mode memory address space while executing in system mode. The CPU flags are used to indicate if the transfer was successfully completed. LDUD and LDUP are privileged instructions. The other instructions in this group do not affect the flags, nor can their execution cause exception conditions.


| Instruction Name     | Format   | R | RX | IM | IR | DA | X | SX | RA | SR | BX |
|-|-|-|-|-|-|-|-|-|-|-|-|
| Exchange Accumulator | EX A,src | • | • |   | • | • | • | • | • | • | • |
| Exchange H,L         | EX H,L
| Load Accumulator     | LD A,src | • | • | • | • | • | • | • | • | • | • |
|                      | LD dst,A | • | • |   | • | • | • | • | • | • | • |
| Load Immediate       | LD dst,n | • | • |   | • | • | • | • | • | • | • |
| Load Register (Byte) | LD R,src | • | • | • | • |   |   | •
|                      | LD dst,R | • | • |   | • |   |   | •
| Load in User Data Space | LDUD A,src |   |   |   | • |   |   | •
|                         | LDUD dst,A |   |   |   | • |   |   | •
| Load in User Program Space | LDUP A,src |   |   |   | • |   |   | •
|                            | LDUP dst.A |   |   |   | • |   |   | •

R ... BX = Addressing Modes Available

_Table 5-2. 8-Bit Load Group Instructions_



### 5.4.2 16-Bit Load and Exchange Group

This group of load and exchange instructions (Table 5-3) allows words of data (two bytes equal one word) to be transferred between registers and memory. The exchange instructions allow for switching between the primary and alternate register files, exchanging the contents of two 16-bit registers, or exchanging the contents of an addressing register with the top word on the stack. The 16-bit loads include transfers between registers and memory and immediate loads of registers or memory. The Load Address instruction facilitates the loading of the address registers with a calculated address. The Push and Pop stack instructions are also included in this group. None of these instructions affect the CPU flags, except for EX AF, AF'. The Push instruction can cause a System Stack Overflow Warning trap; otherwise, no exceptions can arise from the execution of these instructions.


| Instruction Name     | Format   | R | IM | IR | DA | X | SX | RA | SR | BX |
|-|-|-|-|-|-|-|-|-|-|-|
| Exchange HL with Addressing Register | EX DE,HL
|                      | EX XY,HL
| Exchange Addressing Register with Top of Stack | EX (SP),XX
| Exchange Accumulator/Flag with Alternate Bank | EX AF,AF'
| Exchange Byte/Word Registers with Alternate Bank | EXX
| Load Addressing Register  | LD XX,src |   | • |   | • | • |   | • | • | • |
|                           | LD dst,XX |   |   |   | • | • |   | • | • | • |
| Load Register (Word)      | LD RR,src |   | • | • | • |   | • |   |   |   |
|                           | LD dst,RR |   |   | • | • |   | • |   |   |   |
| Load Immediate Word       | LD dst,nn | • |   | • | • |   |   | • 
| Load Stack Pointer        | LD SP,src | * | • | • | • |   | • 
|                           | LD dst,SP |   |   | • | • |   | • 
| Load Address              | LDA XX,src|   |   |   | • | • |   | • | • | • |
| Pop                       | POP dst   | • |   | • | • |   |   | •
| Push                      | PUSH src  | • | • | • | • |   |   | •

<span>*</span> Restricted to an addressing register (HL, IX, or IY)<br/>
R ... BX = Addressing Modes Available

_Table 5-3. 16-Bit Load and Exchange Group Instructions_


### 5.4.3 Block Transfer and Search Group

This group of instructions (Table 5-4) supports block transfer and string search functions. Using these instructions, a block of up to 65,536 bytes can be moved in memory, or a byte string can be searched until a given value is found. All the operations can proceed through the data in either direction. Furthermore, the operations can be repeated automatically while decrementing a length counter until it reaches zero, or they can operate on one storage unit per execution with the length counter decremented by one and the source and destination pointer registers properly adjusted. The latter form is useful for implementing more complex operations in software by adding other instructions within a loop containing the block instructions.

Various Z280 MPU registers are dedicated to specific functions for these instructions: the BC register for a counter, the DE and HL registers for memory pointers, and the accumulator for holding the byte value being sought. The repetitive forms of these instructions are
interruptible; this is essential since the repetition count can be as high as 65,536. The instruction can be interrupted after any iteration, in which case the address of the instruction itself, rather than the next one, is saved on the system stack; the contents of the operand pointer registers, as well as the repetition counter, are such that the instruction can simply be reissued after returning from the interrupt without any visible difference in the instruction execution.

| Instruction Name | Format |
|-|-|
| Compare and Decrement | CPD
| Compare, Decrement and Repeat | CPDR
| Compare and Increment | CPI
| Compare, Increment and Repeat | CPIR
| Load and Decrement | LDD
| Load, Decrement and Repeat | LDDR
| Load and Increment | LDI
| Load, Increment and Repeat | LDIR

_Table 5-4. Block Transfer and Search Group_


### 5.4.4 8-Bit Arithmetic and Logic Group

This group of instructions (Table 5-5) performs 8-bit arithmetic and logical operations. The Add, Add with Carry, Subtract, Subtract with Carry, And, Or, Exclusive Or, Compare, and signed and unsigned Multiply take one input operand from the accumulator and the other from a register, from immediate data in the instruction itself, or from memory. All memory addressing modes are supported: Indirect Register, Short Index, Direct Address,PC Relative Address, Stack Pointer Relative, Indexed, and Base Index. Except for the multiplies, which return the 16-bit result to the HL register, these instructions return the computed result to the accumulator. Both signed
and unsigned division are provided. All memory addressing modes except Indirect Register can be used to specify the divisor.

The Increment and Decrement instructions operate on data in a register or in memory; all memory addressing modes are supported. Three instructions operate only on the accumulator: Decimal Adjust, Complement, and Negate. The final instruction in this group, Extend Sign, takes its 8-bit input from the accumulator and returns its 16-bit result to the HL register.

All these instructions except Extend Sign set the CPU flags according to the computed result. Only the Divide instructions can generate an exception.


| Instruction Name     | Format   | R | RX | IM | IR | DA | X | SX | RA | SR | BX |
|-|-|-|-|-|-|-|-|-|-|-|-|
| Add with Carry (Byte) | ADC A,src | • | • | • | • | • | • | • | • | • | • |
| Add (Byte) | ADD A,src | • | • | • | • | • | • | • | • | • | • |
| And | AND A,src | • | • | • | • | • | • | • | • | • | • |
| Compare (Byte) | CP A,src | • | • | • | • | • | • | • | • | • | • |
| Complement Accumulator | CPL A
| Decimal Adjust Accumulator | DAA A
| Decrement (Byte) | DEC dst | • | • |   | • | • | • | • | • | • | • |
| Divide (Byte) | DIV A,src | • | • | • |   | • | • | • | • | • | • |
| Divide Unsigned (Byte) | DIVU A,src  | • | • | • |   | • | • | • | • | • | • |
| Extend Sign (Byte) | EXTS A
| Increment (Byte) | INC dst | • | • |   | • | • | • | • | • | • | • |
| Multiply (Byte) | MULT A,src | • | • | • | • | • | • | • | • | • | • |
| Multiply Unsigned (Byte) | MULTU A,src | • | • | • | • | • | • | • | • | • | • |
| Negate Accumulator | NEG A
| Or | OR A,src | • | • | • | • | • | • | • | • | • | • |
| Substract With Carry (Byte) | SBC A,src | • | • | • | • | • | • | • | • | • | • |
| Substract (Byte) | SUB A,src | • | • | • | • | • | • | • | • | • | • |
| Exclusive OR | XOR A,src | • | • | • | • | • | • | • | • | • | • |

R ... BX = Addressing Modes Available

_Table 5-5. 8-Bit Arithmetic and Logic Group_


### 5.4.5 16-Bit Arithmetic Operations

This group of instructions (Table 5-6) provides 16-bit arithmetic operations. The Add, Add with Carry, Subtract with Carry, and Compare instructions take one input operand from an addressing register and the other from a 16-bit register or from the instruction itself; the result is returned to the addressing register. The 16-bit Increment and Decrement instructions operate on data found in a register or in memory; the Indirect Register, Direct Address or PC Relative addressing mode can be used to specify the memory operand. The instruction that adds the contents of the accumulator to an addressing register supports the use of signed byte indices into tables or arrays in memory.

The remaining 16-bit instructions provide general arithmetic capability using the HL register as one of the input operands. The word Add, Subtract, Compare, and signed and unsigned Multiply instructions take one input operand from the HL register and the other from a 16-bit register, from the instruction itself, or from memory using Indexed, Direct Address, or Relative addressing mode. The 32-bit result of a multiply is returned to the DE and HL registers, with the DE register containing the most significant bits. The signed and unsigned divide instructions take a 32-bit dividend in the DE and HL registers (the DE register containing the most significant bits) and a 16-bit divisor from a register, from the instruction, or from memory using the Indexed, Direct Address, or Relative addressing mode. The 16-bit quotient is returned to the HL register and the 16-bit remainder is returned to the DE register. The Extend Sign instruction takes the contents of the HL register and delivers the 32-bit result to the DE and HL registers, with the DE register containing the most significant bits of the result. The Negate HL instruction negates
the contents of the HL register.

Except for Increment, Decrement, and Extend Sign, all the instructions in this group set the CPU flags to reflect the computed result. The only instructions that can generate exceptions are the Divide instructions.

| Instruction Name     | Format   | R | IM | IR | DA | X | RA
|-|-|-|-|-|-|-|-|
| Add with Carry (Word) | ADC XX,src | •
| Add (Word) | ADD XX,src | •
| Add Accumulator to Addressing Register | ADD XX,A
| Add Word | ADDW HL,src | • | • |   | • | • | •
| Compare (Word) | CPW HL,src | • | • |   | • | • | •
| Decrement (Word) | DECW dst | • |   | • | • | • | •
| Divide (Word) | DIV DEHL,src | • | • |   | • | • | •
| Divide Unsigned (Word) | DIVU DEHL,src | • | • |   | • | • | •
| Extend Sign (Word) | EXTS HL
| Increment (Word) | INCW dst | • |   | • | • | • | •
| Multiply (Word) | MULT HL,src | • | • |   | • | • | •
| Multiply Unsigned (Word) | MULTU HL,src | • | • |   | • | • | •
| Negate HL | NEG HL
| Substract With Carry (Word) | SBC XX,src | •
| Substract (Word) | SUBW HL,src | • | • |   | • | • | •

R ... RA = Addressing Modes Available

_Table 5-6. 16-Bit Arithmetic Operation Instructions_


### 5.4.6 Bit Manipulation, Rotate ands Shift Group

Instructions in this group (Table 5-7) test, set, and reset bits within bytes and rotate and shift byte data one bit position. Bits to be manipulated are specified by a field within the instruction. Rotation can optionally concatenate the Carry flag to the byte to be manipulated. Both left and right shifting is supported. Right shifts can either shift 0 into bit 7 (logical shifts) or can replicate the sign in bits 6 and 7 (arithmetic shifts). The Test and Set instruction is useful in multiprogramming and multiprocessing environments for implementing synchronization mechanisms between processes. All these instructions except Set Bit and Reset Bit set the CPU flags according to the calculated result; the operand can be a register or a memory location specified by the Indirect Register or Short Index addressing modes.

The RLD and RRD instructions are provided for manipulating strings of BCD digits; these rotate 4-bit quantities in memory specified by the indirect register. The low-order four bits of the accumulator are used as a link between rotations of successive bytes.

None of these instructions generate exceptions.


| Instruction Name     | Format   | R | IR | SX
|-|-|-|-|-|
| Bit Test | BIT dst | • | • | •
| Reset Bit | RES dst | • | • | •
| Rotate Left | RL dst | • | • | •
| Rorate Left Accumulator | RLA
| Rotate Left Circular | RLC dst | • | • | •
| Rotate Left Circular (Accumulator) | RLCA
| Rotate Left Digit | RLD |   | • |  
| Rotate Right | RR dst | • | • | •
| Rotate Right Accumulator | RRA
| Rotate Right Circular | RRC dst | • | • | •
| Rotate Right Circular (Accumulator) | RRCA
| Rotate Right Digit | RRD |   | • |
| Set Bit | SET dst | • | • | •
| Shift Left Arithmetic | SLA dst | • | • | •
| Shift Right Arithmetic | SRA dst | • | • | •
| Shift Right Logical | SRL dst | • | • | •
| Test and Set | TSET dst | • | • | •

R ... SX = Addressing Modes Available

_Table 5-7. Bit Manipulation, Rotate and Shift Group_


### 5.4.7 Program Control Group

This group (Table 5-8) consists of the instructions that affect the Program Counter (PC) and thereby control program flow. The CPU registers and memory are not altered except for the Stack Pointer and the stack, which play a significant role in procedures and interrupts. (An exception is Decrement and Jump if Non-Zero [DJNZ], which uses a register as a loop counter.) The flags are also preserved except for the two instructions specifically designed to set and complement the Carry flag.

The Jump (JP) and Jump Relative (JR) instructions provide a conditional transfer of control to a new location if the processor flags satisfy the condition specified in the instruction. Jump Relative is a 2-byte instruction that jumps to any instruction within the range -126 to +129 bytes from the location of this instruction. Most conditional jumps in programs are made to locations only a few bytes away; the Jump Relative instruction exploits this fact to improve code compactness and efficiency.

A special Jump instruction tests whether the primary or auxiliary register file is being used and branches if the auxiliary file is in use. In systems that reserve the auxiliary register file for interrupt handlers only (via a software convention), this instruction can be used to decide whether registers must be saved.

Call and Restart are used for calling subroutines; the current contents of the PC are pushed onto the processor stack and the effective address indicated by the instruction is loaded into the PC. The use of a procedure address stack in this manner allows straightforward implementation of nested and recursive procedures. Call, Jump, and Jump Relative can be unconditional or based on the setting of a CPU flag.

Jump and Call instructions are available with the Indirect Register and PC Relative Address modes in addition to the Direct Address mode. These can be useful for implementing complex control structures such as dispatch tables. When using Direct Address mode for a Jump or Call, the operand is used as an immediate value that is loaded into the PC to specify the address of the next instruction to be executed.

The conditional Return instruction is a companion to the Call instruction; if the condition specified in the instruction is satisfied, it loads the PC from the stack and pops the stack.

A special instruction, Decrement and Jump if Non-Zero (DJNZ), implements the control part of the basic Pascal FOR loop in a one-word instruction.

System Call (SC) is used for controlled access to facilities provided by the operating system. It is implemented identically to a trap or interrupt in interrupt mode 3: the current program status is pushed onto the system stack, and a new program status is loaded from a dedicated part of memory.


| Instruction Name     | Format   | IR | DA | RA
|-|-|-|-|-|
| Call | CALL cc,dst | • | • | •
| Complement Carry Flag | CCF
| Decrement and Jump if Non-Zero | DJNZ dst |   |   | •
| Jump on Auxiliary Accumulator/Flag | JAF dst |   |   | •
| Jump on Auxiliary Register File in Use | JAR dst |   |   | •
| Jump | JP cc,dst | • | • | •
| Jump Relative | JR cc,dst |   |   | •
| Return | RET cc
| Restart | RST p
| System Call | SC nn
| Set Carry Flag | SCF

IR ... RA = Addressing Modes Available

_Table 5-8. Program Control Group Instructions_


### 5.4.8 Input/Output Instruction Group

This group (Table 5-9) consists of instructions for transferring a byte, a word, or a string of bytes or words between peripheral devices and the CPU registers or memory. Byte I/O port addresses transfer bytes on AD<sub>0</sub>-AD<sub>7</sub> only. Thus in a 16-bit data bus environment, 8-bit peripherals must be connected to bus lines AD<sub>0</sub>-AD<sub>7</sub>. In an 8-bit data bus environment, word I/O instructions to external peripherals should not be used; however, on-chip peripherals can still be accessed by word I/O instructions.

The instructions for transferring a single byte (IN, OUT) can transfer data between any 8-bit CPU reqister or memory address specified in the instruction and the peripheral port specified by the contents of the C reqister. The IN instruction sets the CPU flags according to the input data; however, special cases of these instructions, restricted to using the CPU accumulator and Direct Address mode, do not affect the CPU flags. Another variant tests an input port specified by the contents of the C register and sets the CPU flags without modifying CPU reqisters or memory.

The instructions for transferring a single word (INW, OUTW) can transfer data between the HL register and the peripheral port specified by the contents of the C reqister. For word I/O, the contents of H appear on AD<sub>0</sub>-AD<sub>7</sub> and the contents of L appear as AD<sub>8</sub>-AD<sub>15</sub>. These instructions do not affect the CPU flags.

The remaining instructions in this qroup form a powerful and complete complement of instructions for transferring blocks of data between I/O ports and memory. The operation of these instructions is very similar to that of the block move instructions described earlier, with the exception that one operand is always an I/O port whose address remains unchanged while the address of the other operand (a memory location) is incremented or decremented. Both byte and word forms of these instructions are available. The automatically repeating forms of these instructions are interruptible.

I/O instructions are not privileged if the Inhibit User I/O bit in the Trap Control register is clear; they can be executed in either system or user mode, so that I/O service routines can execute in user mode. The Memory Management Unit and on-chip peripherals' control and status registers are accessed using the I/O instructions. The contents of the I/O Page register are output on AD<sub>23</sub>-AD<sub>16</sub> with the I/O port address and can be used by external decoding to select specific devices. Pages FF and FE are reserved for on-chip I/O and no external bus transaction is generated. I/O devices can be protected from unrestricted access by using the I/O Page register to select among I/O peripherals.


| Instruction Name | Format |
|-|-|
Input| IN dst,(C)
Input Accumulator | IN A(n)
Input HL | INW HL,(C)
Input and Decrement (Byte) | IND
Input and Decrement (Word) | INDW
Input, Decrement and Repeat (Byte) | INDR
Input, Decrement and Repeat (Word) | INDRW
Input and Increment (Byte) | INI
Input and Increment (Word) | INIW
Input, Increment and Repeat (Byte) | INIR
Input, Increment and Repeat (Word) | INIRW
Output | OUT (C),src
Output Accumulator | OUT (n),A
Output HL | OUTW (C),HL
Output and Decrement (Byte) | OUTD
Output and Decrement (Word) | OUTDW
Output, Decrement and Repeat (Byte) | OTDR
Output, Decrement and Repeat (Word) | OTDRW
Output and Increment (Byte) | OUTI
Output and Increment (Word) | OTIRW
Output, Increment and Repeat (Byte) | OTIR
Output, Increment and Repeat (Word) | OTIRW
Test Input |TSTI (C)

_Table 5-9. Input/Output Instruction Group Instructions_


### 5.4.9 CPU Control Group

The instructions in this group (Table 5-10) act upon the CPU control and status registers or perform other functions that do not fit into any of the other instruction groups. There are three instructions used for returning from an interrupt or trap service routine. Return from Nonmaskable Interrupt (REIN) and Return from Interrupt (RETI) are used in interrupt modes 0, 1, and 2 to pop the Program Counter from the stack and manipulate the Interrupt Mask reqister, or to signal a reset to Z8400 Family peripherals. The Return from Interrupt Long (RETIL) instruction pops a 4-byte program status from the System stack, and is used in interrupt mode 3 and trap processing.

Two of these instructions are not privileged: No Operation (NOP) and Purge Cache (PCACHE). The remaining instructions are privileged.

| Instruction Name | Format |
|-|-|
Disable Interrupt| DI mask
Enable Interrupt | EI mask
Halt |HALT
Interrupt Mode Select | IM p
Load Accumulator From I or R Register | LD A,src
Load I or R Register From Accumulator | LD dst,A
Load Control | LDCTL dst,src
No Operation | NOP
Purge Cache | PCACHE
Return From Interrupt | RETI
Return From Interrupt Long | RETIL
Return From Nonmaskable Interrupt | RETN

_Table 5-10. CPU Control Group_


### 5.4.10 Extended Instruction Group

The Z280 MPU architecture contains a powerful mechanism for extending the basic instruction set through the use of external co-processors called Extended Processing Units (EPUs). A group of 22 opcodes is dedicated for the implementation of extended instructions using this facility. The extended instructions (Table 5-11) are intended for use on a 16-bit data bus; thus, this facility is available only on the Z-BUS configuration of the Z280 MPU.

There are four types of extended instructions in the Z280 MPU instruction set: EPU internal operations, data transfers from an EPU to memory, data transfers from memory to an EPU, and data transfers between an EPU and the CPU's accumulator. The extended instructions that access memory can use any of the six basic memory addressing modes (Indexed, Base Index, PC Relative, SP Relative, Indirect Register, and Direct Address). Transfers between the EPU and CPU accumulator are useful when the program must branch based on conditions generated by an EPU operation.

A 4-byte long "template" is embedded in each of the extended instruction opcodes. These templates determine the operation to be performed in the EPU itself. The formats of these templates are described in the following pages. The descriptions are from the point of view of the CPU; that is, only CPU activities are described. The operation of the EPU is implied, but the full specification of the instruction template depends on the implementation of the EPU, and is beyond the scope of this manual. Fields in the template that are ignored by the CPU are indicated by asterisks, and would typically contain opcodes that determine any operation to be performed by the EPU in addition to the data transfers specified by the instruction. A 2-bit identification field is included in each template, for use in selecting one of up to four EPUs in a multiple-EPU system.

The action taken by the CPU upon encountering an extended instruction depends upon the EPA control bit in the CPU's Trap Control reqister. When this bit is set to 1, indicating that EPUs are included in the system, extended instructions are executed. If this bit is cleared to 0, indicating that there are no EPUs in the system, the CPU executes an extended instruction trap whenever an extended instruction is encountered; this allows a trap service routine to emulate the desired operation in software.

| Instruction Name | Format
|-|-|
Load EPU From Memory | EPUM src
Load Memory From EPU | MEPU dst
Load Accumulator From EPU | EPUF
EPU Internal Operation | EPUI

_Table 5-11. Extended Instructions_


## 5.5 NOTATION AND BINARY ENCODING

The rest of this chapter consists of detailed descriptions of the Z280 MPU instructions, arranged in alphabetical order by mnemonic. This section describes the notational conventions used in the instruction descriptions and the binary encoding for register fields within instruction's operation codes (opcodes).

The description of each instruction begins on a new page. The instruction mnemonic and name is printed in bold letters at the top of each page to enable the reader to easily locate a desired description. The assembly language syntax is then given in a single generic form that covers all the variants of the instruction, along with a list of applicable addressing modes. This is followed by a description of the operstion performed by the instruction, a listing of all the flags that are affected by the instruction, a listing of exception conditions that may be caused by execution of the instruction, illustrations of the opcodes for all variants of the instruction, and a simple example of the use of the instruction.

The following notation is used throughout the descriptions of the instructions:

| | |
|-|-|
(addr) |  A direct address
&lt;addr&gt; | An address to be encoded using relative addressing
b | A 3-bit field specifying the position of a bit within a byte
BX | Base Index addressing mode
cc | A condition code specifying whether a flag is set to 1 or cleared to 0
d | An 8-bit signed displacement
DA | Direct Address addressing mode
dd | A 16-bit signed displacement
disp | The displacement calculated from the address in relative addressing
dst | Destination location or contents
IM | Immediate addressing mode
IR | Indirect Register addressing mode
MSR | The Master Status register
n | 8-bit immediate data
nn | 16-bit immediate data
p | An interrupt mode
PC | The Program Counter
PS | The program status registers (the Program Counter and Master Status register)
R | A single 8-bit register of the set (A,B,C,D,E,H,L); also, R1 and R2 are used when two different registers are specified in the same instruction. (Note that the R register itself is accessed by a single instruction and violates this convention.)
R' | The corresponding 8-bit or 16-bit register in the alternate register file, such as A'
RA | PC Relative Address addressing mode
RR | A 16-bit register of the set (BC,DE,HL,SP); also, RRA and RRB are used when two different registers are specified in the same instruction
RX | A single byte in the IX or IY registers; that is, a register in the set (IXH,IXL, IYH,IYL); also, RXA and RXB are used when two different registers are specified in the same instruction
SP | The current Stack Pointer in use
SR | Stack Pointer Relative addressing mode
src | Source location or contents
SX | Short Index addressing mode
USP | The User Stack Pointer
X | Indexed addressing mode
XX | One of the 16-bit addressing registers HL, IX, or IY; also XXA and XXB are used when two different registers are specified in the same instruction
XY | One of the 16-bit index registers IX or IY

In the binary encoding of the instruction, lower case is used for the corresponding encoding of the assembler syntax.

Brackets ([ and ]) are used in the assembly language syntax to indicate an optional field. For example, the 16-bit addition instruction for adding word data to the HL register is described as:

```
ADDW [HL,]src
``````
This format means the instruction can be written as:

```
ADDW HL,src
```
or
```
ADDW src
```

Assignment of a value is indicated by the symbol "<—". For example,

```
dst <— dst + src
```

indicates that the source data is added to the destination data and the result is stored in the destination location.

The notation "addr(n)" is used to refer to bit "n" of 8 given location, for example, dst(7).

The register field in the binary encoding of an instruction opcode is encoded as shown in Table 5-12.

| Register | Encoding
|-|-|
A |111
B | 000
C | 001
D | 010
E | 011
H | 100
L | 101

_Table 5-12. Encoding of 8-Bit Registers in Instruction Opcodes_


The remainder of this chapter consists of the individual descriptions of each Z280 MPU instruction.