Download Link: https://assignmentchef.com/product/solved-cs2110-homework5-lc-3-introduction
<br>
So far in CS 2110 you’ve learned the basics of digital logic and how to build simple circuits, but how does all of this become relevant to computer science? This assignment will help pull it altogether as you will work step-by-step and build a functional computer! This computer will be able to run simple programs and be a stepping stone into the rest of the material in this class.

<strong>It is very important that you read ALL of this PDF before beginning this assignment, as it goes into much detail about how to do this homework.</strong>

<h1><a name="_Toc8537"></a>1           Instructions</h1>

IMPORTANT: Please make sure you are running <strong>CircuitSim 1.8.0 </strong>or else this assignment will not work!

Your friend was building his own computer in CircuitSim, but then he accidentally deleted some portions of it. Now, your friend needs your help to fill in the missing parts! The computer you will be building will be a modification of the LC-3 known as the LC-M. The LC-M supports the following instructions:

<ul>

 <li>ADD</li>

 <li>AND</li>

 <li>BR</li>

 <li>LDR</li>

 <li>LEA</li>

 <li>NOT</li>

 <li>STR</li>

 <li>HALT</li>

</ul>

We have provided LCM.sim, which has the following blank subcircuits where you will be creating various components:

<ul>

 <li>ALU</li>

 <li>CC-Logic</li>

 <li>PC</li>

 <li>ADDR</li>

 <li>SRC2MUX</li>

</ul>

You <strong>should not edit anything in the main circuit or other subcircuits</strong>, however, feel free to take a look at them to get a better understanding of the rest of a computer!

Before starting, we recommend that you have a basic understanding of the LC-M’s cousin, the LC-3. Chapters 4 and 5 of the Patt &amp; Patel textbook are a good resource, as well as Appendix A, which goes into more detail about each instruction. The only new instruction is HALT. In the LC-3, there is an instruction, TRAP, which allows for calling a variety of other pre-created instructions in memory. You will not have to worry about TRAPs for this assignment and merely interpret the TRAP opcode as HALT, which indicates the end of a program.

Figure 1: Diagram of the LC-M

<h2><a name="_Toc8538"></a>1.1         Program Counter (PC)</h2>

The PC is a 16 bit register that holds the address of the next instruction to be executed. During the FETCH stage, the contents of the PC are loaded into the memory address register (MAR), and the PC is updated with the address of the next instruction. There are two scenarios for updating the PC:

<ol>

 <li>The contents of the PC are incremented by 1.</li>

 <li>The result of the ADDR is the address of the next instruction. The output from the ADDR should be stored in the PC. This occurs if we use the branching instruction, (BR).</li>

</ol>

<h2><a name="_Toc8539"></a>1.2         Instruction Register (IR)</h2>

The IR is a 16 bit register that holds the machine code of the current instruction. During the FETCH stage, the LC-M interrogates memory for the instruction, which gets loaded into the memory data register (MDR). Then, the contents of the MDR are loaded into the IR. The contents of the IR are then decoded and executed. For more reference on how instructions are stored, check out the diagram in section 2.7.2 of the PDF.

<h3><a name="_Toc8540"></a>1.2.1         Sign Extension</h3>

There are two LC-M instructions that use constants: ADD and AND. For example, ADD R0, R1, 2 adds the constant 2 to the contents of register 1 and stores the result in register 0. This constant is called imm5 (immediate 5) and is stored in the 5 least significant bits of the instruction. These 5 bits are sign extended to 16 bits so that imm5 can be used in other components of the LC-M.

<h2><a name="_Toc8541"></a>1.3         ADDR</h2>

The ADDR is used to calculate addresses during the following instructions:

<ul>

 <li>BR | 0000 | nzp | PCoffset9 |</li>

</ul>

Here, the PCoffset9 (the 9 least signifcant bits of the instruction) is sign extended to 16 bits and added to the contents of the PC.

<ul>

 <li>LDR | 0110 | DR | baseR | offset6 |</li>

</ul>

Offset6 (the 6 least significant bits of the instruction) is sign extended to 16 bits and added to the contents of the base register, which can be accessed by SRC1OUT.

<ul>

 <li>LEA | 1110 | DR | PCoffset9 |</li>

</ul>

PCoffset9 (the 9 least signifcant bits of the instruction) is sign extended to 16 bits and added to the contents of the PC.

<ul>

 <li>STR | 0111 | SR | baseR | offset6 |</li>

</ul>

Offset6 (the 6 least significant bits of the instruction) is sign extended to 16 bits and added to the contents of the base register, which can be accessed by SRC1OUT.

Notice that in each of these instructions we are adding either SR1OUT or PC to either sign-extended offset6 or sign-extended offset9 from IR. Hint: you will need two multiplexers for this part.

<h2><a name="_Toc8542"></a>1.4         ALU</h2>

The LC-M uses an ALU to execute various instructions, such as ADD and NOT. The ALU takes in two 16 bit inputs and has four operations:

<ol>

 <li>A + B</li>

 <li>A AND B</li>

 <li>NOT A</li>

 <li>PASS A (A is unchanged)</li>

</ol>

<h2><a name="_Toc8543"></a>1.5         SRC2MUX</h2>

The LC-M’s ALU has two inputs: the A input is from SRC1OUT and the B input is from SRC2MUX. The SRC2MUX is a one-bit multiplexer that selects the second input to the ALU. It has two choices depending on the value of bit 5 in the IR:

<ol>

 <li>This refers to the contents of the second source register in the instruction. For example, for the instruction AND R0, R1, R2, the register file outputs the contents of R2 as SRC2OUT and SRC2MUX selects this as the B input to thw ALU.</li>

 <li>imm5 is the 5-bit constant in the instruction word. For example, the instruction AND R0, R1, #4, SRC2MUX selects the sign-extended 5-bit imm5 from the bits 0-4 of the instruction.</li>

</ol>

<h2><a name="_Toc8544"></a>1.6         Condition Codes (CC)</h2>

The LC-M has three condition codes: N (negative), Z (zero), and P (positive). These codes are set after the LC-M executes instructions that include loading a result into a register, such as ADD and LDR. For example, if ADD R2, R0, R1 results in a positive number, then NZP = 001. Opcodes that are followed by “+” in 2.7.2 are operations that set the condition codes. The CC subcircuit should set N to 1 if the input is negative, set Z to 1 if the input is zero, and set P to 1 if the input is positive. Only 1 bit in NZP should be set at any given time. Zero is not considered a positive number.

Hint: you are allowed to use a comparator, which can be found under Arithmetic!

<h2><a name="_Toc8545"></a>1.7         Running a Program</h2>

Now that we’ve finished building the LC-M, we want to run a program with it! We have provided a simple assembly program which you will need to translate to machine code so that you can execute it on the LC-M. The program is equivalent to:

int i = 5; while (i &gt; 0) { i = i – 2;

} with a couple extra instructions special to assembly to help test your circuit.

<h3><a name="_Toc8546"></a>1.7.1         Machine Code</h3>

So far in most Computer Science courses, you’ve likely mostly only been exposed to high-level languages such as Python or Java and while these are readable somewhat easily by a human, they cannot be directly interepreted by a computer. The language of computers is binary. The process to get to binary for a language like C (which you will learn later in this course!) is compilation to assembly and then assembling that to machine code which is written in binary. You will function as the assembler for this program.

<h3><a name="_Toc8547"></a>1.7.2         Instruction Format</h3>

The LC-M is set up to interpret instructions in a certain format. The chart below shows how instructions are stored to help you understand how to convert the assembly instructions to binary.

For any instruction, the 4 most significant bits are the opcode which indicates which instruction is being performed. For example, if an instruction said ADD R3, R3, R4, which means take the contents of register 3 and register 4, add them and store the value back into register 3, the machine code would be as follows: 0001 011 011 000 100.

Some hints: Values indicated as offset or imm simply need to sign extended to the correct number of bits (such as 3 for imm5 would be written as 00011). Values called PCoffset are determined based on the value of the PC at the time. The excel sheet you’re given indicates the PC at each instruction to assist you with these calculations.

<h3><a name="_Toc8548"></a>1.7.3         Loading the instructions to the LC-M</h3>

After you have converted the instructions to machine code, the excel document will have converted them into hexadecimal. Copy the hexadecimal into a blank text file called machinecode.txt. In the main circuit, click the RST button and then right click on the RAM component and choose ”Edit Contents”. A new window should pop up and in the upper right hand corner you should see a button that says ”Load from file”, which you can click on and then navigate to where you saved machinecode.txt and load it. The LC-M is now set up to run a program!

<h3><a name="_Toc8549"></a>1.7.4         Running the Program</h3>

You can run the program manually by clicking the CLK on and off, but you can also use the simulator tool in Circuit Sim to run it faster. It’s important to pay attention to the way the control signals change in different states. You also can double click subcircuits while the program is running to see more specifics as the program runs (such as the values changing in the registers).

When the program ends, the isHalt LED in the main circuit will be red. When running it on the highest frequency, the program should not take more than a couple seconds to run.

<h3><a name="_Toc8550"></a>1.7.5         Information about State Machines</h3>

An important part of running a program is the state machine. The state machine keeps track of what state the computer is in (such as FETCH), and outputs the proper control signals for each state (such as the value of a selector for a multiplexer). The state machine has already been built for you so that you will be able to easily run a program, but try and pay attention to see what control signals (indicated by LEDs in the main circuit) light up during different times of a program. An important note to this is that while many instructions (such as ADD and NOT) can be implemented in one clock cycle, some (such as LDR) can take even more!

Hint: The state machine always follows this format:

FETCH → DECODE → INSTR

Fetch is 3 cycles that involves incrementing the PC and loading the instruction from memory into the instruction register. The decode state is 1 cycle and uses the opcode from the instruction to determine which instruction is being executed. The instruction state takes a variable number of cycles dependent on the instruction and once completed, the computer returns to the fetch state.

<h1><a name="_Toc8551"></a>2           Testing Your Work</h1>

You can run the autograder using Gradescope which will test individual circuits as well as your machine code.

You can also run your machine code on your completed computer! One way to indicate that the program ran correctly is to look at the Register File (DPRF) after the program ran. A correctly run program will have these values in the registers:

R0: 0000

R1: 0000

R2: 0003

R3: ffff

R4: fffe

R5: 000f

R6: 0000

R7: 0000

This isn’t a guarantee that the circuit is fully correct, but a good way to measure a correctly built circuit.