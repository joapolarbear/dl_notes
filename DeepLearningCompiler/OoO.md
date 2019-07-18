# The OoO VLIW JIT Compiler for GPU Inference

* This paper is too high level.

## Motivation

* Deep learning inference needs to scale to billions of queries per day (Amazon estimates that 90% of production ML infrastructure costs are from inference, not training)
* To achieve tight latency objective for **interactive inference (online) **, GPU is required, resulting in **poor GPU utilization**. In contrast,
  * Offline iterative minibatch <u>training</u> can achieve high level of parallelism
  * <u>Batch inference</u> queries have no strict latency deadline and are primarily concerned with maximizing throughput. System designers aim to optimize the *cost-per-query* for batch inference



## An Emergeing Utilization Gap

* <u>Throughput-optimized inference accelerators (e.g., GPU, TPU)</u> see low utilization when serving interactive DNN workloads

#### Factors of the Gap

* Tight latency objectives limit abailable parallelism
  * low latency demand -> small batch size -> low arithmetic intensity -> insufficient parallelism
* Resources must be provisioned for peak demand, in case of <u>occasional bursts in request volume</u>
* growth in compute throughput outpaces memory bandwidth. (online inference demands large memory bandwidth)



## Proposed Solution

* An *Out-of-Order (OoO) Just-in-Time (JIT)* GPU kernel *VLIW*-inspired compiler for DNN inference

#### Details

* *late-binding* and *context-aware* dynamic resource allocation:
  * leverage runtime information about the number of concurrent kernels of execution, and device context (e.g., the typical problem sizes served by a device). 
  * In contrast to tradional *early-binding* approach (e.g., CUDA API), where users need to specify the dimensionality and shape of the program without any contextual information on the available GPU resources or other kernel of execution that may be in flight.
  * Given this information, individual kernels can be
    * Retuned for better **spatial multiplexing ???**
    * Coalesced for better utilization
    * Reordered for better spatiotemporal packing

* OoO Execution for SLO Attainment
  * monitoring inference latencies per-kernel
  * Reallocate resources between tenants on-the-fly
  * In contrast to static compilers, e.g., TensorRT...
  * A dynamic JIT compiler <u>overlaps</u> waiting time from caolescing with computation from other execution streams.

* VLIW Compilation for Efficiency **???**
  * Retune superkernel ahead-of-time -> improve **co-tenancy ???**
  * Auto-tune other parameters of GPU pragrams
  * At runtime,  the VLIW JIT compiler repacks multiple small kernels into a large execution block. The compiler can apply pre-computed parameters from the auto-tuning phase to further optimize these larger kernel conigurations. 



## VLIW compilers

* Extract *instruction level parallelism* (ILP)
* Packet multiple <u>mutually-independent</u> instructions that utilize <u>diferent arithmetic processing units</u> into a single large instruction word. 
* Modify programs ahead-of-time
