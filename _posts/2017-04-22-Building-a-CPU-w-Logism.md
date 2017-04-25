---
layout: post
title: Building a CPU With Logism
author: Nate Nuval
---
In my Computer Systems and Architecture course we built a simple computer in Logism. Logism is an educational tool for designing and simulating digital logic circuits. Throughout the semester, we were given different labs/homework assignments using Logism such as assembling a calculator that takes binary input and displays the decimal representation of that number, or using logic gates to create a rock, paper, scissors game. 
Building an entire CPU required us to use all the lessons we learned about the different parts that make a computer and how they interacted with each other.
#### The Arithmetic Logic Unit
![ALU](/assets/ALU.png)

The ALU takes in two 16-bit values and an operation code. The operation code represents an operation that can be performed on the 16-values (And, Or, Xor, Add, Shift left, etc…).
The output of the ALU is the result you get after performing the operation on the input values.

#### The Register File
![RegFile](/assets/RegisterFile.png)

The register file is a small memory that contains the working set of values used by the cpu. The register file takes a clock signal, a clear signal, 16-bit write data, write address, a write enable signal, and 2 read addresses as input.
The outputs of the register file are the two 16-bit read values.
 
#### The Screen Memory
![ScreenMem](/assets/ScreenMem.png)

The screen memory is similar to the register file storing 16 values each of the following are 16-bits. Each register represents a row on the screen. Each bit represents the pixel on the screen. The inputs are a clock signal, a clear signal, the 16-bit write value, the write address, a write enable signal, and a read address.

#### Fetch
![Fetch](/assets/Fetch.png)

The fetch stage of a processor contains the ROM. The ROM is where the instructions are loaded. Fetch also keeps track of which instruction to fetch next.

#### Decode
![Decode](/assets/Decode.png)

The decode stage of the processor takes the 16-bit instruction that was just fetched. Decode takes all of the information from the instruction and separates it so it can send the chunks of values to the proper piece of the CPU.

#### Reading Registers
![Read](/assets/Read.png)

This stage takes some of the inputs from the decoder into the register file. It then sends one of its outputs to the execute processor.

#### Execute
![Execute](/assets/Execute.png)

The execute processor contains the ALU.

#### The Memory System
![Memory](/assets/Memory.png)
The memory system contains the screen memory, RAM, and the inputs for the button controls.

#### The CPU
![CPU2](/assets/CPU2.png)

Here we have all the components connected. 

Red = Fetch<br />
Blue = Decode<br />
Orange = Read<br />
Green = Execute<br />
Purple = Memory<br />

Here is a video of my CPU running with a Pong program my professor wrote.
<iframe src="https://drive.google.com/file/d/0B55hEujmzuNYX3BJMVY0QVZpYm8/preview" width="640" height="480"></iframe>

I would love to go back and write some programs for this CPU.
