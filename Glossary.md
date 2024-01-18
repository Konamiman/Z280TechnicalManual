# Glossary

**access protection:** A function of memory management that controls read, write, and execute access to memory locations, protecting proprietary or operating system memory areas from tampering by unauthorized users.

**access protection violation:** An incorrect or forbidden attempt to access a memory location; for example, an attempt to write to a read-only page. An access violation causes the CPU to abort the current instruction and generate an Access Violation trap.

**accumulator:** A register within a central processing unit (CPU) that can hold the result of an arithmetic or logical operation.

**address space:** A set of addresses that are accessed in a similar manner. The Z280 MPU contains four types of address spaces: CPU register, CPU control register, memory, and I/O. The memory space can be divided into four separate memory address spaces: system-mode program, system-mode data, user-mode program, and user-mode data.

**addressing mode:** The way in which the location of an operand is specified. There are nine addressing modes in the Z280 MPU: Register, Immediate, Register Indirect, Direct Address, Indexed, Short Index, Base Index, Relative Address, and Stack Pointer Relative.

**address tag:** The portion of certain associative memories that is compared against a referenced address to determine whether the matching value is found. The address tag for a cache block is the physical memory address.

**address translation:** The process of mapping logical addresses into physical addresses.

**aligned address:** An address that is a multiple of an operand's size in bytes. Aligned word addresses are a multiple of two.

**associative memory:** A memory in which data is accessed by specifying a value rather than a location. The cache is an associative memory.

**autodecrement:** The operation of decrementing an address in a register by the operand's size in bytes. The decrement amount is one for byte operands, two for word operands.

**autoincrement:** The operation of incrementing an address in a register by the operand's size in bytes. The increment amount is one for byte operands, two for word operands.

**base address:** The address used, along with an index and/or displacement value, to calculate the effective address of an operand. The base address is located in a register, the Program Counter, or the instruction.

**Base Index (BX) addressing node:** In this mode, the contents of the base register and index register are added to obtain the effective address.

**burst transaction:** The transfer of several consecutive items of data in one memory transaction.

**bus master:** The device in control of the bus.

**bus request:** A request for control of the bus initiated by a device other than the bus master.

**byte:** A data item containing eight contiguous bits. A byte is the basic data unit for addressing memory and peripherals.

**cache:** An on-chip buffer that automatically stores copies of recently used memory locations (both instructions and data), allowing fast access for memory fetches.

**Central Processing Unit (CPU):** The primary functioning unit of a computer, consisting of an ALU, control logic for decoding and executing instructions and controlling program flow, and registers.

**coprocessor:** A processor that works synchronously with the CPU to execute a single instruction stream using the Extended Processing Architecture (EPA).

**destination:** The register, memory location, or device to which data are to be transferred.

**Direct Address (DA) addressing mode:** In this mode, the effective address is contained in the instruction.

**displacement:** A constant value located in the instruction that is used for calculating the effective address of an operand.

**effective address:** The logical memory address of an operand, calculated by adding the base address, an optional index value, and an optional displacement.

**EPU internal operation:** An EPU-handled operation that controls EPU operations but does not transfer data.

**exception:** A condition or event that alters the usual flow of instruction processing. The Z280 MPU supports three types of exception: reset, interrupts, and traps.

**Extended Processing Architecture (EPA):** A CPU facility that allows the operations defined in the architecture to be extended by hardware or software. If enabled, the CPU transfers EPA instructions to an Extended Processing Unit (EPU) for execution; if disabled, the CPU traps EPA instructions for software emulation.

**Extended Processing Unit (EPU):** An external device, that handles Extended Processing Architecture instructions (such as floating-point arithmetic).

**flowthrough transaction:** A DMA-initiated data transfer consisting of separate read and write transactions. Data is temporarily stored in the DMA channel between the read and write transactions.

**flyby transaction:** A transaction controlled by the bus master, but in which another device transfers data to the responding device.

**frame:** A block of physical memory used by the memory management mechanism to map logical memory pages.

**global bus:** A bus shared by tightly coupled, multiple CPUs; the bus master is chosen by an external arbiter device.

**hit:** A hit occurs when a associative memory is searched for a value and a match is found.

**identifier word:** A 16-bit code saved on the system stack during exception processing that provides information about the cause of the exception.

**Immediate (IM) addressing mode:** In this mode, the operand is contained in the instruction.

**index:** A value located in a register used for calculating the effective address of an operand. The index value usually specifies the calculated offset of an operand from the orgin of an array or other data structure.

**Indexed (X) addressing mode:** In this mode, the contents of an index register are added to a base address contained in the instruction to obtain the effective address.

**Indirect Register (IR) addressing mode:** In this mode, the effective address is contained in a register.

**interrupt:** An asynchronous exception that occurs when an NMI or INT line is activated, usually when a peripheral device needs attention.

**least recently used (LRU):** The CPU records the order of use for cache blocks. When a tag miss occurs, the CPU replaces the least recently used block.

**local bus:** The bus controlled by the CPU and shared with slave processors.

**logical address:** The address manipulated by the program. The memory management mechanism translates logical addresses to physical addresses.

**loosely coupled CPUs:** CPUs that execute independent instruction streams and communicate through a multi-ported peripheral, such as a Z8038 FIO I/O interface unit.

**Master Status register:** A 16-bit CPU control register that contains status information describing the current operating states of the CPU.

**memory management:** The process of translating logical addresses into physical addresses, plus certain protection functions. In the Z280 MPU, memory management is integrated into the chip.

**memory-mapped I/O:** An I/O device accessed in the memory address space.

**miss:** A miss occurs when an associative memory is searched for a value and no match is found.

**nonmaskable interrupt:** The highest priority interrupt; cannot be disabled.

**page:** A logical memory unit mapped by the memory management mechanism to a physical memory frame.

**paged translation:** A method of address translation in which the logical and physical address spaces are divided into fixed, equal-sized units called pages and frames, respectively. During address translation, a logical page is mapped to an arbitrary physical frame.

**physical address:** The 24-bit address required for accessing memory and peripherals, obtained by the CPU's address translation hardware.

**pipeline:** A computer design technique in which an instruction is executed in a seguence of stages by different functional units. The functional units can be operating on several different instructions simultaneously, similiar to an automobile assembly line.

**prefetching:** Ability of the CPU to fetch an instruction or operand before the previous instructions have been completed.

**privileged instruction:** An instruction that performs I/O operations, accesses control registers, or performs some other operating system function. Privileged instructions execute in system mode only.

**Program Counter (PC):** One of the two Program Status registers; it contains the address of the current instruction.

**Program Status registers:** The two registers (Program Counter and Master Status register) that contain the Program Status. The Program Status is automatically saved during exception processing.

**protection:** See access protection.

**read access:** The type of memory access used by the CPU for fetching data operands other than those specified by Immediate addressing mode.

**refresh:** To restore information that fades away if left alone. For example, dynamic memories must be refreshed periodically in order to retain their contents.

**Register (R) addressing node:** In this mode, the operand is in a general-purpose register.

**Relative Address (RA) addressing node:** In this mode, the displacement in the instruction is added to the contents of the Program Counter to obtain the effective address.

**relocation:** The process of mapping a logical address to a different physical address, so that multiple processes can use the same logical address for distinct physical memory locations.

**request:** A signal or message used by a device to indicate the need for some action or resource.

**reset:** A CPU operating state or exception that results when a reset request is signaled on the RESET line.

**responder:** The device to which bus transactions transfer data.

**self modifying program:** A program that stores to a location from which a subsequent instruction is fetched.

**semaphore:** A storage location used as a Boolean variable to synchronize the use of resources among multiple programming tasks. A semaphore ensures that a shared resource is allocated to only one task at any given time.

**service routine:** Program code that is executed in response to an interrupt or trap.

**Short Index addressing mode:** In this mode, the contents of the IX or IY register are added to an 8-bit displacement corftained in the instruction to obtain the effective address of the operand.

**slave processor:** A processor, such as a Direct Memory Access transfer controller, that performs dedicated functions asynchronously to the CPU.

**source:** The register, memory location, or device from which data are being read.

**spatial locality:** The characteristic of program behavior whereby consecutive memory references often apply to closely located addresses.

**stack:** An area of memory used for temporary storage and subroutine linkages. A stack uses the first-in, last-out method for storing and retrieving data; the last data written onto the stack will be the first data read from the stack.

**Stack Pointer (SP):** A register indicating the top (lowest address) of the processor stack used by Call and Return instructions for linking procedures. User and system modes of operation use separate Stack Pointers, the User Stack Pointer (USP) and System Stack Pointer (SSP).

**system mode:** A CPU mode of operation, used for operating system functions. In this mode, the CPU can execute privileged (and all other) instructions.

**System Stack Pointer (SSP):** The Stack Pointer used while the CPU is in system mode. User mode programs cannot access the SSP.

**tag hit:** On a memory reference, a tag hit occurs when the cache address tags are searched for the referenced address and a match is found.

**tag miss:** On a memory reference, a tag miss occurs when the cache address tags are searched for the referenced address and no match is found.

**temporal locality:** The characteristic of program behavior whereby memory references often apply to a location that has been referred to recently.

**tightly coupled CPUs:** CPUs that execute independent instruction streams and communicate through shared memory on a common (global) bus.

**transaction:** A basic bus operation involving the transfer of one byte or word of data between the CPU and a memory or peripheral device.

**trap:** An exception that occurs when certain conditions, such as an access protection violation, are detected during execution of an instruction.

**unaligned address:** An address that is not a multiple of an operand's size in bytes. Odd addresses are unaligned for words.

**user mode:** A CPU mode of operation, generally used for application programs. In this mode, the CPU cannot execute privileged instructions or access protected memory locations.

**User Stack Pointer (USP):** The Stack Pointer used while the CPU is in user mode. System mode programs can access the USP with the Load Control instruction.

**vectored interrupt:** A interrupt that uses the low-order byte of the identifier word as a vector to an interrupt service routine; can be disabled.

**virtual memory:** A memory management technique in which the system's logical memory address space is not necessarily the same as, and can be much larger than, the available physical memofcy.

**wait state:** A clock period that is added to a memory or I/O transaction due to an active WAIT signal. Wait states are used to prolong memory and I/O transactions to devices with long access times.

**word:** A data item containing sixteen contiguous bits.

**write access:** The type of memory access used by the CPU for storing data operands.
