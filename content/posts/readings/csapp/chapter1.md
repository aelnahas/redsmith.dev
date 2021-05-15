---
title: "Chapter 1: A Tour of the Computer system"
date: 2021-04-25T20:22:12-04:00
draft: true
---

### Introduction and Compilation Phases

As the title indicates, this chapter gives a quick tour of the computer systems. Some of the topics discussed includes compilation phases, hardware organization, memory model, and operating systems. It starts by describing the different phases a program goes through in its compilation. 

Those phases are as follows:

- Preprocessor phase: The phase that modifies original programming according to the directive that start with #.

- Compilation phase: The process of translating the source code into its assembly representation.

- Assembler phase: Translates assembly representation into a mchine language instruction packages. Files are known as object file or relocatable object program

- Linking phase: the linker handles merging and resolving dependencies, such as including relevant info or code from an external module. At the end of this state an executable file would be produced.

### Memory 

The chapter gives a light introduction into the memory and its hierchy. It begins by definig the Register File, which is a small storage device that consists of word-sized registers. Registers are fast but are small in size. Futhermore, this inverse relation ship between a memory speed and it's size, is further illustrated via the different memory layers a computer systems can have. 

They include:

- L1, L2, L3 : which are caches that are on a processor chip.

In terms of speed L1 is the fastest while L3 is the slowest, however, the L3 is the largest in size while L1 is the smallest. 

Other memory storages such DRAM ( Main memory ) and Disk ( Local secondary memory ) are vastly larger than the cache layers but with the cost of being much slower.Additionally, now a days systems can also take advantage of remote secondary storage that can be accessed over the network.

Below is a pyramid of the cache and memory hierchy:

![memory hierchy pyramid](/img/diagrams/memory-hierchy.svg)


### Operating System

Next in this chapter an overview of the operating system is presented. The author starts by discussing the primary purposes of an operating system, which are: 

- Protecting hardware from misuse by runaway application
- Providing a uniform mechanisim for manipulating I/O devices.

The author also introduces the O/S Hardware Abstractions :

- File : Abstraction to an I/O Device
- Virtual Memory : Abstraction to I/O device and Main Memory
- Processess: Abstraction to the Processor, main memory, and I/O devices.

#### Virtual Memory

Definition: An abstraction that provide each process with the illusion that it has exclusive access to the main memory. 

Virtual Address Space: A uniform view of the memory. In it describes regions of the memory such as the Kernal Virual Memoy, regions for sshared library, run-time heap ... etc.


### Amdahl's Law 

The chapter conclude with the discussion of performanmc and the advert effects of bottlenecks. Particuarly, this section gives a definition to Amdahl's Law , which states the following:

- When we speed up a part of the system, the effect on the over all system performance depends on:
    - How significant this part was
    - How much it was sped up to 

