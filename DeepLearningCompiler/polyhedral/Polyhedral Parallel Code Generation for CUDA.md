# Polyhedral Parallel Code Generation for CUDA - 2013

* PPCG (Polyhedral Parallel Code Generator), a source-to-source compiler, compile a sequential program for parallel execution on a modern GPU
* For the first time, we provide a set of <u>algorithms</u> and a robust implementation capable of generating correct host and GPU code for any static control loop nest with <u>affine loop bounds and affine subscripts (limitation).</u>
* We do not address the selection of tile sizes, unroll factors, or any profitability consideration **???**

* A kernel consists of <u>a sequential piece of code</u> that has to be executed by a large set of threads on the GPU multiprocessors.
* generate CUDA code



## GPU Architecture model

* **Warp**: a set of threads executed on the scalar processors of a single multiprocessor in lock step 
* **CUDA Blocks**: threads within a block are all executed on the same multiprocessor
* The programmer specifies h<u>ow many blocks</u> and <u>how many threads per block</u> are assigned to the execution of a given kernel

* key factor: **bandwidth usage** $\Rightarrow$ reducing global memory accesses, by using local shared memory to exploit inter thread localitya and data reuse

* **Challenges of compiling for GPUs**

  * The compiler needs to partition the code into host and GPU code, <u>knowing that not all parallel codes are suitable for the GPU</u>
  * Mapped onto the CUDA model of blocks and threads, choice between two different levels of parallelism: 
    * independent threads: assigned to different blocks
    * cooperating threads: assigned to the same block forming warps

  * **Data locality**: compilers need to explicitly consider, schedule, and control data transfers between the different memory spaces 



## A bird’s eye view of PPCG

1. Model extraction -> pet
2. Dependence analysis -> isl
3. Scheduling
   1. Exposing parallelism and more tiling opportunities -> isl
   2. Map to host and GPU
   3. **Tiling and map to blocks and threads**
4. **Memory management**.
5. Code generation





## Extract Polyhedral Model using pet: 

#### Matrix Multiplication

```c++
void matmul ( int M , int N , int K , 
             float A [ M ][ K ] , float B [ K ][ N ] , float C [ M ][ N ]){
  for ( int i = 0; i < M ; i ++)
    for ( int j = 0; j < N ; j ++) {
      C [ i ][ j ] = 0; //S1
      for ( int k = 0; k < K ; k ++)
        C [ i ][ j ] = C [ i ][ j ] + A [ i ][ k ] * B [ k ][ j ]; //S2
    }
}
```

#### 1. Iteration Domain

$(K, N, M) \rightarrow \{S_2(i, j, k) | 0 \leq i < M \and 0 \leq j < N \and 0 \leq k < K  \} \cup$

$(K, N, M) \rightarrow \{S_1(i, j) | 0 \leq i < M \and 0 \leq j < N \}$

#### 2. Write access relation

$\{S_2(i, j, k) \rightarrow C(i, j)\} \cup \{S_1(i, j) \rightarrow C(i, j)\} $

#### 2. Read access relation

$\{S_2(i, j, k) \rightarrow A(i, k)\} \cup \{S_2(i, j, k) \rightarrow B(k, j)\} \cup \{S_2(i, j, k) \rightarrow C(i, j)\} $

#### 3. Schedule

* specifies the order in which the statement instances are executed
* $\{S_2(i, j, k) \rightarrow (i, j, k, 1)\} \cup \{S_1(i, j) \rightarrow (i, j, 0, 0)\}$



## Tiling

- Definition: replace the original dimension by two new diminsions
- may increase the chances of exploiting data reuse in the shared memory, **but** no analysis supports this
- PPCG takes the *tile* sizes, *thread block* sizes and *grid* sizes as user parameters.
  - Pros: 1) user can fine tune these parameters for a given algorithm. 2) reduce the complexity for the final code generation stage
  - Cons: it is hard for a non-expert user to find optimal parameters
- For the above example: 
  - $(b_0, b_1, t_0, t_1) \rightarrow \{ (I, J, K, i, j, k, s) | \exist \alpha_0, \alpha_1, \beta_0, \beta_1: I = 16 \alpha_0 + b_0 \and J = 16 \alpha_1+ b_1 \and i = 8 \beta_0 + t_0 \and j = 16 \beta_1 + t_1 \}$
  - where $0 \leq b_0 < 16, 0 \leq b_1 < 16, 0 \leq t_0 < 8, 0 \leq t_1 < 16$ are parameters refering to the block and thread identifiers
  - $(I, J, K, i, j, k, s)$ are the loop iterators.



## Scheduling

### Dependency analysis

- The dependence relation can be obtained using standard techniques (FEAUTRIER, P. 1991. Dataflow analysis of array and scalar references. Int. J. Parallel Prog. 20, 1, 23–53.) 
- PPCG, it is performed using *isl*.
- **Dependence relation**:  $\{S_1(i, j, 0, 0) \rightarrow S_2(i, j, 0, 1)\} \cup \{S_2(i, j, k, 1) \rightarrow S_2(i, j, k+1, 1)\} $
- Dependence distances: $\{(0, 0, 0, 1), (0, 0, 1, 0)\}$ 
  - <u>**lexicographically positive**</u> $\Rightarrow$ statement instances only depend on earlier statement instances (**What we want to ensure**)
  - no dependence between $i, j$, the first two schedule dimensions are parallel

### Exposing parallelism and tiling opportunities

- The <u>dependence relation</u> and the previously computed parts are used to construct an **ILP** problem 
  - over the **schedule coefficients**
  - Constraints: Dependence distances is positive
  - target: minimal dependence distance. 
  - In the optimal case, the dependence distance is zero (for each dimension) and the loop corresponding to this schedule dimension is parallel (can be tiled)
- Mainly refer to ***Pluto*** algorithm (2008)
  - A <u>fully automatic</u> polyhedral source-to-source transformation framework that can optimize regular programs for <u>parallelism and locality</u> simultaneously
  - Target: finding good ways of  <u>**1)** tiling for parallelism and **2)** locality</u> using affine transformations
- Band*s ???

### Map to host and GPU

* Find the outermost bands containing at least one parallel loop, before $\Rightarrow$ CPU, after $\Rightarrow$ GPU

### Tiling and map to blocks and threads

* The outermost (up to) two parallel tile loops are mapped to the <u>blocks</u> in the grid
* The innermost (up to) three parallel point loops are mapped to the <u>threads</u> in a block





## MEMORY ALLOCATION

### Allocation to registers and shared memory

* Shared memory: 
  * for each <u>reference group</u> (combination of loop iterators)
  * find a constant-size block in the A array with an offset that may depend on the tile (and outer) loop iterators that covers the set of accessed elements
* Register
  * first need to check that each element is only accessed by a single thread and we
    do so by checking that this mapping is injective
  * Secondly, we check that the accessed element does not depend on any inner
    loops, ensure the index expression is constant, a requirement for
    registers

* principle
  * If the data can be put in registers and there is reuse, then put it in registers.
  * Otherwise, if the data can be put in shared memory and either there is reuse or the access is non-coalesced, then put it in shared memory.
  *  Otherwise, put it in global memory.