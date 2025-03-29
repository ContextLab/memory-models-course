# Assignment 1: Hopfield Networks

## Overview
In this assignment, you will explore computational memory models by implementing a Hopfield network. In the original article ([Hopfield (1982)](https://www.dropbox.com/scl/fi/iw9wtr3xjvrbqtk38obid/Hopf82.pdf?rlkey=x3my329oj9952er68sr28c7xc&dl=1)), neuronal activations were set to either 0 ("not firing") or 1 ("firing").  Modern Hopfield networks nearly always follow an updated implementation, first proposed by [Amit et al. (1985)](https://www.dropbox.com/scl/fi/3a3adwqf70afb9kmieezn/AmitEtal85.pdf?rlkey=78fckvuuvk9t3o9fbpjrmn6de&dl=1).  In Amit et al.'s framing, neurons take on activation values of either -1 ("down state") or +1 ("up state").  This has three important benefits over Hopfield's original implementation:
  - It provides a cleaner way to implement the Hebbian learning rule (i.e., without subtracting means or shifting values).
  - It avoids a bias towards 0 (i.e., +1 and -1 are equally "attractive" whereas 0-valued neurons have a stronger "pull").
  - The energy function (i.e., a description of the attractor dynamics of the network) can be directly mapped onto the [Ising model](https://en.wikipedia.org/wiki/Ising_model) from statistical physics.

You should start by reading [Amit et al. (1985)](https://www.dropbox.com/scl/fi/3a3adwqf70afb9kmieezn/AmitEtal85.pdf?rlkey=78fckvuuvk9t3o9fbpjrmn6de&dl=1) closely.  Then you should code up the model in a Google Colaboratory notebook.  Unless otherwise noted, all references to "the paper" refer to Amit et al. (1985).

## Tasks

### 1. Implement Memory Storage and Retrieval
- **Objective:** Write functions that implement the core operations of a Hopfield network:
  - **Memory Storage:** Implement the Hebbian learning rule to compute the weight matrix, given a set of network configurations (memories).  This is described in **Equation 1.5** of the paper:

  Let \($p$\) be the number of patterns, \($N$\) the number of neurons, and \($\xi_i^\mu \in \{-1, +1\}$\) the value of neuron \($i$\) in pattern \($\mu$\).

  The synaptic coupling between neuron \($i$\) and \($j$\) is:
  
  $$
  J_{ij} = \frac{1}{N} \sum_{\mu=1}^p \xi_i^\mu \xi_j^\mu.
  $$

  Note that the matrix is symmetric \($J_{ij} = J_{ji}$\), and there are no self-connections allowed (by definition $J_{ii} = 0$).

  - **Memory Retrieval:** Implement the retrieval rule using **Equation (1.3)** and surrounding discussion.

    At each time step, each neuron updates according to its **local field** \($h_i$\):

    $$
    h_i = \sum_{j=1}^N J_{ij} S_j
    $$

    The neuron updates its state to align with the sign of the field:

    $$
    S_i(t+1) = \text{sign}(h_i(t)) = \text{sign} \left( \sum_{j} J_{ij} S_j(t) \right)
    $$

    Note that \($S_i \in \{-1, +1\}$\) represents the state of neuron \($i$\).

### 2. Test with a Small Network

Encode the following test memories in a small Hopfield network with \( N = 5 \) neurons:
  
  $$
  \xi^1 = [+1, -1, +1, -1, 1]
  $$
  $$
  \xi^2 = [-1, +1, -1, +1, -1]
  $$
  
  - Store these memories using the Hebbian rule.
  - Test memory retrieval by presenting the network with noisy versions of the stored patterns (e.g., flipping one neuron's sign, setting one or more activation values to 0).
  - Briefly discuss the results and provide insights into how the network behaves.  You might find Figure 3 from [Hopfield (1984)](https://www.dropbox.com/scl/fi/7wktieqztt60b8wyhg2au/Hopf84.pdf?rlkey=yi3baegby8x6olxznsvm8lyxz&dl=1) useful!  You can either write a paragraph or two, or sketch (or code up) a diagram or figure, or some combination.  In particular:
    - Can you get a sense of how the network "works"-- i.e., why it stores memories?
    - Why do some memories interfere and others don't?  Can you build up enough of an intuition that you can manually construct memories that can vs. can't be retrieved by a toy (small) network?
    - Can you build up any intuitions about what sorts of factors might affect the "capacity" of the network?  (Capacity is the maximum number of memories that can be "successfully" retrieved).

### 3. Evaluate Storage Capacity
- **Objective:** Determine how the ability to recover memories degrades as you vary:
  - **Network Size:** The total number of neurons in the network.
  - **Number of Stored Memories:** The number of patterns stored in the network.
  
  To generate $m$ memories \($\xi_1, ... \xi_m$\) for a network of $N$ neurons, you can use the following Python code; each row of the resulting matrix `xi` contains a single memory:
  ```python

  import numpy as np
  xi = 2 * (np.random.rand(m, N) > 0.5) - 1
  ```

- **Method:**
  - For each configuration, run multiple trials to compute the proportion of times that at least 99% of a memory is accurately recovered.
  - **Visualization 1:** Create a heatmap where the $x$-axis represents the network size, the $y$-axis represents the number of stored memories, and the color indicates the recovery accuracy.  Play around with this to decide on a range of network sizes and numbers of memories that adequately illustrates the system's behavior.
  - **Visualization 2:** For each network size ($x$-axis), plot the expected number of memories that can be retrieved accurately ($y$-axis).  Let:

    - \($N$\): the number of neurons (network size)
    - \($m$\): the number of stored memories
    - \($P\left[m, N\right] \in \left[0, 1\right]$\): the empirically observed success rate (from your heatmap); i.e., the **proportion** of memories correctly retrieved with at least 99% accuracy, for a network of size \($N$\) and \($m$\) stored memories.

    Then the **expected number of successfully retrieved memories**, \($\mathbb{E}\left[R_N\right]$\), for each network size \($N$\) is given by:

    $$
    \mathbb{E}\left[R_N\right] = \sum_{m=1}^{M} m \cdot P[m, N],
    $$
    where \($M$\) is the maximum number of stored memories you tested.

  - Is there any systematic relationship (between network size and capacity) that emerges?  Can you describe any intuitions and/or develop any "rules" that might enable you to estimate a network's capacity solely from its size?

### 4. Simulate Cued Recall
**Objective:** Evaluate how well the network performs associative recall when presented with only a partial input (a cue), and must recover the corresponding response.

#### Setup: A–B Pair Structure

- Each stored memory is a concatenated pair of binary patterns:
  - The first half of the neurons represents the **cue** \($A$\)
  - The second half represents the **response** \($B$\)

- If the total number of neurons \($N$\) is odd:
  - Let the cue occupy the first $\lfloor N/2 \rfloor$ neurons
  - Let the response occupy the remaining $\lceil N/2 \rceil$ neurons

Each full pattern \($\xi^\mu \in \{-1, +1\}^N$\) is defined as:
$$
\xi^\mu = \begin{bmatrix} A^\mu \\ B^\mu \end{bmatrix}
$$

#### Simulation Procedure

For each trial:

  - **Choose a stored memory** \( \xi^\mu \)

  - **Construct the initial network state \( x \)**:
    - Set the cue half to match the stored pattern:  
      $$
      x_i = A^\mu_i \quad \text{for } i = 1, \dots, \lfloor N/2 \rfloor
      $$
    - Set the response half to zero (i.e., no initial information):  
      $$
      x_i = 0 \quad \text{for } i = \lfloor N/2 \rfloor + 1, \dots, N
      $$

  - **Evolve the network** until it reaches a stable state using the usual update rule:
    $$
    x_i \leftarrow \text{sign} \left( \sum_j J_{ij} x_j \right)
    $$

    You may choose whether to:
    - Let the cue neurons update along with the rest of the network
    - Or **clamp** the cue (i.e., keep \($x_i = A^\mu_i$\) fixed for the cue indices)

  - **Evaluate accuracy**:
    - Extract the response portion from the final state \($x^*$\)
    - Compare it to the original response \($B^\mu$\)
    - Mark as a **successful recall** if at least 99% of the bits match:
      $$
      \frac{1}{|B|} \sum_{i \in \text{response}} \mathbb{1}[x^*_i = B^\mu_i] \geq 0.99
      $$

#### Analysis

- Repeat the simulation for multiple stored $A$–$B$ pairs
- For each network size \($N$\), compute the **expected number of correctly recalled responses**
- Plot this value as a function of \($N$\)


#### Optional Extensions

- Compare performance with and without clamping the cue neurons
- Test whether cueing with partial or noisy \($A$\) patterns still leads to correct retrieval of \($B$\)

### 5. Simulate Contextual Drift

**Objective:** Explore how gradual changes in context influence which memories are retrieved. This models how temporal or environmental drift might bias recall toward memories with similar contexts.

#### Setup: Item–Context Memory Representation

- Use a Hopfield network with **100 neurons**.
- Each memory is a combination of:
  - **Item features**: 50 neurons (first half)
  - **Context features**: 50 neurons (second half)

- Create a **sequence of 10 memories** \($\{\xi^1, \xi^2, \dots, \xi^{10}\}$\), each composed of:
  $$
  \xi^t = \begin{bmatrix} \text{item}^t \\ \text{context}^t \end{bmatrix}
  $$

- Initialize the **context vector** for the first memory randomly (set it to a random vector of +1s and -1s).
- For each subsequent memory \($t + 1$\), create a new context vector by:
  - **Copying** the previous context
  - **Perturbing** a small number of bits (e.g., 5% of context features flipped)

This creates a **drifting context** across the sequence.

#### Simulation Procedure

1. **Train the network** on all 10 memory patterns using Hebbian learning (encode them into a weight matrix, $J$)

2. For each memory index \($i = 1, \dots, 10$\):

   a. **Cue the network** with the **context vector** from \($\xi^i$\):
   - Set the **context neurons** (second half) to match \($\text{context}^i$\)
   - Set the **item neurons** (first half) to zero (i.e., no initial item input)

   b. **Run the network dynamics** until convergence

   c. **Compare the final state** to all 10 stored patterns:
   - For each stored pattern \($\xi^j$\), extract the item portion and compare it to the final item state
   - If the item portion of \($\xi^j$\) matches the recovered state with ≥99% accuracy, consider memory \( j \) to have been retrieved

   d. Record the **retrieved index** (if any), and compute the **relative position**:
   $$
   \Delta = j - i
   $$
   (e.g., \($\Delta = 0$\) means the correct memory was retrieved; \($\Delta = 1$\) means the next one in the sequence was recalled, etc.)

#### Analysis

- Repeat the simulation multiple times (e.g., 100 runs) to account for randomness
- For each relative position \( \Delta \in [-9, +9] \), compute:
  - The **probability** that a memory at offset \($\Delta$\) was retrieved when cueing from index \($i$\)
  - A **95% confidence interval** for each probability estimate

#### Visualization

Create a line plot where:

- $x$-axis: Relative position in the sequence \($\Delta$\)
- $y$-axis: Probability of retrieval
- Error bars: 95% confidence intervals

Write up a brief description of what you think is happening (and why).

#### Optional extensions

- Context drift simulates how memory might change over time or under shifting external conditions
- You may adjust the context perturbation rate to see how sharply it affects retrieval
- This model can be adapted to explore recency effects, intrusion errors, or generalization


## Submission Instructions
- [Submit](https://canvas.dartmouth.edu/courses/71051/assignments/517353) a single stand-alone Google Colaboratory notebook (or similar) that includes:
  - Your full model implementation.
  - Markdown (text) cells explaining your approach, methodology, any design decisions you want to draw attention to, and discussion points.
  - Plots and results for each simulation task.
- Ensure that your notebook runs without errors in Google Colaboratory.