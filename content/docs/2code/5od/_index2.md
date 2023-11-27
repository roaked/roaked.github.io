---
title: "Genetic Algorithm"
weight: 2
---

# # **Time Tetris: Genetic Algorithms**

{{< columns>}}
![sa](https://user-images.githubusercontent.com/36581610/78828900-6d6a2080-79b3-11ea-81a3-bd284849c3f2.gif)
<--->
![asd](https://user-images.githubusercontent.com/36581610/78828927-79ee7900-79b3-11ea-9b25-936f19c4bf4a.gif)
{{< /columns>}}

{{< details "Source" close >}}
[Source](https://github.com/greerviau/TetrisAI)
{{< /details>}}


## 1 Theoretical Remarks

Genetic Algorithm (GA) stands as an adaptive method employed for resolving search and optimization dilemmas. Operating across numerous generations, it simulates the evolutionary process observed in natural populations, adhering to the principles of natural selection and the concept of "survival of the fittest." By emulating this phenomenon, genetic algorithms possess the capability to "evolve" solutions for real-world problems, provided they have been encoded effectively.

While GA does not assure convergence or the discovery of the global optimal solution, it does tend to furnish an "acceptably good" solution relatively swiftly, on average. Typically, GA is extensively tailored to suit specific applications, resulting in a myriad of variants. Consequently, pinning down a singular "generic" or "traditional" GA proves challenging due to this diversity. Nevertheless, a study of the foundational concepts behind early GA, along with exploration of its various adaptations, enables the identification of key operations, culminating in a conceptualization of a "traditional" GA.

The fundamental components of the "traditional" GA encompass a fitness function, a selection technique, and fixed-probability-driven crossover and mutation operators. The population comprises all chromosomes, treated as individuals, with each iteration referred to as a generation, signifying an iterative updating of the population. Chromosomes, composed of genes reflecting parameters for optimization, embody potential solutions to the optimization problem, facilitating the simultaneous evaluation of multiple solutions.

The GA's dimension corresponds to the search space dimension, equating to the number of genes housed within each chromosome. The fitness function assumes a pivotal role, enabling the GA to assess the performance of each chromosome within the population. Tailored for each specific problem, the fitness function serves as the sole bridge between the GA and the application itself, necessitating a careful and precise selection. Its design must accurately mirror the application's parameters and their optimization criteria.

The selection operator plays a critical role by electing chromosomes from the current generation to act as parents for the subsequent generation. This selection process favors the most fit individuals, and the probability of each chromosome's selection adheres to a specific scheme, dictating the genetic inheritance for the subsequent iterations.

{{< katex display >}}
P_s(i) = \frac{f(i)}{\sum_{J=1}^{N} f(J)}
{{< /katex>}}

The process described outlines the selection, crossover, and mutation stages within the Genetic Algorithm (GA) framework:

1. **Selection**:

- {{< katex>}}F_s(i){{< /katex>}} represents the probability of selection for the {{< katex>}}i^{th}{{< /katex>}} chromosome, while f(i) denotes its fitness value.
- Chromosomes are selected in pairs, ensuring diversity by re-normalizing probabilities after each selection to avoid selecting the same chromosome.
- Each pair consists of two distinct chromosomes, allowing for multiple associations of a single chromosome across different pairs.

2. **Crossover**:

- As the primary local search process, crossover drives the generation of new offspring from selected parent pairs.
- Two offspring are generated for each parent pair selected by the selection operator.
- Prior to the algorithm's execution, a predetermined probability of crossover determines whether each parent pair undergoes crossover or replication.
- Replication leads to offspring identical to their parents, while crossover involves converting parent pairs to binary notation and exchanging bits beyond a randomly chosen crossover point to form the offspring pair.

This iterative process, involving selection of diverse parent pairs, crossover, and mutation of offspring, fuels the continual evolution and generation of new solutions within the GA framework.

![2](https://live.staticflickr.com/65535/53359214517_b44b86b342_b.jpg)

3. **Mutations**: are global searches. A predetermined probability of mutation, established before the algorithm's initiation, is applied to every individual bit of each offspring chromosome. This probability governs whether a specific bit within an offspring chromosome will be inverted.

![3](https://live.staticflickr.com/65535/53360310243_b6be608583_h.jpg)

The elitist operator in GA's serves to maintain or improve the algorithm's performance throughout its progression. It accomplishes this by replicating the best chromosome into the succeeding generation, bypassing both crossover and mutation operators. This strategy ensures that the best-performing chromosome, in terms of fitness, remains constant or improves, preventing a decrease in its fitness value.

{{< hint tip >}}
The termination criteria is based upon:

- Number of generations
- Fitness of the best individual in the population
- Average fitness of the entire population
- Differences in the best fitness or average fitness values across successive generations.
{{< /hint >}}

## 2 Code Implementation

Initially, implementing the Best Fit Decreasing (BFD) method was way too simple. Hence, I decided to ommit implementation and results -- since it's a basic heuristic and doesn't need a complex cost function. It was coded from scratch in Matlab.

However, the GA is a bit more intricate. Instead of creating these algorithms from scratch, existing Matlab codes from online sources were adapted and optimized to suit the problem's requirements and parameters.

For GA to work, a cost function is needed. This function must consider the main goal (minimizing the number of bins) and the constraints (all weights should fit within the bin capacity). If a bin's capacity is exceeded, it's termed a "bin violation." Initially, the attempt was made to prevent any bin violations from occurring during the simulation. However, this led to the algorithms getting stuck at a suboptimal solution of 16 bins for a problem where the optimal solution was 15 bins. It was challenging for the algorithms to redistribute weights across bins without exceeding their capacities in a single step.

A different approach, found in literature, allowed bin capacities to be exceeded temporarily during iterations. In this approach, bin violations were included in the cost function, aiming to minimize them to zero. So, the algorithms started with several bin violations, but reducing these led to improvements in the cost function, unveiling better solutions.

The cost function used became: {{< katex >}} J = nBin + \alpha \times MeanViol + LBPenalty   {{< /katex >}}

{{< hint tip >}}
The variables are described as:

- nBin: Number of bins used
- MeanViol: Average of violations across the bins
- α: Tuning parameter - if low converges to solutions with fewer bins than optimal. if too high, the solutions are feasible, but not optimal
- LBPenalty: Prevents algorithms from converging to a number of bins below a certain lower bound.
{{< /hint >}}

To solve the bin packing problem with these algorithms, the program received a set of items with weights that needed to be assigned. A position vector was created and shuffled to obtain a solution. For example, if weights were [20, 15, 10, 5], shuffling them to [2, 3, 1, 4] meant arranging them as [15, 10, 20, 5]. Each item needed at least one decision variable in the position vector.

Items had to be assigned to different bins. More variables were added inside the position vector to handle this. For instance, [2, 3, 5, 1, 4] could mean two bins: [15, 10] and [20, 5]. Integers higher than the number of items represented bin divisions. Stopping criteria included a maximum number of iterations, adjusted according to the number of items to be assigned due to the inherent randomness in these heuristics.

Another criterion introduced is a "stuck counter." Often, it's ambiguous whether the algorithm is stuck in a local minimum or encountering difficulties in finding an improved solution. To address this, if the program identifies that the best cost remains unchanged from the previous iteration, it initiates a count of iterations. Upon surpassing a predetermined limit, the program halts. Additionally, if the best cost is an integer value, it suggests that the program might have discovered a feasible solution and is trapped within it. This "stuck counter" proves to be a valuable stopping criterion.

(to add code segments later)

{{< hint tip >}}
[If you would like to return to the section explaining the abstract and analogies of evolutionary computation and metaheuristics click here ✌️](https://ricardochin.com/docs/2code/5od/){{< /hint >}}

{{< hint tip >}}
[If you would like to return to advance to the implementation using particle swarm optimization click here ✌️](https://ricardochin.com/docs/2code/5od/_index3/){{< /hint >}}