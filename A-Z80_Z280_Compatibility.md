# Appendix A. Z80/Z280 Compatibility

The Z280 MPU architecture is an upward-compatible extension of the Z80 CPU architecture. This compatibility extends to the instruction set, register architecture, interrupt structure, and bus structure of the Z280 MPU and Z80 CPU.

The Z80 CPU's instruction set is a subset of the Z280 MPU's instruction set. Thus, the Z280 MPU is completely binary-compatible with Z80 code. However, since some Z80 instructions, such as HALT, are privileged instructions in the Z280 MPU, complete compatibility is achieved only when the Z280 MPU is executing in the system mode. All Z80 software will execute successfully on a Z280 MPU running in system mode, provided that the software contains no timing dependencies, does not modify itself, and does not use any of the Z80's reserved instruction encodings.

Since the Z280 MPU is binary-code compatible with the Z80 CPU, the Z280 MPU's general-purpose register set is the same as the Z80 CPU's, with the exception of the Stack Pointer. The Z280 MPU contains both a System Stack Pointer and a User Stack Pointer, whereas the Z80 CPU has only one Stack Pointer register. In the Z80 CPU, the R register is used to indicate the next refresh address; in the Z280 MPU, the R register is not involved with the refresh logic and may be used by the programmer as a general-purpose storage register.

The Z280 MPU's interrupt structure is also an upward-compatible extension of the Z80 CPU's. The Z280 MPU supports all three interrupt modes found on the Z80 CPU, as well as a fourth interrupt mode new to the Z280 MPU.

The Z80 Bus configurations of the Z280 MPU are also bus-compatible with the Z80 CPU, generating the same <ins>RD</ins>, <ins>WR</ins>, <ins>IORQ</ins>, and <ins>MREQ</ins> bus control and status signals. However, <ins>M1</ins> is asserted during every instruction fetch and interrupt acknowledge cycle in the Z80 CPU; for the Z280 MPU, <ins>M1</ins> is asserted only during the special RETI bus transaction and interrupt acknowledge cycles. The Z8400 family of peripherals interface directly to both Z80 CPUs and Z80 bus configuration of the Z280 MPUs.

Following a reset, the Z280 MPU takes on a configuration that is fully compatible with Z80 code. The Memory Management Unit is disabled, meaning that the 16-bit logical addresses from the Z280 CPU are routed directly to the 16 least significant address pins on the external bus. The User/System bit in the Master Status register specifies system-mode operation, allowing execution of privileged instructions and enabling the System Stack Pointer. The I/O Page register is cleared to all 0s and Interrupt Mode 0 is selected. The Trap Control register is cleared to all zeros, disabling System Stack Overflow Warning traps and designating that I/O instructions are not privileged. All Z80 instructions can be successfully executed (and may execute from the on-chip memory that is enabled as an instruction-only cache upon reset). The Z280 MPU will remain in a Z80-compatible configuration as long as Z80 code is executed, since the Load Control instruction that acts on the Z280 MPU's control registers is not part of the Z80 instruction set.

The software routine shown below can be used to determine if code is executing on a Z80 CPU or Z280 MPU. This facilitates development of programs that can execute on either processor, but contain special routines invoked only when executing on a Z280 MPU and, therefore, allowing use of Z280 MPU features not available on the Z80 CPU. The routine differentiates the Z80 CPU from the Z280 MPU by executing the instruction with machine code CB37<sub>H</sub>. This instruction code is reserved in the Z80 CPU, and results in logically shifting the A register one bit to the left while shifting a 1 into the least significant bit. For the Z280 MPU, CB37<sub>H</sub> is the code for the Test and Set instruction. If the A register holds a 40<sub>H</sub> before executing this instruction code, the A register holds an 81<sub>H</sub> and the Sign flag is set to 1 after executing the instruction on a Z80 CPU; the A register holds an FF<sub>H</sub> and the Sign flag is cleared to 0 after executing the instruction on a Z280 MPU.

```
; This instruction sequence exploits the difference when executing the CB37h
; machine code on the Z80 CPU and Z280 MPU, to allow a program to determine which
; processor it is executing on. This instruction sets the S flag on the Z80 CPU 
; and clears the S flag on the Z280 MPU. The A and F registers are used by the
; routine.

    LD   A,40h        ; Initialize the operand.
    DEFB 0CBh,037h    ; This instruction will set the S flag on the 
                      ; Z80 CPU and clear the S flag on the Z280 MPU.
    JP   M,Z80        ; Now test the flag and jump.
       or
    JP   P,Z280
```

_Code to Distinguish Execution on a Z80 CPU and Z280 MPU_
