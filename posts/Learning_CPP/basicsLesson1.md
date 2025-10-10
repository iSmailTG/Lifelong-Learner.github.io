---
title: "Learning CPP As Pythonista: Day-1 "
author: "Ismail TG"
date: "09/19/2025"
format:
  html:
    code-fold: true
    code-line-numbers: true
    code-tools: true
    highlight-style: github
    theme: cosmo
categories: [Kernel Engineering, Cuda, Pytorch, CPP]
image: "PythonVsCPP.png"
---
## Motivation:
- In this series of lessons, I will track my learning through the universe of C++ as someone who mainly programm in Python.
- The motivation behind learning C++ in 2025 can be resumed into one word: **Cuda**.
- CUDA kernels are still C++ first, Python second. Learning the bare-metal steps so I can stop guessing and start writing fast GPU code. One small page a day until the compiler feels normal.
- This idea of learning C++ for CUDA make it less intimidating, since C++ isn't the goal here  rather just a tool for something else.
- But still I need to learn some basics of C++ in order to navigate its world that already after one studying session looks very strange and outlandish for a Python programmer.

## C++ World:
- C++ is a **Compiled language**, which means the code goes through a transformation process before it can run.
- In C++, we write codem then a special program called **Compiler** translates our *Human-readable* code into machine code (1s & 0s) that computer can executes.
- This process of compiling works in **4 stages**:
  * Stage1: Pre-processing before the compiler even looks at our code, it's a text manipulation step where the preprocessor check code that starts with `#` and  copy-past, find-replace it automatically, so the next step include only pure C++ code.
  * Stage2: The compiler reads the C++ code and converts it to **Assembly Language**, which is a human readable instructions that are very close to machine code.
  * Stage3: An **Assembler** converts assembly into actual `1s` & `0s` that the CPU understands.
  * Stage4: A **Linker** connects everything together (code, libraries, other file..) and creates the final executable file that can run.


```cpp
#include <iostream>
std::cout << "It worked!" << std::endl;

```

    It worked!



```cpp

```
