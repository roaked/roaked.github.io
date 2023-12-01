---
title: "EC Optimization: Space & Time"
weight: 5
---

# **EC: Where Algorithms Find Their Inner Darwin**

![124](https://i.pinimg.com/originals/88/66/b2/8866b25a23afb5185612095ffc57eca3.gif)

## 1 Abstract

In the realm of time management, optimizing tasks and resources mirrors the challenge of efficiently packing items into bins. This project delves into the application of evolutionary computation techniques, specifically Genetic Algorithm (GA) and Particle Swarm Optimization (PSO), in enhancing time management strategies. By comparing and contrasting these widely used methods. Additionally, this study illuminates their applicability and effectiveness in optimizing time allocation, task prioritization, and resource management.

## 2 Starting Point

Time management intricately resembles the Bin Packing problem, demanding efficient allocation of limited resources within predefined constraints. Evolutionary computation techniques, such as GA and PSO, offer promising avenues to optimize time allocation by drawing inspiration from nature's optimization processes. This work aims to explore the application of these evolutionary algorithms in enhancing time management strategies.

## 3 Reading Guide

This work dives into the complexities of the Bin Packing problem, focusing primarily on its one-dimensional variant (BPP-1). The problem involves allocating a set of objects, each characterized by its weight (size), into bins with fixed maximum capacities, aiming to minimize the number of bins used without violating their capacity constraints. Despite its seemingly straightforward premise, this problem falls under the classification of NP-hard problems, posing computational challenges due to its complexity.

{{< hint note >}}
In this exploration, intricacies of BPP-1 are examined and its practical applications in time management and logistics is compared. The study also introduces the BPP-2 variant, which involves minimizing bin sizes given a fixed number of bins, and extends the discussion to multi-dimensional bin packing problems.
{{< /hint >}}

Theoretical frameworks and computational models are utilized to elucidate the inherent complexities of these variants, emphasizing the limitations of polynomial-time solutions and highlighting the NP-hard nature of these optimization challenges. An illustrative example of the one-dimensional bin packing problem is provided to contextualize the fundamental principles and difficulties associated with efficiently allocating objects into bins while adhering to specified capacity constraints.

![1](https://live.staticflickr.com/65535/53360419559_8b76deff5b.jpg)

To address this challenge, as stated, two distinct meta-heuristic methods will undergo testing. The aim is to begin with a relatively simpler problem, featuring approximately 10 to 15 bins in the optimal solution, and then progress toward a larger-scale problem, requiring a minimum of 100 bins for an optimal solution. Alongside these meta-heuristics, straightforward yet efficient heuristics can also be employed. One such heuristic, Best Fit Decreasing (BFD), will serve as a comparative benchmark against the other two methods.

Another analogous yet distinct problem in this domain is the [cutting stock problem](https://en.wikipedia.org/wiki/Cutting_stock_problem#:~:text=In%20operations%20research%2C%20the%20cuttingarises%20from%20applications%20in%20industry.) (CSP). This problem involves m types of items, each characterized by its weight ({{< katex >}}w_j{{< /katex >}}) and demand ({{< katex >}}d_j{{< /katex >}}), in addition to an unlimited supply of bins, each with a capacity denoted as c. The primary objective here is to minimize the utilization of bins while meeting the demand for each item. Notably, these problems demonstrate an interrelation, allowing for easy conversion between the cutting stock problem and its equivalent bin packing problem. This connection arises from the ability to transform multiple items with identical weights in the bin packing problem, highlighting the versatility and overlapping nature of these optimization challenges.

## 4 Puzzle Formulation

As previously highlighted, the primary objective of this problem is to minimize the total number, denoted as 'm', of bins utilized, while ensuring that the capacity constraint of each bin, denoted as 'c', is not surpassed. With 'n' items in consideration, in the most extreme scenario, the requirement might necessitate 'n' bins (under the assumption that the weight of each item, represented by '{{< katex >}}w_j{{< /katex >}}', remains less than 'c'). A comprehensive and generalized formulation of this problem can be expressed as follows:

{{< katex display >}}
    \text{minimize } \hspace{0.2cm} z = \sum_{i=1}^{n} y_i
{{< /katex >}}

`Subject to:`

{{< katex display >}}
   \sum_{j=1}^{n} w_j x_{ij} \leq c y_i  \hspace{1cm} i \in N = {1,...,n}
{{< /katex >}}
{{< katex display >}}
   \sum_{i=1}^{n} x_{ij} = 1  \hspace{1cm} j \in N
{{< /katex >}}

`Where`

{{< katex display >}}
   y_{i} = 
   \begin{cases}
        1, & \text{if bin i is used} \\
        0, & \text{otherwise}
   \end{cases}
{{< /katex >}}
{{< katex display >}}
   x_{ij} = 
   \begin{cases}
        1, & \text{if item $j$ is assigned to bin i}\\
        0, & \text{otherwise}
   \end{cases}
{{< /katex >}}

{{< katex display >}}
   w_{j} \leq c \hspace{1cm} j \in N
{{< /katex >}}


The initial constraint specifies that for any utilized bin 'i', the collective sum of weights '{{< katex >}}w_j{{< /katex >}}' of the assigned items must not exceed the bin's capacity 'c'. The subsequent constraint ensures that all items are allocated to a singular bin, leaving none unassigned.

A lower limit for the count of bins can be established as the summation, from 1 to 'n', of the weights '{{< katex >}}w_j{{< /katex >}}' divided by the bin capacity 'c' (expressed as {{< katex >}}\sum_{j=1}^n  \frac{w_j}{c}{{< /katex >}}). 

Consequently, this signifies that the minimum requisite number of bins corresponds to the resultant value derived from the sum of weights divided by the bin capacity. As the number of bins must be an integer, this derived value is rounded up to the nearest whole number.

### 4.1. Genetic Algorithm in Time Management

Genetic Algorithm, inspired by the principles of natural selection and genetic inheritance, applies a population-based approach to search for optimal solutions. In the context of time management, GA can be likened to a process of 'evolving' schedules by iteratively selecting, combining, and mutating tasks within time constraints. The analogy to genetic traits in time management involves tasks mutating or evolving to fit within limited time slots, optimizing overall productivity.

{{< hint tip >}}
[If you would like to advance to the section explaining the implementation and results of a Genetic Algorithm click here ✌️](https://ricardochin.com/docs/2code/5od/_index2/){{< /hint >}}

### 4.2. Particle Swarm Optimization in Time Management:

In contrast to Genetic Algorithms, Particle Swarm Optimization mimics the social behaviour of particles to iteratively converge towards optimal solutions. In time management scenarios, PSO represents tasks as particles moving through a solution space, adjusting their positions (task schedules) based on individual experiences and collective knowledge. This method emphasizes cooperation and information sharing among tasks to reach efficient scheduling solutions within time constraints.

{{< hint tip >}}
[If you would like to advance to the section explaining the implementation and results of a Particle Swarm Optimization click here ✌️](https://ricardochin.com/docs/2code/5od/_index3/){{< /hint >}}


### 4.3. Comparative Analysis GA vs. PSO

Comparing GA and PSO for time management reveals distinct approaches to optimizing schedules. GA's population-based evolution allows for diverse exploration of scheduling possibilities, potentially finding globally optimal solutions. On the other hand, PSO's focus on particle interactions promotes local search capabilities, emphasizing quick convergence to feasible schedules. The trade-off between exploration and exploitation in these methods affects their efficacy in different time management contexts.

## 5 Ending Thoughts

After exhaustive testing across both benchmarks, several key conclusions emerge. Thought ommited from the writting literature, Best Fit Decreasing stands out for its simplicity, swiftly achieving optimal solutions and being highly suitable for bin packing problems. It operates without randomness, ensuring consistent outcomes, and is relatively easy to implement. However, its limitations surface in real-life scenarios where pre-sorting items by size may not be feasible from the outset.

When sorting by size isn't possible, Best Fit takes precedence. While still a straightforward heuristic, it yields notably inferior results compared to Best Fit Decreasing. Despite its speed in generating solutions, these heuristics serve best when urgency is paramount.

Contrastingly, meta-heuristics like GA and PSO offer adaptability and flexibility, ideally suited for intricate and extensive problems. Nevertheless, they demand parameter tuning—a non-trivial process, as optimal settings vary across different problem sets. Both GA and PSO involve intrinsic randomness.

GA operates relatively well with higher mutation probabilities but can occasionally get ensnared in non-feasible solutions, irrespective of problem size. Its randomness prevents absolute reliability, yet it still tends to converge towards optimal or near-optimal solutions, especially for smaller problems.

PSO encounters challenges without mutations but significantly improves with their inclusion, showcasing impressive performance and reliability. Notably, it steers clear of non-feasible solutions better than GA. However, PSO's sensitivity to velocities poses a risk of entrapment when velocities are high.

One of PSO's notable advantages over GA lies in its ability to perform multiple mutations on the global best in each iteration. This feature aids PSO in evading local minima, a hurdle that GA struggles to overcome.

In summary, the algorithm ranking based on performance would be: Best Fit Decreasing, followed by PSO, GA, and lastly, Best Fit. The fact that PSO and GA outperformed Best Fit was anticipated, given that the algorithms were well-optimized.

Enhancing these algorithms further presents challenges. While the 1D bin packing problem seems straightforward, situations where only one bin exceeds capacity can trap the algorithms. Incorporating a "best fit" mechanism within meta-heuristics seemed plausible initially, but the unreliability of the Best Fit results cast doubt on this approach.

## 6 Alternative Applications

Evolutionary computation techniques like GA and PSO find applications beyond time management, extending into diverse problem domains such as game design. Notably, games like Tetris harness these methods for optimization and decision-making.

{{< hint example>}}
Tetris, a classic puzzle game, involves arranging falling blocks of various shapes to fill complete rows without gaps. The challenge resembles a packing problem where the objective is to efficiently arrange blocks within a limited space. Evolutionary computation techniques like GA and PSO have been adapted to optimize strategies in Tetris gameplay. 🧩
{{< /hint >}}

By representing game moves as genetic sequences and applying selection, crossover, and mutation operations, GA iteratively refines strategies that aim to clear rows efficiently, maximizing points while minimizing gaps.

Similarly, PSO simulates particle movements (representing game strategies) to find configurations that lead to successful block placements and high-scoring patterns. The approach emphasizes the collaborative movement of particles (strategies) towards favorable game states.

These applications in gaming demonstrate the versatility of evolutionary computation techniques beyond time management. The adaptability of GA and PSO in optimizing strategies and decision-making extends to various problem-solving domains, showcasing their effectiveness in addressing complex optimization challenges beyond traditional scheduling or packing problems.



