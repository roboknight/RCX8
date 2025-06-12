## Instruction Set for the fictional RCx8

- - -

The RCx8 processor is an 8-bit processor with 8 8-bit registers R0-R7 as well as the Accumulator ACC. The processor can address up to 65536 bytes of memory. The program counter PC is a 16-bit register. All addresses appear as 2 bytes, LSB followed by MSB. The processor is intended for use as a microcontroller for simple plant monitoring and processing. It can interface directly to an output terminal and keyboard for real time messaging and decision making by operators. Programs can be bootstrapped directly from a network uplink to the 64k RAM by holding the test pin high, and holding load pin high for memory upload and dropping low for PC upload. Releasing the test pin restarts execution. Systems control and monitoring is via port addressing to up to 256 ports which would generally consist of transducers. The RCx8 chip has an on board 256 byte RAM which is used to maintain an internal call stack.

| Operation | Mnemonic | OPR |     |     |     | OPA |     |     |     | Description | Carry | Bytes | Cycles |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| D3  | D2  | D1  | D0  | D3  | D2  | D1  | D0  |
| No operation | NOP | 0   | 0   | 0   | 0   | 0   | 0   | 0   | 0   | PC « PC + 1 | \-  | 1   | 1   |
|     | No operation. |     |     |     |     |     |     |     |     |     |     |     |     |
| Jump conditionally | JCN | 0  <br>A  <br>A | 0  <br>A  <br>A | 0  <br>A  <br>A | 1  <br>A  <br>A | C1  <br>A  <br>A | C2  <br>A  <br>A | C3  <br>A  <br>A | C4  <br>A  <br>A | If Cx, PC « Address | \-  | 3   | 2   |
|     | If the condition exists, load program counter with address. Conditions are:  <br>C1 = Invert condition of C2, C3, and C4  <br>C2 = Accumulator is zero  <br>C3 = carry/link is true  <br>C4 = Test signal is true |     |     |     |     |     |     |     |     |     |     |     |     |
| Fetch immediate | FIM | 0  <br>D | 0  <br>D | 1  <br>D | 0  <br>D | R  <br>D | R  <br>D | R  <br>D | 0  <br>D | Register R « D | \-  | 2   | 2   |
|     | Load the register RRR with D. |     |     |     |     |     |     |     |     |     |     |     |     |
| Zero register | ZER | 0   | 0   | 1   | 0   | R   | R   | R   | 1   | Register R « 0 | \-  | 1   | 1   |
|     | Zero contents of register RRR. |     |     |     |     |     |     |     |     |     |     |     |     |
| Fetch indirect | FIN | 0   | 0   | 1   | 1   | R   | R   | R   | 0   | Register R « \[R1.R0\] | \-  | 1   | 2   |
|     | Load register RRR with the contents of RAM at address pointed to by R1.R0 where R1=MSB of address. |     |     |     |     |     |     |     |     |     |     |     |     |
| Fetch direct | FTC | 0  <br>A  <br>A | 0  <br>A  <br>A | 1  <br>A  <br>A | 1  <br>A  <br>A | R  <br>A  <br>A | R  <br>A  <br>A | R  <br>A  <br>A | 1  <br>A  <br>A | Register R « \[A\] | \-  | 3   | 2   |
|     | Load register RRR with the contents of RAM at address A. |     |     |     |     |     |     |     |     |     |     |     |     |
| Jump unconditionally | JUN | 0  <br>A  <br>A | 1  <br>A  <br>A | 0  <br>A  <br>A | 0  <br>A  <br>A | 0  <br>A  <br>A | 0  <br>A  <br>A | 0  <br>A  <br>A | 0  <br>A  <br>A | PC « Address | \-  | 3   | 2   |
|     | Unconditional jump to address A. The program counter is loaded with A. |     |     |     |     |     |     |     |     |     |     |     |     |
| Jump to subroutine | JMS | 0  <br>A  <br>A | 1  <br>A  <br>A | 0  <br>A  <br>A | 1  <br>A  <br>A | 0  <br>A  <br>A | 0  <br>A  <br>A | 0  <br>A  <br>A | 0  <br>A  <br>A | Stack « PC, PC « A | \-  | 3   | 2   |
|     | Store program counter on stack, moving subsequent values down the stack. Load the program counter with address. |     |     |     |     |     |     |     |     |     |     |     |     |
| Increment | INC | 0   | 1   | 1   | 0   | R   | R   | R   | 0   | R « R + 1 | ±   | 1   | 1   |
|     | Increment register RRR and set carry if overflow. |     |     |     |     |     |     |     |     |     |     |     |     |
| Increment on overflow | INO | 0   | 1   | 1   | 0   | R   | R   | R   | 1   | R « R + C | ±   | 1   | 1   |
|     | Increment register RRR if carry is set and set carry if overflow. |     |     |     |     |     |     |     |     |     |     |     |     |
| Increment register, skip jump if zero | ISZ | 0  <br>A  <br>A | 1  <br>A  <br>A | 1  <br>A  <br>A | 1  <br>A  <br>A | R  <br>A  <br>A | R  <br>A  <br>A | R  <br>A  <br>A | 0  <br>A  <br>A | R « R + 1, if R <> 0 then PC « Address | \-  | 3   | 2   |
|     | Increment register RRR and if Register R is not zero then jump to address A. |     |     |     |     |     |     |     |     |     |     |     |     |
| Store Reg | STO | 0  <br>A  <br>A | 1  <br>A  <br>A | 1  <br>A  <br>A | 1  <br>A  <br>A | R  <br>A  <br>A | R  <br>A  <br>A | R  <br>A  <br>A | 1  <br>A  <br>A | \[A\] « R | \-  | 3   | 3   |
|     | Store register RRR at RAM address A. |     |     |     |     |     |     |     |     |     |     |     |     |
| Add | ADD | 1   | 0   | 0   | 0   | R   | R   | R   | 0   | R « A + R + C | ±   | 1   | 1   |
|     | Add accumulator plus register RRR plus carry flag. If overflow, set carry flag. |     |     |     |     |     |     |     |     |     |     |     |     |
| Xor | XOR | 1   | 0   | 0   | 0   | R   | R   | R   | 1   | R « A xor R | \-  | 1   | 1   |
|     | Xor register RRR with accumulator. |     |     |     |     |     |     |     |     |     |     |     |     |
| Subtract | SUB | 1   | 0   | 0   | 1   | R   | R   | R   | 0   | R « A - R - C | ±   | 1   | 1   |
|     | Subtract from accumulator, register RRR and carry. If borrow, set carry. |     |     |     |     |     |     |     |     |     |     |     |     |
| Store Indirect | STI | 1   | 0   | 0   | 1   | R   | R   | R   | 1   | \[R1.R0\] « R | \-  | 1   | 1   |
|     | Store register RRR at RAM address R1.R0 where R1=MSB of address. |     |     |     |     |     |     |     |     |     |     |     |     |
| Load | LD  | 1   | 0   | 1   | 0   | R   | R   | R   | 0   | A « R | \-  | 1   | 1   |
|     | Load accumulator with contents of register R. |     |     |     |     |     |     |     |     |     |     |     |     |
| Exchange | XCH | 1   | 0   | 1   | 1   | R   | R   | R   | 0   | R « A, A « R | \-  | 1   | 1   |
|     | Exchange the contents of accumulator with register R. |     |     |     |     |     |     |     |     |     |     |     |     |
| Branch back one level | BBL | 1   | 1   | 0   | 0   | D   | D   | D   | D   | PC « Stack, A « DDDD | \-  | 1   | 1   |
|     | Load program counter with address on top of stack, move subsequent values up the stack. Returns 0000DDDD in accumulator. |     |     |     |     |     |     |     |     |     |     |     |     |
| Load immediate | LDM | 1   | 1   | 0   | 1   | D   | D   | D   | D   | A « D | \-  | 1   | 1   |
|     | Load accumulator with immediate data D. |     |     |     |     |     |     |     |     |     |     |     |     |

#### Input/Output and RAM Instructions

| Operation | Mnemonic | OPR |     |     |     | OPA |     |     |     | Description | Carry | Bytes | Cycles |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| D3  | D2  | D1  | D0  | D3  | D2  | D1  | D0  |
| Write Port | WMP | 1  <br>P | 1  <br>P | 1  <br>P | 0  <br>P | 0  <br>P | 0  <br>P | 0  <br>P | 1  <br>P | Port P « A | \-  | 2   | 1   |
|     | Write accumulator to I/O port P. |     |     |     |     |     |     |     |     |     |     |     |     |
| Read Port | RDR | 1  <br>P | 1  <br>P | 1  <br>P | 0  <br>P | 1  <br>P | 0  <br>P | 1  <br>P | 0  <br>P | A « Port P | \-  | 2   | 1   |
|     | Read the IO port P and place the result in the accumulator. |     |     |     |     |     |     |     |     |     |     |     |     |
| Keyboard input | KBI | 1   | 1   | 1   | 1   | 1   | 1   | 0   | 0   | A « KEYBD | \-  | 1   | 1   |
|     | Load accumulator with ascii value from keyboard |     |     |     |     |     |     |     |     |     |     |     |     |
| Output accumulator as ascii | ASC | 1   | 1   | 1   | 1   | 1   | 1   | 0   | 1   | VDU « A | \-  | 1   | 1   |
|     | Output accumulator as ascii |     |     |     |     |     |     |     |     |     |     |     |     |

#### Accumulator Group Instructions

| Operation | Mnemonic | OPR |     |     |     | OPA |     |     |     | Description | Carry | Bytes | Cycles |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| D3  | D2  | D1  | D0  | D3  | D2  | D1  | D0  |
| Clear both | CLB | 1   | 1   | 1   | 1   | 0   | 0   | 0   | 0   | A « 0; C « 0 | 0   | 1   | 1   |
|     | Clear accumlator and carry |     |     |     |     |     |     |     |     |     |     |     |     |
| Clear carry | CLC | 1   | 1   | 1   | 1   | 0   | 0   | 0   | 1   | C « 0 | 0   | 1   | 1   |
|     | Clear carry |     |     |     |     |     |     |     |     |     |     |     |     |
| Increment accumulator | IAC | 1   | 1   | 1   | 1   | 0   | 0   | 1   | 0   | A « A + 1 | ±   | 1   | 1   |
|     | Increment accumulator. If overflow, set carry. |     |     |     |     |     |     |     |     |     |     |     |     |
| Complement carry | CMC | 1   | 1   | 1   | 1   | 0   | 0   | 1   | 1   | C « #C | ±   | 1   | 1   |
|     | Invert carry. |     |     |     |     |     |     |     |     |     |     |     |     |
| Complement accumulator | CMA | 1   | 1   | 1   | 1   | 0   | 1   | 0   | 0   | A « #A | \-  | 1   | 1   |
|     | Invert the contents of the accumulator. This is a bitwise complement. ~0xFF=0x00. |     |     |     |     |     |     |     |     |     |     |     |     |
| Roll accumulator left | RAL | 1   | 1   | 1   | 1   | 0   | 1   | 0   | 1   |     | ±   | 1   | 1   |
|     | Roll bits in accumulator left through carry and carry to LSB of accumulator. |     |     |     |     |     |     |     |     |     |     |     |     |
| Roll accumulator right | RAR | 1   | 1   | 1   | 1   | 0   | 1   | 1   | 0   |     | ±   | 1   | 1   |
|     | Roll bits in accumulator right through carry and carry to MSB of accumulator. |     |     |     |     |     |     |     |     |     |     |     |     |
| Transmit carry and clear | TCC | 1   | 1   | 1   | 1   | 0   | 1   | 1   | 1   | A « 000C; C « 0 | 0   | 1   | 1   |
|     | Transmit carry to accumulator and clear carry. |     |     |     |     |     |     |     |     |     |     |     |     |
| Decrement accumulator | DAC | 1   | 1   | 1   | 1   | 1   | 0   | 0   | 0   | A « A - 1 | ±   | 1   | 1   |
|     | Decrement accumulator. If borrow, set carry. |     |     |     |     |     |     |     |     |     |     |     |     |
| Set carry | STC | 1   | 1   | 1   | 1   | 1   | 0   | 1   | 0   | C « 1 | 1   | 1   | 1   |
|     | Set carry. |     |     |     |     |     |     |     |     |     |     |     |     |
| Decimal adjust accumulator | DAA | 1   | 1   | 1   | 1   | 1   | 0   | 1   | 1   |     | ±   | 1   | 1   |
|     | Decimal adjust accumulator after addition. |     |     |     |     |     |     |     |     |     |     |     |     |
| Halt | HLT | 1   | 1   | 1   | 1   | 1   | 1   | 1   | 1   |     | ±   | 1   | 1   |
|     | Halts processor until test pin is used to reset it. |     |     |     |     |     |     |     |     |     |     |     |     |
