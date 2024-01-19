# 11. Reset

Hardware resets are asserted by an active <ins>RESET</ins> input and place the Z280 MPU in a known state. Optionally, the Bus Timing and Initialization register can be initialized to a system specifiable value during a reset. The <ins>RESET</ins> input is internally synchronized to the clock and then sampled at the end of every processor clock cycle. When an active <ins>RESET</ins> line is detected, the current bus transaction is allowed to be completed before starting the reset process. A reset overrides all other operations, including interrupts, traps, and bus requests. A hardware reset must be used to initialize the Z280 MPU as part of the power-up sequence.

The <ins>RESET</ins> input must be asserted for a minimum of 128 processor clock cycles. Within this time the Z280 MPU lines assume their reset values: the address and address/data lines are 3-stated and all control lines are forced High. While <ins>RESET</ins> is asserted, the clock output line (CLK) is the processor clock frequency divided by four.

When <ins>RESET</ins> is sampled high (deasserted), the state of the <ins>WAIT</ins> input is sampled. If <ins>WAIT</ins> is asserted, the contents of the AD<sub>0</sub>-AD<sub>7</sub> lines are sampled on the falling edge of the processor clock and loaded into the Bus Timing and Initialization register; if this method of initialization is chosen, AD<sub>7</sub> must be a 1 and AD<sub>4</sub> must be a 0 when the bus is sampled, and the state of the AD<sub>6</sub> line determines whether the bootstrap mode option is selected. <ins>WAIT</ins> must be asserted for at least two processor clock cycles after <ins>RESET</ins> is deasserted in order for the Bus Timing and Initialization register, thereby specifying a bus clock frequency of one-half the processor clock, no automatic wait states when accessing the lower 8M bytes of memory, and disabling the multiprocessor mode of operation.

Table 11-1 delineates the effect of a reset on other CPU registers. A reset places the CPU in
interrupt mode 0; thus, the IM field in the Interrupt Status register will be a 0. The Interrupt Vector Enable bits in the Interrupt Status register also are cleared to 0 by a reset, and the Interrupt Pending bits will reflect the current status of the interrupt requests. All other CPU and MMU registers, including the remaining registers in the CPU register file, the MMU page descriptor registers, and the Interrupt/Trap Vector Table Pointer are unaffected by a reset.

<br/>

Register | Value Loaded on Reset<br/>(Hexadecimal) | Comments
|-|-|-|
Program Counter | 0000
System Stack Pointer | 0000
I | 00
R | 00
Master Status | 0000 | System mode, Single-Step disabled, Breakpoint-on-Halt disabled<br/>All maskable interrupts disabled
Bus Timing and Control | 30 | No automatic wait states for I/O, upper 8M bytes of memory, or interrupt acknowledges
Bus Timing and Initialization | 80 | CLK output 2 x processor clock period, no automatic wait states for lower 8M bytes of memory, bootstrap mode disabled
I/O Page | 00 | I/O Page 0 in use
Cache Control | 20 | Cache enabled for instructions<br/>All valid bits cleared to 0<br/>Burst mode disabled
Trap Control | 00 | EPA trap disabled, I/O not privileged
System Stack Limit | 0000 | System Stack Overflow Warning trap disabled
Local Address | 00 | All memory transactions are made to local bus
Interrupt Status | 00xx | Interrupt mode 0, nonvectored interrupts, current state of interrupt requests (indicated by xx)
Interrupt/Trap Vector Table Pointer | | Unaffected
CPU Registers AF, BC, DE, HL, IX, IY, AF', BC', DE', HL' | | Unaffected
User Stack Pointer | | Unaffected
MMU Master Control | 0000 | MMU disabled
MMU Page Descriptor Register, Page Descriptor Register Pointer | Unaffected

_Table 11-1. Effect of a Reset on Z280 MPU and MMU Registers_

<br/>

The effect of a reset on the on-chip peripherals' programmable registers is shown in Table 11-2. The on-chip counter/timers are always disabled by a reset. The on-chip DMA channels and UART are also disabled by a reset, unless bootstrap mode is selected (see Section 9.7). The counter/timers' Time Constant and Count-Time registers are unaffected by a reset. The DMA channels' Destination Address, Source Address, and Count registers also are unaffected by a reset, except for DMA Channel 0's Destination Address and Count registers.

<br/>

Register | Value Loaded on Reset<br/>(Hexadecimal) | Comments
|-|-|-|
Refresh | 88 | Refresh enabled, rate = 32
Counter/Timers:
&nbsp;&nbsp;&nbsp;&nbsp;Configuration | 00 | Timer mode, single-cycle mode
&nbsp;&nbsp;&nbsp;&nbsp;Command/Status | 00 | Timer disabled
DMA Channels:
&nbsp;&nbsp;&nbsp;&nbsp;Master Control | 0000* | No DMA linking, EOP disabled, Software Ready disabled
&nbsp;&nbsp;&nbsp;&nbsp;DMA0 Transaction Descriptor | 0100* | DMA0 disabled, continuous mode
&nbsp;&nbsp;&nbsp;&nbsp;DMA1/2/3 Transaction Descriptor | - | EN, IE, TC, and EPS fields cleared, other fields unaffected
&nbsp;&nbsp;&nbsp;&nbsp;DMA0 Destination Address | 000000
&nbsp;&nbsp;&nbsp;&nbsp;DMA0 Count | 0100
UART:
&nbsp;&nbsp;&nbsp;&nbsp;Configuration | 00* | 5 bits/character, parity disabled, external clock, x 1 clock rate, loop back disabled
&nbsp;&nbsp;&nbsp;&nbsp;Transmitter Control/Status | 01 | Transmitter disabled, transmit buffer empty
&nbsp;&nbsp;&nbsp;&nbsp;Receiver Control/Status | 00* | Receiver disabled

\* Unless bootstrap mode is selected.

_Table 11-2. Effect of a Reset on Z280 On-Chip Peripheral Registers_

<br/>

In a multiprocessing system employing multiple Z280 MPUs with a shared bus, the internal processor clocks for the Z280 MPUs need to be synchronized. The processor clock is generated by dividing the XTAL1 input by two. The falling edge of <ins>RESET</ins> is used internally to synchronize the processor clock, and can be used to synchronize processor clocks in a multiprocessing system. If all the Z280 MPUs in the system have identical XTAL1 and <ins>RESET</ins> input signals, their internal processor clocks will be initialized in the same manner by a reset.

If an active bus request is detected on the rising edge of <ins>RESET</ins>, the Z280 MPU grants the bus before fetching the first instruction from location 0. Thus, an external DMA device can initialize RAM memory before execution begins. If bus request is not asserted, the CPU begins execution with a fetch from location 0 unless bootstrap mode is in effect.
