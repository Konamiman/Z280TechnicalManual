# Appendix E. Instruction Timing

## Index

[INTRODUCTION](#introduction)

[E.1. INSTRUCTION EXECUTION TIMES](#e1-instruction-execution-times)

- [E.1.1. 8-Bit Load Group](#e11-8-bit-load-group)
- [E.1.2. 16-Bit Load Group](#e12-16-bit-load-group)
- [E.1.3. Block Transfer and Search Group](#e13-block-transfer-and-search-group)
- [E.1.4. 8-Bit Arithmetic and Logic Group](#e14-8-bit-arithmetic-and-logic-group)
- [E.1.5. 16-Bit Arithmetic and Logic Group](#e15-16-bit-arithmetic-and-logic-group)
- [E.1.6. Bit Manipulation, Rotate and Shift Group](#e16-bit-manipulation-rotate-and-shift-group)
- [E.1.7. Program Control Group](#e17-program-control-group)
- [E.1.8. Input/Output Instruction Group](#e18-inputoutput-instruction-group)
- [E.1.9. CPU Control Group](#e19-cpu-control-group)

[E.2. EXTENDED INSTRUCTION EXECUTION TIMES](#e2-extended-instruction-execution-times)

[E.3. INTERRUPT, TRAP, AND SPECIAL CONDITION EXECUTION TIMES](#e3-interrupt-trap-and-special-condition-execution-times)

[E.4. INSTRUCTION FETCH AND DECODE TIMING](#e4-instruction-fetch-and-decode-timing)

[E.5. DATA READ TIMING](#e5-data-read-timing--rdsrc-rddst-and-rdir)

[E.6. DATA WRITE TIMING](#e6-data-write-timing--wrsrc-wrdst-and-wrir)

[E.7. I/O READ AND WRITE TIMING](#e7-io-read-and-write-timing)

[E.8. EPU READ AND WRITE TIMING](#e8-epu-read-and-write-timing)

[E.9. INTERRUPT ACKNOWLEDGE TIMING](#e9-interrupt-acknowledge-timing)

[E.10. MISCELLANEOUS TRANSACTION TIMING](#e10-miscellaneous-transaction-timing)

## INTRODUCTION

The Z280 CPU processes instructions using a three-stage pipeline consisting of an instruction prefetch unit, an instruction decoder, and an instruction execution unit. Each section of the pipeline operates autonomously, communicating with the other stages of the pipeline via handshakes and local buses. The pipelined architecture of the Z280 MPU greatly increases program throughput; as one instruction is being executed, the next instruction can be decoded, and the instruction after that can be prefetched.

The autonomous operation of the three stages in the Z280 CPU instruction pipeline makes it difficult to calculate exact instruction execution times. Furthermore, execution times are affected by cache activity; the current cache contents determine the number of external memory transactions made during the fetch and execution of a given instruction. In this appendix, three types of tables are provided for calculation of instruction timings: instruction execution timing, instruction fetch and decode timing, and bus transaction timing. All tables list execution and transaction timings in terms of CPU clock cycles.

Tables [E-1](#e1-instruction-execution-times), [E-2](#e2-extended-instruction-execution-times), and [E-3](#e3-interrupt-trap-and-special-condition-execution-times) show the execution times for all instructions and interrupt and trap processing. Table [E-1](#e1-instruction-execution-times) lists the execution times for all CPU-executed instructions, with the instructions listed by functional group. Table [E-2](#e2-extended-instruction-execution-times) lists the execution times for the extended instructions. Table [E-3](#e3-interrupt-trap-and-special-condition-execution-times) shows execution times for interrupt and trap events. These tables assume that the instruction has been fetched, decoded, and is ready for execution, and that the bus is idle when the execution unit makes a request for a transaction. Thus, the execution times shown in these tables represent the maximum execution rate of the machine. The actual execution rate will be somewhat lower than this maximum for two reasons: (1) the execution unit must compete with the prefetch unit for use of the external bus, and (2) some instructions may take longer to prefetch and decode than the previous instruction will take to execute.

Furthermore, the activity of the execution unit can affect the prefetch unit when certain instructions are executed. In Tables [E-1](#e1-instruction-execution-times) and [E-2](#e2-extended-instruction-execution-times), an "F" on the right-hand side of the table indicates that the pipeline is flushed when that instruction is executed; the pipeline is also flushed during all interrupt and trap processing.

In these cases, the next instruction must be completely fetched and decoded before the execution unit can proceed. The execution times in these tables do not include the time necessary to fetch and decode the next instruction if the pipeline is flushed.

In Tables [E-1](#e1-instruction-execution-times) through [E-3](#e3-interrupt-trap-and-special-condition-execution-times), execution times are given as the number of absolute CPU clock cycles plus the number and type of bus transactions. Bus transaction timings are shown separately in Tables [E-5](#e5-data-read-timing--rdsrc-rddst-and-rdir) through [E-10](#e10-miscellaneous-transaction-timing); the average would be half of the sum of the minimum and maximum numbers listed in the parentheses. The notation "w" in these tables refers to the number of wait states added to the transaction (either by asserting the <ins>WAIT</ins> input or by programming the appropriate CPU control registers) in addition to any automatically inserted wait states. Again, the numbers in these tables assume that the bus is idle when the transaction request is made.

Table [E-4](#e4-instruction-fetch-and-decode-timing) contains the instruction fetch and decode timing, and Tables [E-5](#e5-data-read-timing--rdsrc-rddst-and-rdir) through [E-10](#e10-miscellaneous-transaction-timing) show bus transaction timings. The CPU clock is divided by a factor of 1, 2, or 4 to form the bus clock; thus, bus transaction timing depends on the relationship between the CPU clock and bus clock. All three types of bus timing are shown in the tables. Furthermore, because of the different phase relationships between the request for a transaction and the bus clock, a variable number of cycles can be included in parentheses in Tables [E-4](#e4-instruction-fetch-and-decode-timing) through [E-10](#e10-miscellaneous-transaction-timing); the average would be half of the sum of the minimum and maximum numbers listed in the parentheses.

## E.1. INSTRUCTION EXECUTION TIMES

### E.1.1. 8-Bit Load Group

| Instruction | Addressing Modes | Execution Time |
|-------------|------------------|----------------|
| EX A,src | src = R,RX,IR,DA,X,SX<br/>RA,SR,BX | R,RX: 4<br/>IR,DA,X,SX,RA,SR,BX: 5 + rd(src) + wr(src) |
| EX H,L | | 4 |
| LD dst,src | dst = A<br/>src = R,RX,IM,IR,DA,X<br/>SX,RA,SR,BX<br/>(BC),(DE) | R,RX: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + rd(src)<br/>(BC),(DE): 3 + rd(IR) |
| | **or** | |
| | dst = R,RX,IR,DA,X<br/>SX,RA,SR,BX<br/>(BC),(DE)<br/>src = A | R,RX,IM: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + wr(dst)<br/>(BC),(DE): 3 + wr(IR) |
| LD dst,src | dst = R<br/>src = R,RX,IM,IR,SX | R,RX,IM: 2<br/>IR,SX: 3 + rd(src) |
| | **or** | |
| | dst = R,RX,IR,SX<br/>src = R | R,RX: 2<br/>IR,SX: 3 + wr(dst) |
| LD dst,n | dst = R,RX,IR,DA,X,<br/>SX,RA,SR,BX | R,RX: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + wr(dst) |
| LDUD dst,src | dst = A<br/>src = IR,SX in user | 3 + rd(src) |
| | **or** | |
| | dst = IR,SX in user<br/>src = A | 3 + wr(dst) |
| LDUP dst,src | dst = A<br/>src = IR,SX in user | 3 + rd(src) |
| | **or** | |
| | dst = IR,SX in user<br/>src = A | 3 + wr(dst) |

### E.1.2. 16-Bit Load Group

| Instruction | Addressing Modes | Execution Time |
|-------------|------------------|----------------|
| EX src,HL | src = DE,IX,IY | 5 |
| EX (SP),dst | dst = HL,IX,IY | 5 + rd(IR) + wr(IR) |
| EX AF,AF' | | 2 |
| EXX | | 2 |
| LD[W] dst,src | dst = HL,IX,IY<br/>src = IM,DA,X,RA,SR,BX | IM: 2<br/>DA,X,RA,SR,BX: 3 + rd(src) |
| | **or** | |
| | dst = DA,X,RA,SR,BX<br/>src = HL,IX,IY | 3 + wr(dst) |
| LD[W] dst,src | dst = BC,DE,HL,SP<br/>src = IM,IR,DA,SX | IM: 2<br/>IR,DA,SX: 3 + rd(src) |
| | **or** | |
| | dst = IR,DA,SX<br/>src = BC,DE,HL,SP | 3 + wr(dst) |
| LD[W] dst,nn | dst = RR,IR,DA,RA | RR: 2<br/>IR,DA,RA: 3 + wr(dst) |
| LD[W] dst,nn | dst = RR | 2 |
| LD[W] dst,src | dst = SP<br/>src = HL,IX,IY,IM,IR,DA,SX | HL,IX,IY,IM: 2<br/>IR,DA,SX: 3 + rd(src) |
| | **or** | |
| | dst = IR,DA,SX<br/>src = SP | 3 + wr(dst) |
| LDA dst,src | dst = HL,IX,IY<br/>src = DA,X,RA,SR,BX | DA: 2<br/>X,RA,SR: 5<br/>BX: 6 |
| POP dst | dst = RR,IR,DA,RA | RR: 9 + rd(IR)<br/>IR,DA,RA: 9 + rd(IR) + wr(dst) |
| PUSH src | src = RR,IM,IR,DA,RA | RR,IM: 8 + wr(IR)<br/>IR,DA,RA: 9 + rd(src) + wr(IR) |

### E.1.3. Block Transfer and Search Group

| Instruction | Execution Time |
|-------------|----------------|
| CPD | 8 + rd(IR) |
| CPDR | 8 + rd(IR), each iteration |
| CPI | 8 + rd(IR) |
| CPIR | 8 + rd(IR), each iteration |
| LDD | 9 + rd(IR) + wr(IR) |
| LDDR | 9 + rd(IR) + wr(IR), each iteration |
| LDI | 9 + rd(IR) + wr(IR) |
| LDIR | 9 + rd(IR) + wr(IR), each iteration |

### E.1.4. 8-Bit Arithmetic and Logic Group

| Instruction | Addressing Modes | Execution Time |
|-------------|------------------|----------------|
| ADC [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + rd(src) |
| ADD [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + rd(src) |
| AND [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + rd(src) |
| CP [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + rd(src) |
| CPL [A] | | 2 |
| DAA [A] | | 3 |
| DEC dst | dst = R,RX,IR,DA,X,<br/>SX,RA,SR,BX | R,RX: 3<br/>IR,DA,X,SX,RA,SR,BX: 4 + rd(dst) + wr(dst) |
| DIV [HL,]src | src = R,RX,IM,DA,X,<br/>SX,RA,SR,BX | R,RX,IM: 46<br/>4 if divide by zero<br/>20 if overflow<br/>DA,X,SX,RA,SR,BX: 47 + rd(src)<br/>5 + rd(src) if divide by zero<br/>21 + rd(src) if overflow |
| DIVU [HL,]src | src = R,RX,IM,DA,X,<br/>SX,RA,SR,BX | R,RX,IM: 34<br/>4 if divide by zero<br/>13 if overflow<br/>DA,X,SX,RA,SR,BX: 35 + rd(src)<br/>5 + rd(src) if divide by zero<br/>14 + rd(src) if overflow |
| EXTS [A] | | 4 |
| INC dst | dst = R,RX,IR,DA,X,<br/>SX,RA,SR,BX | R,RX: 3<br/>IR,DA,X,SX,RA,SR,BX: 4 + rd(dst) + wr(dst) |
| MULT [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 17*<br/>IR,DA,X,SX,RA,SR,BX: 18 + rd(src)*<br/>* add 1 if src < 0 |
| MULTU [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 17<br/>IR,DA,X,SX,RA,SR,BX: 18 + rd(src) |
| NEG [A] | | 3 |
| OR [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + rd(src) |
| SBC [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + rd(src) |
| SUB [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + rd(src) |
| XOR [A,]src | src = R,RX,IM,IR,DA,<br/>X,SX,RA,SR,BX | R,RX,IM: 2<br/>IR,DA,X,SX,RA,SR,BX: 3 + rd(src) |

### E.1.5. 16-Bit Arithmetic and Logic Group

| Instruction | Addressing Modes | Execution Time |
|-------------|------------------|----------------|
| ADC dst,src | dst = HL<br/>src = BC,DE,HL,SP | 3 |
| | **or** | |
| | dst = IX<br/>src = BC,DE,IX,SP | 3 |
| | **or** | |
| | dst = IY<br/>src = BC,DE,IY,SP | 3 |
| ADD dst,src | dst = HL<br/>src = BC,DE,HL,SP | 3 |
| | **or** | |
| | dst = IX<br/>src + BC,DE,IX,SP | 3 |
| | **or** | |
| | dst = IY<br/>src = BC,DE,IY,SP | 3 |
| ADD dst,A | dst = HL,IX,IY | 3 |
| ADDW [HL,]src | src = RR,IM,DA,X,RA | RR,IM: 3<br/>DA,X,RA: 3 + rd(src) |
| CPW [HL,]src | src = RR,IM,DA,X,RA | RR,IM: 3<br/>DA,X,RA: 3 + rd(src) |
| DECW dst | dst = RR,IR,DA,X,RA | RR: 3<br/>IR,DA,X,RA: 4 + rd(dst) + wr(dst) |
| DEC[W] dst | dst = RR | 3 |
| DIVUW [DEHL,]src | src = RR,IM,DA,X,RA | RR,IM: 51<br/>4 if divide by zero<br/>13 if overflow<br/>DA,X,RA: 52 + rd(src)<br/>5 + rd(src) if divide by zero<br/>14 + rd(src) if overflow |
| DIVW [DEHL,]src | src = RR,IM,DA,X,RA | RR,IM: 63<br/>4 if divide by zero<br/>20 if overflow<br/>DA,X,RA: 64 + rd(src)<br/>5 + rd(src) if divide by zero<br/>21 + rd(src) if overflow |
| EXTS HL | | 4 |
| INCW dst | dst = RR,IR,DA,X,RA | RR: 3<br/>IR,DA,X,RA: 4 + rd(dst) + wr(dst) |
| INC[W] dst | dst = RR | 3 |
| MULTUW [HL,]src | src = RR,IM,DA,X,RA | RR,IM: 24*<br/>DA,X,RA: 25 + rd(src)*<br/>* add 1 if src < 0 |
| MULTW [HL,]src | src = RR,IM,DA,X,RA | RR,IM: 24<br/>DA,X,RA: 25 + rd(src) |
| NEG HL | | 3 |
| SBC dst,src | dst = HL<br/>src = BC,DE,HL,SP | 3 |
| | **or** | |
| | dst = IX<br/>src = BC,DE,IX,SP | 3 |
| | **or** | |
| | dst = IY<br/>src = BC,DE,IY,SP | 3 |
| SUBW [HL,]src | src = RR,IM,DA,X,RA | RR,IM: 3<br/>DA,X,RA: 3 + rd(src) |

### E.1.6. Bit Manipulation, Rotate and Shift Group

| Instruction | Addressing Modes | Execution Time |
|-------------|------------------|----------------|
| BIT b,dst | dst = R,IR,SX | R: 2<br/>IR,SX: 3 + rd(dst) |
| RES b,dst | dst = R,IR,SX | R: 2<br/>IR,SX: 4 + rd(dst) + wr(dst) |
| RL dst | dst = R,IR,SX | R: 2<br/>IR,SX: 4 + rd(dst) + wr(dst) |
| RLA | | 2 |
| RLC dst | dst = R,IR,SX | R: 2<br/>IR,SX: 4 + rd(dst) + wr(dst) |
| RLCA | | 2 |
| RLD | | 5 + rd(IR) + wr(IR) |
| RR dst | dst = R,IR,SX | R: 2<br/>IR,SX: 4 + rd(dst) + wr(dst) |
| RRA | | 2 |
| RRC dst | dst = R,IR,SX | R: 2<br/>IR,SX: 4 + rd(dst) + wr(dst) |
| RRCA | | 2 |
| RRD | | 5 + rd(IR) + wr(IR) |
| SET b,dst | dst = R,IR,SX | R: 2<br/>IR,SX: 4 + rd(dst) + wr(dst) |
| SLA dst | dst = R,IR,SX | R: 2<br/>IR,SX: 4 + rd(dst) + wr(dst) |
| SRA dst | dst = R,IR,SX | R: 2<br/>IR,SX: 4 + rd(dst) + wr(dst) |
| SRL dst | dst = R,IR,SX | R: 2<br/>IR,SX: 4 + rd(dst) + wr(dst) |
| TSET dst | dst = R,IR,SX | R: 3<br/>IR,SX: 1 + rd(dst) + wr(dst) |

### E.1.7. Program Control Group

| Instruction | Addressing Modes | Execution Time | F |
|-------------|------------------|----------------|---|
| CALL cc,dst | dst = IR,DA,RA | cc not true: 3<br/>IR,DA: 11 + wr(IR)<br/>RA: 12 + wr(IR) | F*<br/>F |
| CALL dst | dst = IR,DA,RA | IR,DA: 11 + wr(IR)<br/>RA: 12 + wr(IR) | F<br/>F |
| CCF | | 2 | |
| DJNZ dst | dst = RA | B is zero: 6<br/>B is non-zero: 7 | F |
| JAF dst | dst = RA | AF' not in use: 3<br/>AF' in use: 4 | F |
| JAR dst | dst = RA | Alternate file not in use: 3<br/>Alternate file in use: 4 | F |
| JP cc,dst | dst = IR,DA,RA | cc not true: 3<br/>cc true: 4 | F |
| JP dst | dst = IR,DA,RA | 4 | F |
| JR cc,dst | dst = RA | cc not true: 3<br/>cc true: 4 | F |
| JR dst | dst = RA | 4 | F |
| RET | | 9 + rd(IR) | F |
| RET cc | | cc not true: 3<br/>cc true: 9 + rd(IR) | F |
| RST dst | dst = DA | 9 + wr(IR) | F |
| SC nn | | 1 + System Call Trap | |
| SCF | | 2 | |

**\* "F" indicates that the pipeline is flushed when that instruction is executed.**

### E.1.8. Input/Output Instruction Group

| Instruction | Addressing Modes | Execution Time |
|-------------|------------------|----------------|
| IN dst,(C) | dst = R,RX,DA,X,RA,SR,BX | R,RX: 3 + in()<br/>DA,X,RA,SR,BX: 4 + in() + wr(dst) |
| IN A,(n) | | 5 + in() |
| IN[W] HL,(C) | | 3 + in() |
| IND | | 8 + in() + wr(IR) |
| INDW | | 8 + in() + wr(IR) |
| INDR | | 8 + in() + wr(IR), each iteration |
| INDRW | | 8 + in() + wr(IR), each iteration |
| INI | | 8 + in() + wr(IR) |
| INIW | | 8 + in() + wr(IR) |
| INIR | | 8 + in() + wr(IR), each iteration |
| INIRW | | 8 + in() + wr(IR), each iteration |
| OUT (C),src | src = R,RX,DA,X,RA,SR,BX | R,RX: 3 + out()<br/>DA,X,RA,SR,BX: 3 + rd(src) + out() |
| OUT (n),A | | 5 + out() |
| OUT[W] (C),HL | | 3 + out() |
| OUTD | | 8 + rd(IR) + out() |
| OUTDW | | 8 + rd(IR) + out() |
| OTDR | | 8 + rd(IR) + out(), each iteration |
| OTDRW | | 8 + rd(IR) + out(), each iteration |
| OUTI | | 8 + rd(IR) + out() |
| OUTIW | | 8 + rd(IR) + out() |
| OTIR | | 8 + rd(IR) + out(), each iteration |
| OTIRW | | 8 + rd(IR) + out(), each iteration |
| TSTI (C) | | 3 + in() |

### E.1.9. CPU Control Group

| Instruction | Addressing Modes | Execution Time | F |
|-------------|------------------|----------------|---|
| DI mask | mask = Hex value | 3 + out() | |
| EI mask | mask = Hex value | 3 + out() | |
| HALT | | 11 + rd(halt) minimum | |
| IM p | p = 0,1,2,3 | 3 | |
| LD dst,src | dst = A<br/>src = I,R | 2 | |
| LD dst,src | dst = I,R<br/>src = A | 2 | |
| LDCTL dst,src | dst = (C),USP<br/>src = HL,IX,IY | (C): 4 + out()<br/>USP: 2 | F* |
| | **or** | | |
| | dst = HL,IX,IY<br/>src = (C),USP | (C): 3 + in()<br/>USP: 2 | |
| NOP | | 2 | |
| PCACHE | | 2 | F |
| RETI | | Z-BUS: 8 + rd(IR)<br/>Z80: 8 + rd(reti) + rd(IR) | F<br/>F |
| RETIL | | 14 + 2*rd(IR) + out() | F |
| RETN | | 7 + rd(IR) | F |

**\* "F" indicates that the pipeline is flushed when that instruction is executed.**

<a id="notes-e1"></a>
#### NOTES:

1. This table assumes that the instruction has been fetched, decoded, and is ready for execution. The execution time for instructions that require the pipeline to be flushed do not include the time necessary to fetch and decode the following instruction.

2. This table assumes that the PAUSE input is inactive. If PAUSE is active, the execution unit will wait before beginning the next instruction.

3. The bus is assumed to be idle when the execution unit makes a request for a transaction.

4. This table assumes that no exceptions occur during instruction execution except where indicated.

## E.2. EXTENDED INSTRUCTION EXECUTION TIMES

### Extended Instruction Group Template Fetch (EPU Enable Bit Set to 1)

| Instruction | Addressing Modes | Execution Time | F |
|-------------|------------------|----------------|---|
| Aligned template | | 7 + epu(f1) + epu(fn) + out() | |
| Unaligned template | | 7 + epu(f1) + 2*epu(fn) + out() | |

### Extended Instruction Group

| Instruction | Addressing Modes | Execution Time | F |
|-------------|------------------|----------------|---|
| EPUI (Internal Operation) | | 4 + p | F* |
| EPUF (CPU←EPU) | | 6 + p + epu(cpu) | F |
| MEPU dst (Memory←EPU) | dst = IR,DA,X,RA,SR,BX | 5 + p + k*[3 + epu(wr)] | F |
| EPUM src (EPU←Memory) | src = IR,DA,X,RA,SR,BX | 5 + p + k*[3 + epu(rd)] | F |

**\* "F" indicates that the pipeline is flushed when that instruction is executed.**

#### NOTES:

1. Additional cycles are necessary for address computation in the case of EPU-to-Memory and Memory-to-EPU instructions, as shown below:

   - IR,DA: no additional cycles
   - X,SX,RA,SR: 1 additional cycle
   - BX: 2 additional cycles

2. The notation "p" in the table is the number of pause cycles added to the bus cycle.

3. The notation "k" in the table is a function of n, the number of bytes to be transferred that is specified in the template, and the address of the source or destination as shown below:

   - n is odd: k = (n + 1)/2
   - n is even and aligned: k = n/2
   - n is even and unaligned: k = (n = 2)/2

4. See ["Notes" from Table E-1](#notes-e1).

## E.3. INTERRUPT, TRAP, AND SPECIAL CONDITION EXECUTION TIMES

### INTERRUPTS

| Type | Execution Time |
|------|----------------|
| NMI in Modes 0, 1, 2 | 13 + iack(nmi012) + in() + out() + wr(IR) |
| Mode 0 | 9 + out() + [iack(m0) for each byte of opcode] |
| Mode 1 | 13 + iack(m1) + in() + wr(IR) + out() |
| Mode 2 | 16 + iack(m2) + in() + wr(IR) + rd(IR) + out() |
| Mode 3 Nonvectored | 28 + iack(m3) + in() + 3*wr(IR) +2*rd(IR) + out() |
| Mode 3 Vectored | 31 + iack(m3) + in() + 3*wr(IR) +2*rd(IR) + out() |
| On-Chip (Mode 3) | 28 + iack(m3) + in() + 3*wr(IR) +2*rd(IR) + out() |

### TRAPS

| Type | Execution Time |
|------|----------------|
| Single-Step | 26 + in() +2*wr(IR) + 2*rd(IR) + out() |
| Breakpoint-on-Halt | 26 + in() +2*wr(IR) + 2*rd(IR) + out() |
| Division Exception | 25 + in() +2*wr(IR) + 2*rd(IR) + out() |
| Stack Overflow Warning | 26 + in() +2*wr(IR) + 2*rd(IR) + out() |
| Access Violation | 25 + in() +2*wr(IR) + 2*rd(IR) + out() |
| System Call | 30 + in() +3*wr(IR) + 2*rd(IR) + out() |
| Privileged Instruction | 26 + in() +2*wr(IR) + 2*rd(IR) + out() |
| EPU ← Memory | 38 + in() +4*wr(IR) + 2*rd(IR) + out() |
| Memory ← EPU | 38 + in() +4*wr(IR) + 2*rd(IR) + out() |
| A ← EPU | 31 + in() +3*wr(IR) + 2*rd(IR) + out() |
| EPU Internal Operation | 31 + in() +3*wr(IR) + 2*rd(IR) + out() |

### MISCELLANEOUS

| Type | Execution Time |
|------|----------------|
| FATAL | 15 + out() + rd(halt) minimum |
| RESET | 3 + rd(reset) + out() minimum |
| EPU Data Page Fault | 1 + epu(f1) and then Access Violation trap |

#### NOTES:

1. Additional cycles are necessary for address computation in the case of EPU-to-Memory and Memory-to-EPU traps, as shown below:

   - IR,DA: no additional cycles
   - X,SX,RA,SR: 1 additional cycle
   - BX: 2 additional cycles

2. The pipeline is flushed at the end of any interrupt or trap sequence.

## E.4. INSTRUCTION FETCH AND DECODE TIMING

| Condition | 1× Bus Timing | 2× Bus Timing | 4× Bus Timing |
|-----------|---------------|---------------|---------------|
| First byte, cache | 4 | 4 | 4 |
| First byte, external | 9 + w | 12 + 2w + (0-1) | 17 + 4w + (0-3) |
| First byte, burst | 12 + w | 18 + 2w + (0-1) | 29 + 4w + (0-3) |
| Subsequent byte, cache | 1 | 1 | 1 |
| Subsequent byte, external | 5 + w | 8 + 2w + (0-1) | 13 + 4w + (0-3) |
| Subsequent byte, burst | 8 + w | 14 + 2w + (0-1) | 25 + 4w + (0-3) |

#### NOTES:

1. The term "first" means the first byte fetched following a flushed pipeline. All other bytes are "subsequent". With a full pipeline, only the execution times are necessary.

2. With a 16-bit external bus, the prefetch unit tries to fetch words from external memory though bytes are transferred to the pipeline. Bytes other than the one requested are placed in cache.

3. A burst transfer transfers a four-word block starting with the word with the three least significant bits being zero. The appropriate byte is transferred to the decoder as it is written to the cache. The execution unit of the pipeline can begin execution prior to the burst transaction's completion if the necessary bytes are fetched during the early part of the burst transaction.

4. The numbers in parentheses depend on the phase relationship between the transaction request and the bus clock. The average will be half of the sum of the minimum and maximum numbers in parentheses.

5. The notation "w" in the transaction tables is the number of WAIT states added to the bus cycle that are either externally generated or programmably added. Wait states that are an integral part of the transaction (e.g., one wait state for I/O transactions) should not be included.

6. Examples of instruction fetch/decode time (assuming flushed pipeline and 1× bus timing):
   - a) Two-byte instruction in cache: [4 + 1] processor cycles
   - b) Two-byte instruction both bytes not in cache: [(9 + w) + (5 + w)]
   - c) Two-byte instruction, first byte in cache, second not in cache: [4 + (5 + w)]
   - d) Four-byte instruction in cache: [4 + 1 + 1 + 1] processor cycles
   - e) Four-byte instruction not in cache, no burst, not cacheable: [9 + w + 3 * (5 + w)] processor cycles
   - f) Four-byte instruction not in cache, burst, cacheable: [12 + w + 1 + 1 + 1]
   - g) Six-byte instruction, burst, first two bytes in cache: [4 + 1 + (8 + w) + 1 + 1 + 1]

## E.5. DATA READ TIMING — rd(src), rd(dst), and rd(IR)

| Condition | 1× Bus Timing | 2× Bus Timing | 4× Bus Timing |
|-----------|---------------|---------------|---------------|
| Byte Hit | 5 | 5 | 5 |
| Byte Miss | 8 + w | 11 + 2w + (0-1) | 16 + 4w + (0-3) |
| Aligned Word Hit | 5 | 5 | 5 |
| Aligned Word Miss | 8 + w | 11 + 2w + (0-1) | 16 + 4w + (0-3) |
| Unaligned Word Hit Hit | 9 | 9 | 9 |
| Unaligned Word Miss Hit | 12 + w | 15 + 2w + (0-1) | 20 + 4w + (0-3) |
| Unaligned Word Hit Miss | 12 + w | 15 + 2w + (0-1) | 20 + 4w + (0-3) |
| Unaligned Word Miss Miss | 15 + w | 21 + 2w + (0-2) | 31 + 4w + (0-6) |
| TSET (cache) | 8 + w | 11 + 2w + (0-1) | 16 + 4w + (0-3) |
| TSET (fixed memory) | 6 | 6 | 6 |
| Page Fault | 4 + Access Violation trap | 4 + Access Violation trap | 4 + Access Violation trap |

#### NOTES:

1. Additional cycles are necessary for address computation, as shown below:

   - IR,DA: no additional cycles
   - X,SX,RA,SR: 1 additional cycle
   - BX: 2 additional cycles

2. A word is aligned if the address is even and the transfer is over a 16-bit bus. It is otherwise unaligned.

3. The numbers in parentheses depend on the phase relationship between the transaction request and the bus clock. The average will be half of the sum of the minimum and maximum numbers in parentheses.

4. The notation "w" in the transaction tables is the number of wait states added to the bus cycle that are either externally generated or programmably added. Wait states that are an integral part of the transaction (e.g., one wait state for I/O transactions) should not be included.

## E.6. DATA WRITE TIMING — wr(src), wr(dst), and wr(IR)

| Condition | 1× Bus Timing | 2× Bus Timing | 4× Bus Timing |
|-----------|---------------|---------------|---------------|
| Byte | 5 | 5 | 5 |
| Aligned Word | 5 | 5 | 5 |
| Unaligned Word | 9 + w | 12 + 2w + (0-1) | 17 + 4w + (0-3) |
| Page Fault | 4 + Access Violation trap | 4 + Access Violation trap | 4 + Access Violation trap |

#### NOTES:

1. Additional cycles are necessary for address computation, as shown below:

   - IR,DA: no additional cycles
   - X,SX,RA,SR: 1 additional cycle
   - BX: 2 additional cycles

2. A word is aligned if the address is even and the transfer is over a 16-bit bus. It is otherwise unaligned.

3. The pipeline is flushed whenever a byte being written is valid in the cache.

4. In the unaligned word case where the first byte is valid in cache, the execution time is 10 cycles with zero or one wait states and 9 + w cycles for two or more wait states.

5. The number in parentheses depend on the phase relationship between the transaction request and the bus clock. The average will be half of the sum of the minimum and maximum numbers in parentheses.

6. The notation "w" in the transaction tables is the number of wait states added to the bus cycle that are either externally generated or programmably added. Wait states that are an integral part of the transaction (e.g., one wait state for I/O transactions) should not be included.

## E.7. I/O READ AND WRITE TIMING

#### Table E-7. I/O Read and Write Timing

| Type | 1× Bus Timing | 2× Bus Timing | 4× Bus Timing |
|------|---------------|---------------|---------------|
| in(I) | 5 | 5 | 5 |
| in( ) | 9 + w | 13 + 2w + (0-1) | 20 + 4w + (0-3) |
| wr(I) | 5 | 5 | 5 |
| wr( ) | 5 | 5 | 5 |

#### NOTES:

1. The numbers in parentheses depend on the phase relationship between the transaction request and the bus clock. The average will be half of the sum of the minimum and maximum numbers in parentheses.

2. The notation "w" in the transaction tables is the number of wait states added to the bus cycle that are either externally generated or programmably added. Wait states that are an integral part of the transaction (e.g., one wait state for I/O transactions) should not be included.

3. in(I) and wr(I) are performed internally within the Z280 MPU.

## E.8. EPU READ AND WRITE TIMING

#### Table E-8. EPU Read and Write Timing

| Type | 1× Bus Timing | 2× Bus Timing | 4× Bus Timing |
|------|---------------|---------------|---------------|
| epu(f1) | 8 + w | 11 + 2w + (0-1) | 16 + 4w + (0-3) |
| epu(fn) | 8 + w | 11 + 2w + (0-1) | 16 + 4w + (0-3) |
| epu(cpu) | 9 + w | 13 + 2w + (0-1) | 20 + 4w + (0-3) |
| epu(wr) | 10 + w | 15 + 2w + (0-1) | 24 + 4w + (0-3) |
| epu(rd) | 8 + w | 11 + 2w + (0-1) | 16 + 4w + (0-3) |

#### NOTES:

1. The numbers in parentheses depend on the phase relationship between the transaction request and the bus clock. The average will be half of the sum of the minimum and maximum numbers in parentheses.

2. The notation "w" in the transaction tables is the number of wait states added to the bus cycle that are either externally generated or programmably added. Wait states that are an integral part of the transaction (e.g., one wait state for I/O transactions) should not be included.

## E.9. INTERRUPT ACKNOWLEDGE TIMING

#### Table E-9. Interrupt Acknowledge Timing

| Type | 1× Bus Timing | 2× Bus Timing | 4× Bus Timing |
|------|---------------|---------------|---------------|
| iack(nmi012) | 4 | 4 | 4 |
| iack(m0) | 8 + w | 13 + 2w + (0-1) | 22 + 4w + (0-3) |
| iack(m1) | 10 + w | 15 + 2w + (0-1) | 24 + 4w + (0-3) |
| iack(m2) | 10 + w | 15 + 2w + (0-1) | 24 + 4w + (0-3) |
| iack(m3) | 10 + w | 15 + 2w + (0-1) | 24 + 4w + (0-3) |

#### NOTES:

1. The numbers in parentheses depend on the phase relationship between the transaction request and the bus clock. The average will be half of the sum of the minimum and maximum numbers in parentheses.

2. The notation "w" in the transaction tables is the number of wait states added to the bus cycle that are either externally generated or programmably added. Wait states that are an integral part of the transaction (e.g., one wait state for I/O transactions) should not be included.

3. iack(nmi012) is for NMI in modes 0, 1, and 2. iack(m0) is for mode 0 interrupts.

## E.10. MISCELLANEOUS TRANSACTION TIMING

#### Table E-10. Miscellaneous Transaction Timing

| Type | 1× Bus Timing | 2× Bus Timing | 4× Bus Timing |
|------|---------------|---------------|---------------|
| HALT Transaction | 5 | 5 | 5 |
| RESET Transaction | 6 | 6 | 6 |
| RETI Transaction | 21 + w | 31 + 2w + (0-2) | 49 + 4w + (0-6) |

#### NOTES:

1. The numbers in parentheses depend on the phase relationship between the transaction request and the bus clock. The average will be half of the sum of the minimum and maximum numbers in parentheses.

2. The notation "w" in the transaction tables is the number of WAIT states added to the bus cycle in addition to any automatically inserted WAIT states. This includes any WAITs added under program control.
