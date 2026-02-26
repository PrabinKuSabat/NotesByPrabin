# Chapter Wise Notes

## Chapter 2

1. [[Basic Assembler Functions]]  
	Section 2.1 focuses on the fundamental design of an assembler using the **SIC (Simplified Instructional Computer)** architecture. It transitions from theoretical machine structure to the practical software required to translate human-readable mnemonics into executable machine code.
2. [[Machine Depedent Assembler Features]]  
	Section 2.2 of the sources focuses on **Machine-Dependent Assembler Features**, specifically those required to support the **SIC/XE architecture**. These features are critical for both industry practitioners (understanding low-level optimization and memory management) and students preparing for exams (mastering displacement calculations and relocation logic).

## Chapter 3

1. [[Basic Loader Functions]]  
	Welcome to this advanced session on systems architecture. Today, we are exploring the foundational component of the program execution cycle: **Section 3.1, Basic Loader Functions**. While the assembler creates the object code, that code is useless until a **loader** places it into main memory and prepares it for execution. In a sophisticated system, the "loading" process involves three distinct, high-level activities: **loading** (bringing the object program into memory), **relocation** (modifying the program so it can run at a different address than originally specified), and **linking** (resolving symbols between independent program segments). In this introductory section, however, we focus on the most primitive yet essential form of this tool: the **Absolute Loader**.
2. [[Machine Dependent Loader Features]]  
	As we move deeper into the architecture of system software, we transition from the simplistic absolute loader to the significantly more complex and versatile **Section 3.2: Machine-Dependent Loader Features**. In a professional engineering context, the absolute loader we discussed previously is a "toy" implementation; real-world systems require loaders that can handle **Relocation** and **Program Linking**, allowing multiple independently assembled modules to function as a singular, cohesive executable in a dynamic memory environment.
