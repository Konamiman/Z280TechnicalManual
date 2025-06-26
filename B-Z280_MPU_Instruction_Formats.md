# Appendix B. Z280 MPU Instruction Formats

Four formats are used to generate the machine-language bit encodings for the Z280 MPU instructions. Three formats are used for instructions that are executed solely by the Z280 CPU. (These same three formats are used for Z80 CPU instruction encoding.) A fourth format is dedicated to instructions that involve Extended Processing Units (EPUs).

The bit encodings of the Z280 MPU instructions are partitioned into bytes. Every instruction encoding contains one byte dedicated to specifying the type of operation to be performed; this byte is referred to as the instruction's operation code (opcode). Besides specifying a particular operation, opcodes typically include bit encodings specifying the operand addressing mode for the instruction and identifying any general-purpose registers used by the instruction. Along with the opcode, instruction encodings may include bytes that contain an address, displacement, and/or immediate value used by the instruction, and special bytes called "escape codes" that determine the meaning of the opcode itself.

By themselves, one byte opcodes would allow the encoding of only 256 unigue instructions. Therefore, special "escape codes" that precede the opcode in the instruction encoding are used to expand the number of possible instructions. There are two types of escape codes: addressing mode escape codes and opcode escape codes. Escape codes are one byte in length.

Three of the instruction formats are differentiated by the opcode escape value used; the fourth format is for instructions that include an EPU template. Format 1 is for instructions without an opcode escape byte, Format 2 is for instructions whose opcode escape byte has the value ED<sub>H</sub>, and Format 3 is for instructions whose opcode escape byte has the value CB<sub>H</sub>. Instructions that support EPUs use Format 4 and always have the opcode escape byte with value ED<sub>H</sub> as the first byte of the instruction encoding. In Formats 2 and 4, the opcode escape byte immediately preceeds the opcode byte itself.

In Format 3, a 1-byte displacement may be between the opcode escape byte and opcode itself. Opcode escape bytes are used to distinguish between two different instructions with the same opcode byte, thereby allowing more than 256 unique instructions. For example, the 01<sub>H</sub> opcode, when alone, specifies a form of the Load Register Word instruction; when preceded by the CB<sub>H</sub> escape byte, the opcode 01<sub>H</sub> specifies a Rotate Left Circular instruction.

Addressing mode escape codes are used to determine the type of encoding for the addressing mode field within an instruction's opcode, and can be used in instructions with and without opcode escape values. An addressing mode escape byte can have the value DD<sub>H</sub> or FD<sub>H</sub>. The addressing mode escape byte, if present, is always the first byte of the instruction's machine code, and is immediately followed by either the opcode (Format 1) or the opcode escape byte (Formats 2 and 3). For example, the 79<sub>H</sub> opcode, when alone, specifies a Load Accumulator instruction using Register addressing for the source operand; when preceded by the DD<sub>H</sub> escape byte, the opcode 79<sub>H</sub> specifies a Load Accumulator instruction using Base Index addressing for the source operand.

The four instruction formats are shown in Tables B-1 through B-4. Within each format, several different configurations are possible, depending on whether the instruction involves addressing mode escape bytes, addresses, displacements, or immediate data. In Tables B-1 through B-4, the symbol "A.esc" is used to indicate the presence of an addressing mode escape byte, "disp." is an abbreviation for displacement, "addr." is an abbreviation for address, and "temp." is an abbreviation for template. Templates in EPU instructions are four-byte fields that include the bit encodings that specify EPU operation. Example instructions are shown in both Assembly text and Machine Code hexadecimal values.

<br/>

| | | Instruction Format | | Assembly | Machine Code (Hex)
|-|-|-|-|-|-|
| | opcode | | | LD A,C | 79
| | opcode | 2-byte address | | LD A,(addr) | 3A addr(low) addr(high)
| | opcode | 1-byte displacement | | DJNZ addr | 10 disp
| | opcode | immediate | | LD E,n | 1E n
| A.esc | opcode | | | LD A,(HL + IX) | DD 79
| A.esc | opcode | 2-byte address | | LD IX,(addr) | DD 2A addr(low) addr(high)
| A.esc | opcode | 1-byte displacement | | LD A,(IX + d) | DD 7E disp
| A.esc | opcode | 2-byte displacement | | LD A,(IX + dd) | FD 79 d(low) d(high)
| A.esc | opcode | immediate | | LD IX,nn | DD 21 n(low) n(high)
| A.esc | opcode | 2-byte address | immediate | LD (addr),n | DD 3E addr(low) addr(high) n
| A.esc | opcode | 1-byte displacement | immediate | LD (IY + d),n | FD 36 d n
| A.esc | opcode | 2-byte displacement | immediate | LD &lt;addr&gt;,n | FD 06 disp(low) disp(high) n

_Table B-1. Format 1 Instruction Encodings_

<br/>

| | | Instruction Format | | Assembly | Machine Code (Hex)
|-|-|-|-|-|-|
| | ED | opcode | | MULT A,B | ED C0
| | ED | opcode | immediate | SC nn | ED 71 n(low) n(high)
| | ED | opcode | 2-byte address | LD BC,(addr) | ED 4B addr(low) addr(high)
| | ED | opcode | 2-byte displacement | LD (HL + dd),A | ED 3B d(low) d(high)
| A.esc | ED | opcode | | MULT A,IY | FD ED E8
| A.esc | ED | opcode | 2-byte address | MULT A,(addr) | DD ED F8 addr(low) addr(high)
| A.esc | ED | opcode | 1-byte displacement | MULT A,(IY + d) | FD ED F8 d
| A.esc | ED | opcode | 2-byte displacement | LD IX,(IY + dd) | DD ED 34 d(low) d(high)
| A.esc | ED | opcode | 2-byte immediate | MULTUW HL,nn | FD ED F3 n(low) n(high)

_Table B-2. Format 2 Instruction Encodings_

<br/>

| | | Instruction Format | | Assembly | Machine Code (Hex)
|-|-|-|-|-|-|
| | CB | opcode | | RLC (HL) | CB 06
| A.esc | CB | 1-byte displacement | opcode | RLC (IX + d) | DD CB d 06

_Table B-3. Format 3 Instruction Encodings_

<br/>

| | | Instruction Format | | Assembly | Machine Code (Hex)
|-|-|-|-|-|-|
| ED | opcode | 4-byte template | | EPU ← (HL) | ED A6 temp1 temp2 temp3 temp4
| ED | opcode | 2-byte displacement | 4-byte template | EPU ← (HL + dd) | ED BC d(low) d(high) temp1 temp2 temp3 temp4
| ED | opcode | 2-byte address | 4-byte template | EPU ← (addr) | ED A7 addr(low) addr(high) temp1 temp2 temp3 temp4

_Table B-4. Format 4 Instruction Encodings_
