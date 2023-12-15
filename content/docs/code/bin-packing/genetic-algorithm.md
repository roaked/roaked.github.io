---
title: "Genetic Algorithm"
weight: 2
---

# **Time Tetris: Genetic Algorithms**

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

{{< hint important >}}
The termination criteria is based upon:

- Number of generations
- Fitness of the best individual in the population
- Average fitness of the entire population
- Differences in the best fitness or average fitness values across successive generations.
{{< /hint >}}

## 2 Code Implementation

Initially, implementing the Best Fit Decreasing (BFD) method was way too simple. Hence, I decided to ommit implementation and results -- since it's a basic heuristic and doesn't need a complex cost function. It was coded from scratch in Matlab.

However, the GA is a bit more intricate. Instead of creating these algorithms from scratch, existing Matlab codes from online sources were adapted and optimized to suit the problem's requirements and parameters. The code can be seen at [the following GitHub repo](https://github.com/roaked/genetic-algorithm-optimization/tree/main/bpp).

### 2.1. Cost Function and Stopping Criteria

For GA to work, a cost function is needed. This function must consider the main goal (minimizing the number of bins) and the constraints (all weights should fit within the bin capacity). If a bin's capacity is exceeded, it's termed a "bin violation." Initially, the attempt was made to prevent any bin violations from occurring during the simulation. However, this led to the algorithms getting stuck at a suboptimal solution of 16 bins for a problem where the optimal solution was 15 bins. It was challenging for the algorithms to redistribute weights across bins without exceeding their capacities in a single step.

A different approach, found in literature, allowed bin capacities to be exceeded temporarily during iterations. In this approach, bin violations were included in the cost function, aiming to minimize them to zero. So, the algorithms started with several bin violations, but reducing these led to improvements in the cost function, unveiling better solutions.

The cost function used became: {{< katex >}} J = nBin + \alpha \times MeanViol + LBPenalty   {{< /katex >}}

{{< hint note >}}
The variables are described as:

- nBin: Number of bins used
- MeanViol: Average of violations across the bins
- α: Tuning parameter - if low converges to solutions with fewer bins than optimal. if too high, the solutions are feasible, but not optimal
- LBPenalty: Prevents algorithms from converging to a number of bins below a certain lower bound.
{{< /hint >}}

As per seen below, the function takes `pos` and `model` input parameters to represent the allocation of item to bins and problem parameteres `n`, `w` and `c`, respectively. To start with, `Sep` need to be verified by checking the position where bins are divided in the `pos` vector. Consequently, divides the position vector into bins `B` based on the separators.

```matlab
function [J, sol] = GA_BinPackingCost(pos, model)

    n = model.n;
    w = model.w;
    c = model.c;
    
    Sep = find(pos>n);
    
    From = [0 Sep] + 1;
    To = [Sep length(pos)+1] - 1;
    
    B = {};
    for i=1:length(From)
        Bi = pos(From(i):To(i));
        if numel(Bi)>0
            B = [B; Bi]; %#ok
        end
    end
    
    nBin = numel(B);
    Viol = zeros(nBin,1);
    for i=1:nBin
        Vi = sum(w(B{i}));
        Viol(i) = max(Vi/c-1, 0);
    end
    
    MeanViol = mean(Viol);
    
    if n<100
        alpha = 0.7*n;
    else
        alpha = 1.25*n;
    end
    %sum(w)/c = lower bound of bins needed
    J = nBin + alpha*MeanViol + max(sum(model.w)/model.c - nBin,0)*10;
    
    sol.nBin = nBin;
    sol.BPos = B;
    sol.Viol = Viol;
    sol.MeanViol = MeanViol;
end
```

To solve the bin packing problem with these algorithms, the program received a set of items with weights that needed to be assigned. A position vector was created and shuffled to obtain a solution. For example, if weights were [20, 15, 10, 5], shuffling them to [2, 3, 1, 4] meant arranging them as [15, 10, 20, 5]. Each item needed at least one decision variable in the position vector.

Items had to be assigned to different bins. More variables were added inside the position vector to handle this. For instance, [2, 3, 5, 1, 4] could mean two bins: [15, 10] and [20, 5]. Integers higher than the number of items represented bin divisions. Stopping criteria included a maximum number of iterations, adjusted according to the number of items to be assigned due to the inherent randomness in these heuristics.

Another criterion introduced is a "stuck counter." Often, it's ambiguous whether the algorithm is stuck in a local minimum or encountering difficulties in finding an improved solution. To address this, if the program identifies that the best cost remains unchanged from the previous iteration, it initiates a count of iterations. Upon surpassing a predetermined limit, the program halts. Additionally, if the best cost is an integer value, it suggests that the program might have discovered a feasible solution and is trapped within it. This "stuck counter" proves to be a valuable stopping criterion.

### 2.2. Main Function

Analogous to previous chapter, the algorithm is started by initializing the population. This is done by creating a structure `empty_individual` and then populating pop with random positions for each individual. The fitness cost of each individual is also evaluated using the provided objective function.

```matlab
empty_individual.Position = [];
empty_individual.Cost = [];
empty_individual.Sol = [];

pop = repmat(empty_individual, nPop, 1);

for i = 1:nPop
    % Initialize Position
    pop(i).Position = randperm(nVar);
    
    % Evaluate Fitness (Cost) based on the objective function
    [pop(i).Cost, pop(i).Sol] = CostFunction(pop(i).Position);
end
```

Following to population initialization, the parents individuals are selected using roulette wheel selection. Afterwards, crossover to generate offspring is performed, and again, the fitness of the offspring based on the objective function is evaluated.


```Matlab
popc = repmat(empty_individual, nc/2, 2);

for k = 1:nc/2
    % Select Parents using Roulette Wheel Selection
    i1 = GA_RouletteWheelSelection(P);
    i2 = GA_RouletteWheelSelection(P);
    p1 = pop(i1);
    p2 = pop(i2);
    
    % Apply Crossover
    [popc(k, 1).Position, popc(k, 2).Position] = GA_PermutationCrossover(p1.Position, p2.Position);
    
    % Evaluate Offsprings' Fitness
    [popc(k, 1).Cost, popc(k, 1).Sol] = CostFunction(popc(k, 1).Position);
    [popc(k, 2).Cost, popc(k, 2).Sol] = CostFunction(popc(k, 2).Position);
end
popc = popc(:);
```

The `GA_PermutationCrossover` and `GA_RouletteWheelSelection` are relatively simple implementations. `GA_PermutationCrossover`  performs a permutation-based crossover operation for GAs on two parent individuals `x1` and `x2`. On the other hand, tThe function `GA_RouletteWheelSelection` is an utility function used in GAs for selecting individuals based on a roulette wheel selection scheme. Given a set of probabilities `P`, it selects an individual index based on these probabilities using a random number `r`. This function helps in the selection process during crossover operations.

```Matlab
function [y1, y2] = GA_PermutationCrossover(x1,x2)

    nVar=numel(x1);
    
    c=randi([1 nVar-1]);
    
    x11=x1(1:c);
    x12=x1(c+1:end);
    
    x21=x2(1:c);
    x22=x2(c+1:end);
    
    r1=intersect(x11,x22);
    r2=intersect(x21,x12);

    x11(ismember(x11,r1))=r2;
    x21(ismember(x21,r2))=r1;
    
    y1=[x11 x22];
    y2=[x21 x12];

end

function i = GA_RouletteWheelSelection(P)

    r=rand;
    
    C=cumsum(P);
    
    i=find(r<=C,1,'first');

end
```

This process ensures that the offspring have a mixture of segments from both parents while maintaining the uniqueness of elements within segments. Further on, part of the individuals from the population for mutation are selected for mutation. Hence, their positions are mutated and the fitness of the resulting mutants using the provided objective function is also evaluated.

```
popm = repmat(empty_individual, nm, 1);

for k = 1:nm
    % Select Parent Index
    i = randi([1, nPop]);
    
    % Select Parent
    p = pop(i);
    
    % Apply Mutation
    popm(k).Position = GA_PermutationMutate(p.Position);
    
    % Evaluate Mutant's Fitness
    [popm(k).Cost, popm(k).Sol] = CostFunction(popm(k).Position);
end

```

The function `GA_PermutationMutate` randomnly selects one of three mutation operations: swap, reversion, or insertion, and applies the chosen mutation to the individual `x`. 

```
function y = GA_PermutationMutate(x)
    M=randi([1 3]);   
    switch M
        case 1
            % Swap
            y=DoSwap(x);
            
        case 2
            % Reversion
            y=DoReversion(x);
            
        case 3
            % Insertion
            y=DoInsertion(x);            
    end
end

function y=DoSwap(x)   
    n=numel(x);
    
    i=randsample(n,2);
    i1=i(1);
    i2=i(2);

    y=x;
    y([i1 i2])=x([i2 i1]);
    
end

function y=DoReversion(x)

    n=numel(x);
    
    i=randsample(n,2);
    i1=min(i(1),i(2));
    i2=max(i(1),i(2));

    y=x;
    y(i1:i2)=x(i2:-1:i1);

end

function y=DoInsertion(x)
    n=numel(x);  
    i=randsample(n,2);
    i1=i(1);
    i2=i(2);
  
    if i1<i2
        y=[x(1:i1-1) x(i1+1:i2) x(i1) x(i2+1:end)];
    else
        y=[x(1:i2) x(i1) x(i2+1:i1-1) x(i1+1:end)];
    end
end
```

Basically, it selects one of the mutation operations based on a `randomized M`and applies it to the input permutation `x`, resulting in a modified permutation `y`.

{{< hint tip>}}
M is a random integer between 1 and 3, determining the type of mutation to be performed.
M = 1: Swap mutation.
M = 2: Reversion mutation.
M = 3: Insertion mutation.
{{< /hint>}}

These mutation operations introduce diversity in the population by altering individuals to explore different regions of the search space in the genetic algorithm. In the end, as showcased, the best solution found as well as the worst cost are tracked and the termination conditions to break out of the main loop based on certain criteria are also described.

## 3 Results

### 3.1. First Benchmark

In the GA algorithm, the primary parameters for adjustment include population size (nPop), crossover probability (pc), and mutation probability (pm). Initial trials utilized high crossover and low mutation probabilities, aligning with common recommendations. Each parameter set underwent 100 tests to offer a comprehensive understanding of their impact. Results for a population size of 100 are detailed below.


| Crossover | Mutation | # Optimal | # Non-Opt | # Non Feasible | AVG Time (s) | Best Time (s) |
|----------|----------|-----------|------------|----------------|--------------|---------------|
| 0.8      | 0.1      | 57        | 25         | 18             | 16.7065      | 2.9367        |
| 0.4      | 0.1      | 60        | 23         | 17             | 10.2069      | 2.2047        |
| 0.4      | 0.5      | 79        | 12         | 9              | 8.4275       | 2.6534        |
| 0.1      | 0.9      | 80        | 12         | 8              | 7.5532       | 3.0711        |
| 0.1      | 0.5      | **81**    | 6     | 13             | **7.1172**     | **2.1431**        |
| 0.2      | 0.8      | 75        | 10         | 15             | 9.0226       | 3.1714        |


The bold highlights denote the best outcomes. Surprisingly, a higher mutation probability proves advantageous for this specific problem, whereas the crossover probability appears less influential. The most favorable result achieved 81 optimal solutions out of 100 simulations, a decent outcome but with room for improvement. Reducing the population size to 50 and retesting yielded the results detailed as follows:

| Crossover | Mutation | # Optimal | # Non-Opt | # Non Feasible | AVG Time (s) | Best Time (s) |
|-----------|----------|-----------|------------|----------------|--------------|---------------|
| 0.8       | 0.1      | 43        | 27         | 30             | 13.1624      | 2.2969        |
| 0.4       | 0.1      | 34        | 26         | 40             | 8.3888       | 1.5564        |
| 0.4       | 0.5      | 66        | 19         | 15             | 6.2721       | 1.4918        |
| 0.1       | 0.9      | 78        | 14         | 8              | 4.7672       | 1.7086        |
| 0.1       | 0.5      | 75        | 13         | 12             | **4.6430**   | **1.334**     |
| 0.2       | 0.8      | **83**    | 8          | 9              | 5.3174       | 1.6009        |

When the population size reduced to 50, the results remained close to those with 100. Notably, simulations ran faster with the smaller population, requiring a higher mutation probability for good outcomes (50% mutation wasn’t sufficient for 50 individuals, while it performed well with 100). Optimal results — 83 in total — were achieved with a 0.2 crossover, 0.8 mutation, and a population of 50. Although some parameter sets were faster, their lower accuracy led to their exclusion.

Further tests with varying population sizes indicated that a higher population slowed simulations without improving optimal solutions. Conversely, lower populations sped up the process but resulted in fewer optimal solutions due to the algorithm getting stuck in local minimums. Hence, a population size of 50 proved optimal.

{{< hint info>}}
Attempts to modify and test the selection probabilities function yielded inconclusive results.
{{< /hint>}}


In the initial benchmark, the GA underwent testing with the parameters: population size (nPop) = 50, crossover probability (pc) = 0.2, and mutation probability (pm) = 0.8. A single simulation's outcome is depicted as follows:

![wqdj](https://live.staticflickr.com/65535/53359214487_89cbbc4d1d.jpg)

![3i](https://live.staticflickr.com/65535/53359214442_857c059317_b.jpg)

In this simulation, the GA algorithm reached a stopping criterion after 390 iterations, concluding within 7.55 seconds. The algorithm demonstrated swift convergence. The average cost across the population closely mirrored the best cost, albeit with a slight delay, eventually settling at 15.


### 3.2. Second Benchmark

In the second benchmark, the GA was tested with identical parameters to the first benchmark: population size (nPop) = 50, crossover probability (pc) = 0.2, and mutation probability (pm) = 0.8. While the algorithm may not produce the same result in every simulation, after several runs, it managed to approach a near-optimal solution, achieving 101 bins. Results are visualized below.

![3i2kd](https://live.staticflickr.com/65535/53360419414_5ec7249cc5.jpg)

![sad0](https://live.staticflickr.com/65535/53360310158_6129aa5ea6_b.jpg)


In this simulation, the GA algorithm required 5583 iterations over 237 seconds before meeting the stopping criteria. Interestingly, the average cost of the particles matched the global best cost. The algorithm exhibited swift convergence initially but encountered challenges in converging to a feasible solution.

Despite attempts to achieve 100 bins by tweaking parameters, the algorithm consistently faced difficulties. It often became stuck with just a few bin violations unresolved, persisting even after nearly 20000 iterations, resulting in non-feasible solutions slightly above a cost of 100. The randomness inherent in the GA can contribute to such outcomes, making luck a potential factor in its performance.

{{< hint tip >}}
[If you would like to advance to the implementation using Particle Swarm Optimization (PSO) click here!](https://ricardochin.com/docs/2code/5od/_index3/)

[Alternatively, I have included a comparison between GA and PSO in the main chapter, kindly click here! ✌️](https://ricardochin.com/docs/2code/5od/#5-ending-thoughts){{< /hint >}}

