# MLIR （**M**ulti-**L**evel **I**ntermediate **R**epresentation）

[MLIR: A new intermediate representation and compiler framework](https://medium.com/tensorflow/mlir-a-new-intermediate-representation-and-compiler-framework-beba999ed18d) 20190409

## What is MLIR?

* This is a <u>representation format and library of compiler utilities</u> that sits between the model representation and low-level compilers/executors that generate <u>hardware-specific code</u>
* MLIR is, at its heart, a flexible infrastructure for modern optimizing compilers. 
* This means it consists of **1)** a specification for intermediate representations (IR) and **2)** a code toolkit to perform transformations on that representation. 





## Frontends and Backends

* Fontend: 
  * TensorFlow Graph — <u>TensorFlow IR</u>, which represents all things possible in TensorFlow graphs; XLA <u>HLO IR</u>, which is designed to take advantage of XLA’s compilation abilities (with output to, among other things, TPUs)
  * TF Lite representation. TensorFlow Lite will translate to running code on mobile platforms
* Backends: 
  * An experimental affine dialect, which focuses on [polyhedral representations](https://en.wikipedia.org/wiki/Polytope_model) and optimizations
  * LLVM IR, which has a 1:1 mapping between it and LLVM’s own representation, allowing MLIR to emit GPU and CPU code through LLVM

## Adding to IR

MLIR allows you to compose transformations at every level, and you can even define your own operations and abstractions in the IR $\Rightarrow$ In this way, MLIR is more of a pure compiler *infrastructure* than LLVM.(**WHY**)



# [Multi-Level Intermediate Representation Overview](https://github.com/tensorflow/mlir)

* Ability to host high-performance-computing-style loop optimizations across kernels (fusion, loop interchange, tiling, etc) and to transform memory layouts of data.
* We do not try to support <u>low level machine code generation algorithms</u> (like register allocation and <u>instruction scheduling</u>). They are a better fit for lower level optimizers (such as LLVM)



## SSA

MLIR is an [SSA-based](https://en.wikipedia.org/wiki/Static_single_assignment_form) IR, which means that values are defined before use and have scope defined by their dominance relations.

