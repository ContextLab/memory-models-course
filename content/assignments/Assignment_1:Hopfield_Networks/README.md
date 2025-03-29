# Assignment 1: Hopfield Networks

## Overview

In this assignment, you will explore computational memory models by implementing a Hopfield network.  [Here](https://github.com/ContextLab/memory-models-course/blob/main/content/assignments/Assignment_1%3AHopfield_Networks/hopfield_assignment_template.ipynb) is a suggested template to help get you started.

In the original article ([Hopfield, 1982](https://www.dropbox.com/scl/fi/iw9wtr3xjvrbqtk38obid/Hopf82.pdf?rlkey=x3my329oj9952er68sr28c7xc&dl=1)), neuronal activations were set to either 0 ("not firing") or 1 ("firing"). Modern Hopfield networks nearly always follow an updated implementation, first proposed by [Amit et al. (1985)](https://www.dropbox.com/scl/fi/3a3adwqf70afb9kmieezn/AmitEtal85.pdf?rlkey=78fckvuuvk9t3o9fbpjrmn6de&dl=1). In their framing, neurons take on activation values of either –1 ("down state") or +1 ("up state"). This has three important benefits:

- It provides a cleaner way to implement the Hebbian learning rule (i.e., without subtracting means or shifting values).
- It avoids a bias toward 0 (i.e., +1 and –1 are equally "attractive," whereas 0-valued neurons have a stronger "pull").
- The energy function (which describes the attractor dynamics of the network) can be directly mapped onto the [Ising model](https://en.wikipedia.org/wiki/Ising_model) from statistical physics.

You should start by reading [Amit et al. (1985)](https://www.dropbox.com/scl/fi/3a3adwqf70afb9kmieezn/AmitEtal85.pdf?rlkey=78fckvuuvk9t3o9fbpjrmn6de&dl=1) closely. Then implement the model in a Google Colaboratory notebook. Unless otherwise noted, all references to "the paper" refer to Amit et al. (1985).

---

## Tasks

### 1. Implement Memory Storage and Retrieval

#### Objective

Write functions that implement the core operations of a Hopfield network.

#### Memory Storage

Implement the Hebbian learning rule to compute the weight matrix, given a set of network configurations (memories). This is described in *Equation 1.5* of the paper:

Let $p$ be the number of patterns and $\xi_i^\mu \in \{-1, +1\}$ the value of neuron $i$ in pattern $\mu$. The synaptic coupling between neurons $i$ and $j$ is:

$$
J_{ij} = \sum_{\mu=1}^p \xi_i^\mu \xi_j^\mu
$$

Note that the matrix is symmetric ($J_{ij} = J_{ji}$), and there are no self-connections by definition ($J_{ii} = 0$).

#### Memory Retrieval

Implement the retrieval rule using *Equation 1.3* and surrounding discussion. At each time step, each neuron updates according to its local field:

$$
h_i = \sum_{j=1}^N J_{ij} S_j
$$

Each neuron updates its state by aligning with the sign of the field:

$$
S_i(t+1) = \text{sign}(h_i(t)) = \text{sign} \left( \sum_{j} J_{ij} S_j(t) \right)
$$

Here, $S_i \in \{-1, +1\}$ is the current state of neuron $i$.  To "retrieve" a memory:
  - Start by setting all of the neural activations to the **cue**.
  - Loop through all neurons (in a random order), updating one at a time according to the above equation, given the weight matrix ($J$) and the current activities of each of the other neurons.
  - Continue looping until either (a) you have "updated" every neuron in the latest loop, but no activities have changed, or (b) a maximum number of iterations is reached.
  - Return the current state of the network as the retrieved memory.

---

### 2. Test with a Small Network

Encode the following test memories in a Hopfield network with $N = 5$ neurons:

$$
\xi^1 = [+1, -1, +1, -1, +1] \\
\xi^2 = [-1, +1, -1, +1, -1]
$$

- Store these memories using the Hebbian rule.
- Test retrieval by presenting the network with noisy versions (e.g., flipping a sign, or setting some entries to 0).
- Briefly discuss your observations.

Questions to consider:

- Can you tell how and why the network stores memories?
- Why do some memories interfere while others don’t?
- Can you construct memory sets that do or don’t work in a small network?
- What factors do you think affect the **capacity** of the network?

---

### 3. Evaluate Storage Capacity

#### Objective

Determine how memory recovery degrades as you vary:

- **Network size** (number of neurons)
- **Number of stored memories**

To generate $m$ memories $\xi_1, \dots, \xi_m$ for a network of size $N$, use:

```python
import numpy as np
xi = 2 * (np.random.rand(m, N) > 0.5) - 1
```

#### Method

- For each configuration, run multiple trials.
- For each trial, measure whether **at least 99%** of the memory is recovered.

#### Visualization 1

Create a heatmap:

- $x$-axis: network size  
- $y$-axis: number of stored memories  
- Color: proportion of memories retrieved with ≥99% accuracy

#### Visualization 2

Plot the expected number of accurately retrieved memories vs. network size.

Let:

- $P[m, N] \in [0, 1]$: proportion of $m$ memories accurately retrieved in a network of size $N$
- $\mathbb{E}[R_N]$: expected number of successfully retrieved memories

Then:

$$
\mathbb{E}[R_N] = \sum_{m=1}^{M} m \cdot P[m, N]
$$

Where $M$ is the maximum number of memories tested.

#### Follow-Up

- What relationship (if any) emerges between network size and capacity?
- Can you develop rules or intuitions that help predict a network’s capacity?

---

### 4. Simulate Cued Recall

#### Objective

Evaluate how the network performs associative recall when only a **cue** is presented.

#### Setup: A–B Pair Structure

- Each memory consists of two parts:
  - First half: **Cue** ($A$)
  - Second half: **Response** ($B$)

If $N$ is odd:
- Let cue length = $\lfloor N/2 \rfloor$
- Let response length = $\lceil N/2 \rceil$

Each full memory:

$$
\xi^\mu = \begin{bmatrix} A^\mu \\ B^\mu \end{bmatrix}
$$

#### Simulation Procedure

1. **Choose a memory** $\xi^\mu$
2. **Construct initial state** $x$:
   - Cue half: set to $A^\mu$
   - Response half: set to 0
3. **Evolve the network** using the update rule:

   $$
   x_i \leftarrow \text{sign} \left( \sum_j J_{ij} x_j \right)
   $$

   - Optionally: **clamp** the cue (i.e., hold cue values fixed)
4. **Evaluate success**:
   - Compare recovered response to $B^\mu$
   - Mark as successful if ≥99% of bits match:

     $$
     \frac{1}{|B|} \sum_{i \in \text{response}} \mathbb{1}[x^*_i = B^\mu_i] \geq 0.99
     $$

#### Analysis

- Repeat across many $A$–$B$ pairs
- For each network size $N$, compute the expected number of correctly retrieved responses
- Plot this value as a function of $N$

#### Optional Extensions

- Compare performance with and without clamping the cue
- Try cueing with noisy or partial versions of $A$

---

### 5. Simulate Contextual Drift

#### Objective

Investigate how gradual changes in **context** influence which memories are recalled.

#### Setup: Item–Context Representation

- Use a Hopfield network with 100 neurons.
- Each memory:
  - First 50 neurons: **Item**
  - Last 50 neurons: **Context**

Create a sequence of 10 memories:

$$
\xi^t = \begin{bmatrix} \text{item}^t \\ \text{context}^t \end{bmatrix}
$$

Context drift:

- Set $\text{context}^1$ randomly
- For each subsequent $\text{context}^{t+1}$, copy $\text{context}^t$ and flip ~5% of the bits

#### Simulation Procedure

1. Store all 10 memories in the network.
2. For each memory $i = 1, \dots, 10$:
   - Cue the network with $\text{context}^i$
   - Set item neurons to 0
   - Run until convergence
   - For each stored memory $j$, compare recovered item to $\text{item}^j$
   - If ≥99% of bits match, record $j$ as retrieved
   - Record $\Delta = j - i$ (relative offset)

#### Analysis

- Repeat the procedure (e.g., 100 trials)
- For each $\Delta \in [-9, +9]$, compute:
  - Probability of retrieval
  - 95% confidence interval

#### Visualization

Create a line plot:

- $x$-axis: Relative position $\Delta$
- $y$-axis: Retrieval probability
- Error bars: 95% confidence intervals

Write a brief interpretation of the observed pattern.

#### Optional Extensions

- Vary the drift rate and observe the effect
- Try random (non-gradual) context changes
- Explore links to recency effects or memory generalization

---

## Submission Instructions

- [Submit](https://canvas.dartmouth.edu/courses/71051/assignments/517353) a single standalone Google Colaboratory notebook (or similar) that includes:
  - Your full model implementation
  - Markdown cells explaining your methods, assumptions, and findings
  - Plots and results for each section
- Your notebook should run **without errors** in Google Colaboratory.
