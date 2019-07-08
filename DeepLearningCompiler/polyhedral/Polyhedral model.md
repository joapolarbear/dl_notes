# Polyhedral model

## What is Polyhedral model?

The polyhedral model is a <u>compilation technique</u> which 
* treats each loop iteration within nested loops as [lattice points](https://en.wikipedia.org/wiki/Lattice_points) inside mathematical objects called [polyhedra](https://en.wikipedia.org/wiki/Polytope) 
* performs [affine transformations](https://en.wikipedia.org/wiki/Affine_transformation) or more general non-affine transformations such as [tiling](https://en.wikipedia.org/wiki/Loop_tiling) on the polytopes
* converts the transformed polytopes into equivalent, but optimized (depending on targeted optimization goal), loop nests through polyhedra scanning.

* Nested loop programs are the typical, but not the only example, and the most common use of the model is for [loop nest optimization](https://en.wikipedia.org/wiki/Loop_nest_optimization) in [program optimization](https://en.wikipedia.org/wiki/Program_optimization) 



[Introduction to Polyhedral Compilation](https://www.slideshare.net/ahayashi10/introduction-to-polyhedral-compilation)

## Why Polyhedral Model
> The Polyhedral Model is a convenient alternative representation which combines analysis power, expressiveness and high flexibility” - OpenScop Speciﬁcation and Library

* In contrast to Abstract Syntax Tree 
* One solution for tackling the phase-ordering problem
	![image-20190708141316908](/Users/hhp/Library/Application Support/typora-user-images/image-20190708141316908.png)
* Good for performing a set of loop transformations 
	* Loop permutation (interchange) : stride access or offset access
	* Loop fusion/distribution: 
	![image-20190708141234746](/Users/hhp/Library/Application Support/typora-user-images/image-20190708141234746.png)
	* Loop tiling


## Schedules
![image-20190708141523539](/Users/hhp/Library/Application Support/typora-user-images/image-20190708141523539.png)

* $s$ denotes a statement, $\vec{i} $ denotes the iteration vector
* Function $T$: return the logical state of each statement

### An Example: Loop permutation

![image-20190704133939758](/Users/hhp/Library/Application Support/typora-user-images/image-20190704133939758.png)



## 3 important things

- Domain: a set of instances for a statement
- Scattering (Scheduling): an instance -> time stamp (function $T$)
- Access: an instance -> array elements

## Limitation

- Only applicable for Static Control Part (SCoP) in general 
  -> Loop bounds and conditions are affine function of the surroundings the loop iterators.

## Iteration Domain

![image-20190704133716445](/Users/hhp/Library/Application Support/typora-user-images/image-20190704133716445.png)

* <u>If those constraints are affine -> Polyhedron (limitation)</u>

## Legality

![image-20190704134317724](/Users/hhp/Library/Application Support/typora-user-images/image-20190704134317724.png)

![image-20190704134332306](/Users/hhp/Library/Application Support/typora-user-images/image-20190704134332306.png)

