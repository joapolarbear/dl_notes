# nGraph
## What is nGraph
Intel nGraph, a _soon_ to be _open-sourced C++ library_ to simplify the realization of optimized deep learning performance across frameworks and hardware platforms. (not Python)

## Frontend and Backend
* Frontend: TensorFlow, MXNet, and Intel® neon framework, NNVM
* Backends: Intel Architecture CPUs (CPU), the Intel® Nervana Neural Network Processor™ (NNP), and NVIDIA GPUs -> LLVM

## Optimizations
Currently supported (2018)
* memory management
* data layout abstraction.

## nGraph IR
* convert _framework-specific_ computation definitions into a _framework-independent_ intermediate representation (IR)
* be complied into a form that can execute on the backend
* a directed acyclic graph of stateless operation nodes.

## Framework Bridge
* TensorFlow
	* plugin registers itself as a new XLA device
	* map HLO IR (IR of XLA) to nGraph IR
	* return a compiled function

## Transformers
* each nGraph backend has a transformer
* compiles or interprets the IR for the generation of code optimized specially for the selected backed. 
* 

> A key difference between compilers for languages like C++ and compilers for deep learning frameworks is that with deep learning, the data being operated on is large and variable-sized, but highly amenable to parallelization.

## Related Work
* XLA: made no public comments about support of frameworks other than TensorFlow
* NNVM -> TVM: NNVM leaves operator set _unspecified_, which makes different frontends and backends incompatible. nGraph, XLA, and LLVM use a fixed, but extensible, IR operation set.
* *DLVM*: 
	* a project of the University of Illinois Urbana-Champaign. 
	* propose an LLVM inspired modular IR with full control flow and a side-effect free representation. 
	* It remains to be seen if the more flexible IR is capable of supporting the performance optimizations enabled by simpler data flow graph IRs like those of nGraph, XLA, and NNVM.

#