---
title: "Particle Swarm Optimization"
weight: 3
---

# **Disco Dance of Particles**

![12](https://gbhat.com/assets/gifs/pso_nonconvex.gif)

## 1 Algorithm Concept

Particle Swarm Optimization (PSO) draws its inspiration from the social dynamics seen in various species, like birds and insects, as they navigate their environments to fulfill their needs. In this method, each potential solution is likened to a particle, and these particles come together to form a dynamic swarm. Each particle possesses two key characteristics: its position and its velocity. Utilizing this velocity, each particle embarks on a journey to a new position within the search space. Upon arriving at these new positions, individual particle bests and the overall swarm bests are updated accordingly. The velocity of each particle is then fine-tuned based on its experiences, repeating this cycle until certain conditions are met.

Similar to [Genetic Algorithm (GA)](https://ricardochin.com/docs/code/bin-packing/genetic-algorithm/), PSO commences with an initialization step wherein the initial swarm of particles is generated. The idea of representing solutions aligns closely with that of GA, where each particle begins with a random position and zero velocity.

Evaluation for fitness value follows, where every particle's fitness is computed and compared against its prior best fitness and the best fitness across the entire swarm. These comparisons lead to updates in the personal bests and global bests positions. Unless a stopping criterion intervenes, the velocity and position of each particle undergo updates. These adjustments are determined by incorporating both the personal bests (`pbest`) and global bests (`gbest`) positions, along with the previous velocity, to compute the updated velocity using a defined formula.

{{< katex display >}}
 v_{ij} = w v_{ij} + c_1 q \frac{(x_{ij}^{pb} - x_{ij})} {\Delta t} + c_2r \frac{( x_{ij}^{gb} - x_{ij})}{ \Delta t}    
{{< /katex >}}
{{< katex display >}}
 x_{ij} = x_{ij} + v_{ij} \Delta t    
{{< /katex >}}

{{< hint note >}}
The variables are:

- w: Inertia weight
- c1: Represents the experience of an individual particle (cognitive or self learning factor)
- c2: Represents the experience of the whole swarm (group or social learning factor)
- r and q: Random variables from 0 to 1
- vwij: This is the **diversification term** which is responsible for searching for new solutions in new regions while the rest of the expression is the **intensification term**, which explores the current region with the objective of finding a better solution
{{< /hint>}}

The velocity is also limited by upper and lower bounds (maximum and minimum velocities), since if the velocity is too high, the algorithm may become too unstable. In addition, a noteworthy aspect of the PSO algorithm is its divergence from the necessity of sorting fitness values of solutions throughout its processes. This particular characteristic could serve as a considerable computational advantage, particularly in scenarios where dealing with a large population size. Unlike GA, which often involves sorting operations, PSO relies on straightforward arithmetic operations involving real numbers for its velocity and position updates. This simplicity in operations contributes to its efficiency, making it an appealing choice, especially in computational-intensive situations.

## 2 Code Implementation

{{< hint important >}}

A similar approach for the code implementation was used for the [GA](https://ricardochin.com/docs/code/bin-packing/genetic-algorithm/)

{{< /hint >}}

The implementation presented through the function `PSO.m` initializes parameters, including the objective function `CostFunction` and defines variables related to the problem, such as the number of decision variables `nVar`, their boundaries `VarMin` and `VarMax`, and PSO-specific parameters like maximum iterations `MaxIt`, population size `nPop`, and velocity limits.


```matlab
nVar = 2*model.n-1;     % Number of Decision Variables
VarSize = [1 nVar];     % Decision Variables Matrix Size

VarMin = 0;        % Lower Bound of Decision Variables
VarMax = nVar;     % Upper Bound of Decision Variables


%% PSO Parameters

if model.n < 100
    MaxIt = 3000;            % Maximum Number of Iterations
else
    MaxIt = 30000;
end
```

The script initializes the particles (potential solutions) and their velocities randomly within defined bounds. It evaluates the cost of each particle based on the provided objective function `CostFunction`. This cost function is depicted in `PSO_BinPackingCost.m`, it assesses the cost of a bin packing solution based on input parameters `x` and `model`. 

```matlab
empty_particle.Position = [];
empty_particle.Cost = [];
empty_particle.Sol = [];
empty_particle.Velocity = [];
empty_particle.Best.Position = [];
%... other empty_particle fields

particle = repmat(empty_particle, nPop, 1);
GlobalBest.Cost = inf;

for i = 1:nPop
    particle(i).Position = unifrnd(VarMin, VarMax, VarSize);
    particle(i).Velocity = zeros(VarSize);
    %... evaluation and updating best positions
end
%... BestCost and AvgCost initialization
```


Afterwards, the main `PSO.m` function iterates through a predefined number of iterations `MaxIt`, wherein each iteration involves updating the velocities of particles based on personal and global best positions. It subsequently adjusts particle positions considering the updated velocities and ensures these positions adhere to predefined limits. Within each iteration, the loop evaluates the cost of each particle's position using the provided objective function, [applies mutation operations](https://github.com/roaked/genetic-algorithm-optimization/blob/main/bpp/PSO_Mutate.m) to potentially enhance solutions, and updates personal best positions accordingly. Additionally, the algorithm tracks the global best solution among all particles. Throughout iterations, it collects information about the best solution found `BestSol`, its associated cost `BestCost`, the average cost `AvgCost`, and displays the progress. 

```matlab
    for it = 1:MaxIt
        for i = 1:nPop
            % Update velocity
            particle(i).Velocity = w * particle(i).Velocity ...
                + c1 * rand(VarSize) .* (particle(i).Best.Position - particle(i).Position) ...
                + c2 * rand(VarSize) .* (GlobalBest.Position - particle(i).Position);
            %... velocity and position adjustments
            %... cost evaluation and mutation operations
            %... updating personal and global best
        end
        %... adjusting parameters and collecting data for BestSol, BestCost, and AvgCost
        %... breaking criteria based on the stuckCounter
    end
    %... end of the PSO loop, returning results
end
```

As shown above, the loop includes a stopping criterion based on a stuck counter to break out if the algorithm appears trapped in a solution or reaches the maximum number of iterations. Upon completion or reaching the stopping criteria, the loop returns the gathered results.

```matlab
if (it>=50)  
        if (BestCost(it,1) == BestCost(it-1,1)) && BestCost(it,1) == floor(BestCost(it,1))
            %Stuck in feasible solution
            stuckCounter=stuckCounter+1;
            if (stuckCounter == 100)
                break;        
            end
            
        elseif (BestCost(it,1) == BestCost(it-1,1))
            %Stuck in unfeasible solution
            stuckCounter=stuckCounter+1;
            if (stuckCounter == 5000)
                break;        
            end
        else 
            stuckCounter=1;   
        end
        
    end
```

## 3 Results

### 3.1. First Benchmark

For the initial benchmark, PSO underwent 10 simulations, each employing the following set of parameters:

![1](https://live.staticflickr.com/65535/53360419499_f1b24207df.jpg)

![2](https://live.staticflickr.com/65535/53360419489_89b9888fff_c.jpg)


It was evident that while PSO demonstrated notable advantages over GA, showcasing rapid convergence in specific scenarios, it also revealed certain limitations. These included susceptibility to getting trapped in local optima and a significant decline in convergence rate, as evidenced by all (10) simulations becoming non-feasible upon reaching the stopping criteria. Attempts to boost PSO's performance through parameter tuning yielded limited success.

In response to these challenges, an innovative approach was adopted by introducing a mutation mechanism. This involved mutations affecting both the particles' personal best and the global best. Integrating this mutation operator into the PSO algorithm offered significant benefits. Not only did it expedite convergence, but it also mitigated premature convergence issues, thereby enhancing accuracy and circumventing the aforementioned challenges.

Multiple experiments were conducted in an effort to fine-tune the diverse parameters. The initial focus was on understanding the influence of population size. Interestingly, it was noted that augmenting the population had only a restrained impact on the number of optimal solutions, particularly when the number of iterations remained unchanged. The experiments were executed using the following parameters: `w = 0.9`, `wdamp = 0.99`, `c1 = c2 = 2`, `VelMax = 0.0001`, `nParticleMutation = 3` and `nGlobalBestMutation = 10`.

| Population Size | Optimal Sols | Non-Optimal Sols | Non-feasible | Average Simulation Time (s) |
|-----------------|--------------|------------------|--------------|-----------------------------|
| 10              | 18           | 2                | 0            | 7.6248                      |
| 20              | 19           | 1                | 0            | 12.5746                     |
| 50              | 15           | 5                | 0            | 29.8416                     |


20 simulations were conducted for each scenario, solely manipulating the population size. The results indicate a correlation wherein augmenting the population size enhances the model's performance while concurrently elongating the simulation time. Optimal performance was observed when the population size was set at 20. Among the 20 simulations, the algorithm remarkably attained the global optimal solution in 19 instances, narrowly missing it in just one simulation. Notably, no non-feasible solutions were encountered, which stands as a significant advantage over the GA.


The subsequent step aimed to discern the impact of particle mutation and global best mutation on our model's performance. To ensure more conclusive outcomes, a total of 100 simulations were executed for each scenario:

| Particle Mutation | GB Mutation | Optimal Sols | Non-Optimal Sols | AVG Time  (s) | Best Time (s) |
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


| Particle Mutation | GB Mutation | Optimal Sols | Non-Optimal Sols | AVG Time  (s) | Best Time  (s) |
|-------------------|-------------|--------------|------------------|--------------|---------------|
| 3                 | 20          | 100          | 0                | 3.7797       | 1.5666        |
| 5                 | 20          | 100          | 0                | 4.1108       | 2.1950        |
| 3                 | 15          | 94           | 6                | 5.9879       | 1.7073        |
| 3                 | 10          | 86           | 14               | 6.7412       | 1.4942        |
| 3                 | 3           | 72           | 14               | 15.2054      | 2.1753        |


Increasing the count of global best mutations while reducing the number of particle mutations notably enhanced overall performance. When employing either 3 or 5 particle mutations alongside 20 global best mutations, all simulations resulted in achieving the optimal solution — an outstanding outcome for PSO. The preference leans towards 3 particle mutations due to its lower simulation times, presenting a favourable trade-off.

Moreover, these parameters wield a direct influence on average simulation time. Fewer mutation values expedite each iteration but risk resembling the original PSO, increasing the chances of entrapment and necessitating more iterations, ultimately leading to non-feasible solutions and elongated simulation times. Conversely, an excess of mutations doesn't significantly benefit performance. Based on the data, the optimal parameters thus far appear to be `nPop = 10`, `nParticleMutation = 3` and `nGlobalBestMutation = 20`.

Further evaluations explored the impact of maximum velocity on the algorithm. Tests were conducted at different velocities while maintaining other parameters constant. 

![213](https://live.staticflickr.com/65535/53360310213_90bfabcdbc_c.jpg)


The examination reveals that higher velocities introduce instability to the algorithm, manifesting as noise in the `Average Cost` plot. Notably, a `MaxVel` of 0.01 resulted in an `Average Cost` oscillating around 18, while reducing `MaxVel` to 0.001 led to oscillations closer to 16. This velocity variation also impacted the Best Cost of the particles; higher velocities correlated with higher `Best Cost`. This sensitivity of the algorithm to velocity proves crucial when tackling the bin packing problem.

In addition to velocity, the parameters related to inertia, `w` and `wdamp`, and learning factors , `c1` and `c2` underwent testing. To comprehend their impacts better, mutations were maintained at lower values. If set too low, the algorithm would require more iterations to find the optimal solution due to excessively low velocities, thereby affecting simulation time. Conversely, excessively high values would cause velocities to saturate quickly to the maximum allowed value, which is also suboptimal. However, unlike mutations, these parameters didn't exhibit the same influential impact. Ultimately, values were selected based on recommendations found in literature, settling on `c1 = c2 = 2`, `w = 0.9` and `wdamp = 0.99`.


Afterwards, the PSO algorithm underwent testing utilizing the following parameters: `nPop = 10`, `w = 0.9`, `wdamp = 0.99`, `c1 = c2 = 2`, `VelMax = 0.0001`, `nParticleMutation = 3` and `nGlobalBestMutation = 20`:

![123](https://live.staticflickr.com/65535/53359214432_4fd4ff5172.jpg)

Next, convergence for best cost (left) and average cost (right) over the iterations was depicted:

![1241f](https://live.staticflickr.com/65535/53360310203_29792b6c15_b.jpg)


In this simulation, the PSO algorithm completed 206 iterations within 2.52 seconds before reaching its stopping criteria. Remarkably, it displayed rapid convergence for this specific benchmark. The average cost of the particles notably decreased to 15.8 before the simulation concluded. Typically, this value fluctuates between 15 and 16 with a higher number of iterations.

### 3.2. Second Benchmark


In the second benchmark, the PSO underwent testing using identical parameters to those employed in the first benchmark: `nPop = 10`, `w = 0.9`, `wdamp = 0.99`, `c1 = c2 = 2`, `VelMax = 0.0001`, `nParticleMutation = 3` and `nGlobalBestMutation = 20`.

While the algorithm doesn't yield identical results in each simulation, it managed to attain the optimal solution in certain instances. Here is the outcome of one such simulation depicted:

![asfvcc2](https://live.staticflickr.com/65535/53359214352_f9a2bceea8.jpg)

![asdad](https://live.staticflickr.com/65535/53360419374_79b7e25865_c.jpg)


In this particular simulation, the PSO algorithm required 8895 iterations, spanning 444.18 seconds, before meeting the stopping criteria. The algorithm showcased rapid convergence initially, achieving a cost of less than 101 within 1000 iterations. However, it encountered challenges reaching the optimal solution, nearly getting stuck at certain points, evident in the best cost plot. Throughout the process, the average cost of the particles fluctuated between 105 and 107.

## 4 Remarks GA vs. PSO

{{< hint tip >}}
[Implementation using Genetic Algorithm (GA)](https://ricardochin.com/docs/code/bin-packing/genetic-algorithm/){{< /hint >}}

To further test and compare the GA and PSO algorithms, they were ran 10 times each with the second benchmark. 

![sadsad](https://live.staticflickr.com/65535/53359214342_fb24701070_c.jpg)

![das](https://live.staticflickr.com/65535/53360549430_e11ea23c48_c.jpg)


In one set of trials, the algorithm achieved 102 bins once and 4 times in total. However, it successfully obtained feasible solutions in 7 out of 10 attempts. The average time across these 10 simulations stood at 284.40 seconds (averaging 6402 iterations). The most efficient solution, involving 101 bins, was attained in 237.06 seconds (5583 iterations).

Conversely, the PSO algorithm hit the optimal solution only 3 times in 10 simulations, which is less than ideal. Nonetheless, it managed to secure feasible solutions in 9 out of these 10 instances. Notably, the algorithm failed to obtain a feasible solution only when attempting to accommodate all items within 99 bins—an impossible feat. The average time for these 10 simulations totaled 149.37 seconds (averaging 3160 iterations). The best time among the 3 optimal solutions was 107.84 seconds (2303 iterations). Interestingly, three out of the first five simulations resulted in optimal solutions.

Comparatively, both algorithms encountered a higher percentage of non-feasible solutions in the second benchmark compared to the first. This discrepancy aligns with expectations, given the increased complexity of the second benchmark.


## 5 Ending Thoughts

[Brief summary about the algorithms in the main chapter.](https://ricardochin.com/docs/code/bin-packing/#5-ending-thoughts)

