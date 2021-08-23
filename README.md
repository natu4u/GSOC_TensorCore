# GSOC_TensorCore
TensorCore Vector Processor for Deep Learning - Google Summer of Code Project
![GSoC - Vertical Wide - Gray Text - White BG](https://user-images.githubusercontent.com/61764809/130463041-5dd7a87e-301f-40c8-8e96-9870d8a67aa6.png)

## Project Description
This project focuses on implementing a Deep Learning hardware accelerator called TensorCore - which is a Vector Processor based on v0.10 of the open source RISCV Vector ISA.
It is expected to serve as a proof-point for a Deep Learning research platform to experiment with tensor operators and custom number systems. The focus is on delivering core instructions required for deep learning and linear algebra applications. This design can be used as a starting point to study tensor core based designs to accelerate Deep learning workloads at greater scale.

## Project Goals
1. Research the architecture and micro-architecture details of vector processor and study currently available commercial and academic vector processors geared towards DL    
   computations.
2. Micro-architecture design and implementation of a simple open-source Tensor Core RISC-V accelerator to serve as a proof of concept -which can then be used as a starting point 
   for students to learn about the architecture and design of vector processor for deep learning as most of the available designs are either commercially licensed or complex for  
   students at university level to appreciate the advantages of vector processors for these applications. 
3. Create a test bench for executing the tests on our implementation and validate the core design in simulation - eg., SAXPY, dot product, etc.
4. To serve as a proof-point for a Deep Learning research platform to experiment with tensor operators (like fused dot products)and custom number systems(like Posits) . This 
   design can be later used/modified to study tensor core based designs to accelerate Deep learning workloads at greater scale with custom instructions.

To achive these goals, The TensorCore vector execution engine needs to supports the following instructions - 
a. Vector load
b. Vector load
c. Vector scale (VS operation)
d. Vector add (VV or VS)
e. Vector dot product (DOT)

## Goals Achieved
We've been able to design the micro-architecture of a singe lane and single VFU based Vector Processor. Currently,our design the supports the following instructions completely - 
a. Vector 32 element load
b. Vector 32 element store
c. Vector Scale (or VS multiply)
d. Vector Multiplication (or VV multiply)
e. Vector Addition (both VS and VV)

## Ruuning the SAXPY example
The SAXPY is one of the most common vector operations frequently encountered in linear algebra applications. Our SAXPY test is described as follows - 
1. Load a scalar value of '2' into the scalar register file address location 0x2.
2. Load vector register 0x1 with 32 elements in the following pattern - (4, 3, 2, 1, 4, 3, 2, 1……, 4, 3, 2, 1).
3. Load vector register 0x2 with 32 elements  in the following pattern - (-8, -6, -4, -2, -8, -6, -4, -2, ……, -8, -6, -4, -2).
4. Compute the VS product between the scalar value in 0x2 of scalar RF with each element of vector register 0x1. Store the result back in vector register 0x2. Now the VR 0x1 has   
   values (8, 6, 4, 2, 8, 6, 4, 2, ….).
5. Compute vector addition (VV) between vector registers 0x1 and 0x2 and store the result back into vector register 0x3. Since the values are complements, the ALU output of this 
   operation and final result stored in vector register 0x3 will be 0s in all 32 elements.
6. Store this result from vector register 0x3 into memory using store operation. Since the memory interface is not a part of this project, the data is stored in a queue from which 
   it can be transferred to a memory.
   
The test bench can be modified or other tests can be added to run other workloads that use the above defined operations. 

## Caveats
1. In the current implementation, the decoder, load and store unit are all implemented in the "sequencer module" - for the basic operations that are required for this project. A 
   more extensive decoder implementation can be found in the TLV directory
2. The current v0.10 of the RISCV Vector spec does not define the instruction encoding of a VV Integer dot product. To implement this, we've defined our own custom encoding for 
   this operation. The dot product instruction implementation is still under progress although the elementary operations required to compute the dot product are already supported
3. The current design works on vectors with LMUL = 1 and elements with 32 bit precision only. The RISCV Vector extension supports elements with multiple bit precisions and 
   multiple ways of grouping elements using different LMUL values. This will be the next goal for future work
4. Since the design is still a work in progress, some additional control flow signals are included in the design to control the execution of of some operations explicitly from the 
   test bench. These signals have been referred as such in the comments wherever appropriate. Later, the sequencer and the decoder will communicate directly using internal signals 
   without any explicit control through the test bench.

## Future Work
1. Implement the dot product(DP) and the fused dot product (FDP) instruction with Reproducible Computation & Rounding Approach. 
   Reproducible computation requires control over rounding decisions. The fused dot product uses a quire to defer rounding till the end of the dot product. The cycle time of the 
   quire needs to be balanced with the throughput of the multiply unit. The benefit of the quire is that the precision demands of the source number system are reduced. Research 
   has shown that a 32-bit floating-point fused-dot product (FDP) can beat the precision of a 64-bit floating-point that uses fused-multiply-add (FMA). 
2. To exploit the full potential of vector processors, we need to execute multiple instructions in parallel. This would require each lane to have multiple pipelined VFUs geared 
   towards specific numerical operations(integer, floating point)and multiple such lanes to perform 4–8 parallel operations. 
3. Complete the TLV implementation of the design and test it using the sandpiper-verilator test infrastructure - to demonstrate the power of TLV to easily implement pipelined VFUs 
   and create a design that is much less verbose and less prone to bugs.

## Contributions and link to future work
This repository is a culmination of the GSOC 2021 project. Hence, it cannot be modified after the end of the programme. All the future work and modifications will be carried out in a spearate public repository the link to which I'll be adding at the end of the GSOC Programme. 

## Bug reporting
We've tested the design with SAXPY and multiple other examples. But a design of this scope is still prone to bugs as we've not been able to perform extesive Verification. All the bugs/improvements cab be reported to nmishra9@asu.edu. We greatly appreciate your contributions in this regard.

## Contributors
Theodore Omtzigt (https://github.com/stillwater-sc), Primary Mentor\
Akos Hadnagy (https://github.com/ahadnagy), Secondary Mentor\
Steve Hoover (https://github.com/stevehoover), Advisor\
Nitin Mishra, Design & Verification Engineer
