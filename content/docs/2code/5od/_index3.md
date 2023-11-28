---
title: "Particle Swarm Optimization"
weight: 3
---

# **Disco Dance of Particles**

![12](https://gbhat.com/assets/gifs/pso_nonconvex.gif)

## 1 Algorithm Concept

Particle Swarm Optimization (PSO) draws its inspiration from the social dynamics seen in various species, like birds and insects, as they navigate their environments to fulfill their needs. In this method, each potential solution is likened to a particle, and these particles come together to form a dynamic swarm. Each particle possesses two key characteristics: its position and its velocity. Utilizing this velocity, each particle embarks on a journey to a new position within the search space. Upon arriving at these new positions, individual particle bests and the overall swarm bests are updated accordingly. The velocity of each particle is then fine-tuned based on its experiences, repeating this cycle until certain conditions are met.

Similar to [genetic algorithm (GA)](https://ricardochin.com/docs/2code/5od/_index2/), PSO commences with an initialization step wherein the initial swarm of particles is generated. The idea of representing solutions aligns closely with that of GA, where each particle begins with a random position and zero velocity. Evaluation for fitness value follows suit, where every particle's fitness is computed and compared against its prior best fitness and the best fitness across the entire swarm. These comparisons lead to updates in the personal bests and global bests positions. Unless a stopping criterion intervenes, the velocity and position of each particle undergo updates. These adjustments are determined by incorporating both the personal bests (pbest) and global bests (gbest) positions, along with the previous velocity, to compute the updated velocity using a defined formula.

{{< katex display >}}
 v_{ij} = w v_{ij} + c_1 q \frac{(x_{ij}^{pb} - x_{ij})} {\Delta t} + c_2r \frac{( x_{ij}^{gb} - x_{ij})}{ \Delta t}    
{{< /katex >}}
{{< katex display >}}
 x_{ij} = x_{ij} + v_{ij} \Delta t    
{{< /katex >}}

{{< hint tip >}}
The variables are:

- w: Inertia weight
- c1: Represents the experience of an individual particle (cognitive or self learning factor)
- c2: Represents the experience of the whole swarm (group or social learning factor)
- r and q: Random variables from 0 to 1
- wij: Diversification term -- responsible for searching for new solutions in new regions while the rest of the expression is the intensification term, which explores the current region with the objective of finding a better solution
{{< /hint>}}

The velocity is also limited by upper and lower bounds (maximum and minimum velocities), since if the velocity is too high, the algorithm may become too unstable. In addition, a noteworthy aspect of the PSO algorithm is its divergence from the necessity of sorting fitness values of solutions throughout its processes. This particular characteristic could serve as a considerable computational advantage, particularly in scenarios where dealing with a large population size. Unlike GA, which often involves sorting operations, PSO relies on straightforward arithmetic operations involving real numbers for its velocity and position updates. This simplicity in operations contributes to its efficiency, making it an appealing choice, especially in computational-intensive situations.

## 2 Code Implementation

{{< hint example >}}

A similar approach for the code implementation was used for the [Genetic Algorithm. If you want to read more about it as an example, click here.](https://ricardochin.com/docs/2code/5od/_index2/). Hence, I will ommit duplicate writing.

{{< /hint >}}

## 3 Results

For the initial benchmark, PSO underwent 10 simulations, each employing the following set of parameters:

![1](https://live.staticflickr.com/65535/53360419499_f1b24207df.jpg)

![2](https://live.staticflickr.com/65535/53360419489_89b9888fff_c.jpg)


It was evident that while PSO demonstrated notable advantages over GA, showcasing rapid convergence in specific scenarios, it also revealed certain limitations. These included susceptibility to getting trapped in local optima and a significant decline in convergence rate, as evidenced by all (10) simulations becoming non-feasible upon reaching the stopping criteria. Attempts to bolster PSO's performance through parameter tuning yielded limited success.

In response to these challenges, an innovative approach was adopted by introducing a mutation mechanism. This involved mutations affecting both the particles' personal best and the global best. Integrating this mutation operator into the PSO algorithm offered significant benefits. Not only did it expedite convergence, but it also mitigated premature convergence issues, thereby enhancing accuracy and circumventing the aforementioned challenges.

Multiple experiments were conducted in an effort to fine-tune the diverse parameters. The initial focus was on understanding the influence of population size. Interestingly, it was noted that augmenting the population had only a restrained impact on the number of optimal solutions, particularly when the number of iterations remained unchanged. The experiments were executed using the following parameters: w = 0.9, wdamp = 0.99, c1 = c2 = 2, VelMax = 0.0001, nParticleMutation = 3, and nGlobalBestMutation = 10.

| Population Size | Optimal Sols | Non-Optimal Sols | Non-feasible | Average Simulation Time [\s] |
|-----------------|--------------|------------------|--------------|-----------------------------|
| 10              | 18           | 2                | 0            | 7.6248                      |
| 20              | 19           | 1                | 0            | 12.5746                     |
| 50              | 15           | 5                | 0            | 29.8416                     |


20 simulations were conducted for each scenario, solely manipulating the population size. The results indicate a correlation wherein augmenting the population size enhances the model's performance while concurrently elongating the simulation time. Optimal performance was observed when the population size was set at 20. Among the 20 simulations, the algorithm remarkably attained the global optimal solution in 19 instances, narrowly missing it in just one simulation. Notably, no non-feasible solutions were encountered, which stands as a significant advantage over the GA.


The subsequent step aimed to discern the impact of particle mutation and global best mutation on our model's performance. To ensure more conclusive outcomes, a total of 100 simulations were executed for each scenario:

| Particle Mutation | GB Mutation | Optimal Sols | Non-Optimal Sols | AVG Time [\s] | Best Time [\s] |
|-------------------|-------------|--------------|------------------|--------------|---------------|
| 3                 | 20          | 100          | 0                | 5.7910       | 2.4609        |
| 5                 | 20          | 100          | 0                | 7.7046       | 3.7946        |
| 3                 | 10          | 92           | 8                | 12.5384      | 2.8647        |
| 1                 | 20          | 92           | 8                | 4.0097       | 1.6442        |
| 1                 | 10          | 88           | 12               | 6.8914       | 1.5468        |
| 1                 | 5           | 87           | 12               | 10.7145      | 1.8855        |
| 10                | 3           | 82           | 18               | 85.6950      | 15.5693       |
| 1                 | 3           | 82           | 18               | 14.3089      | 2.1009        |


The initial tests were conducted with a population size of 20, revealing notably extended average simulation times in certain experiments. Consequently, to expedite the process, the population size was adjusted to 10:


| Particle Mutation | GB Mutation | Optimal Sols | Non-Optimal Sols | AVG Time [\s] | Best Time [\s] |
|-------------------|-------------|--------------|------------------|--------------|---------------|
| 3                 | 20          | 100          | 0                | 3.7797       | 1.5666        |
| 5                 | 20          | 100          | 0                | 4.1108       | 2.1950        |
| 3                 | 15          | 94           | 6                | 5.9879       | 1.7073        |
| 3                 | 10          | 86           | 14               | 6.7412       | 1.4942        |
| 3                 | 3           | 72           | 14               | 15.2054      | 2.1753        |


Increasing the count of global best mutations while reducing the number of particle mutations notably enhanced overall performance. When employing either 3 or 5 particle mutations alongside 20 global best mutations, all simulations resulted in achieving the optimal solution — an outstanding outcome for PSO. The preference leans towards 3 particle mutations due to its lower simulation times, presenting a favourable trade-off.

Moreover, these parameters wield a direct influence on average simulation time. Fewer mutation values expedite each iteration but risk resembling the original PSO, increasing the chances of entrapment and necessitating more iterations, ultimately leading to non-feasible solutions and elongated simulation times. Conversely, an excess of mutations doesn't significantly benefit performance. Based on the data, the optimal parameters thus far appear to be nPop = 10, nParticleMutation = 3, and nGlobalBestMutation = 20.

Further evaluations explored the impact of maximum velocity on the algorithm. Tests were conducted at different velocities while maintaining other parameters constant. 






