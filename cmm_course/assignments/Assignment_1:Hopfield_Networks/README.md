# Assignment 1: Hopfield Networks

## Overview
In this assignment, you will explore computational memory models by implementing a Hopfield network. You will first read the primary research article (attached) and then code up the model in a Google Colaboratory notebook. Your implementation will allow you to study various aspects of memory storage and retrieval using a network of binary neurons.

## Tasks

### 1. Implement Memory Storage and Retrieval
- **Objective:** Write functions that implement the core operations of a Hopfield network:
  - **Memory Storage:** Implement the Hebbian learning rule to compute the weight matrix \($W$\). The weight matrix should be computed as:
    
    $$
    W_{ij} = \frac{1}{N} \sum_{\mu=1}^{P} \xi_i^\mu \xi_j^\mu
    $$

    where:
    - \($N$\) is the number of neurons,
    - \($$\) is the number of stored memories,
    - \($\xi^\mu$\) represents a stored binary memory pattern (\($\pm1$\)),
    - \($W_{ii} = 0$\) (no self-connections).

  - **Memory Retrieval:** Implement the asynchronous update rule:

    $$
    V_i^{t+1} = \text{sign} \left( \sum_{j} W_{ij} V_j^t \right)
    $$

    where:
    - \($V^t$\) is the current state of the network,
    - The sign function ensures binary activation (\($\pm1$\)).

### 2. Test with a Small Network (5% of Grade)
- **Objective:** Encode the following test memories in a small Hopfield network with \( N = 5 \) neurons:
  
  $$
  \xi^1 = [1, -1, 1, -1, 1]
  $$
  $$
  \xi^2 = [-1, 1, -1, 1, -1]
  $$
  
  - Store these memories using the Hebbian rule.
  - Test memory retrieval by presenting the network with noisy versions of the stored patterns (e.g., flipping one bit).
  - Discuss the results and provide insights into how the network behaves.

### 3. Evaluate Storage Capacity
- **Objective:** Determine how the ability to recover memories degrades as you vary:
  - **Network Size:** The total number of neurons in the network.
  - **Number of Stored Memories:** The number of patterns stored in the network.

- **Method:**
  - For each configuration, run multiple trials to compute the proportion of times that at least 99% of a memory is accurately recovered.
  - **Visualization:** Create a heatmap where one axis represents the network size and the other represents the number of stored memories, with the color indicating the recovery accuracy.

### 4. Simulate Cued Recall
- **Objective:** Explore how the network handles paired associations:
  - **Setup:** Divide the neuron features into two halves, where the first half represents the "cue" (A) and the second half represents the "response" (B) in an A-B pair.
  - **Task:** For each trial, present the network with a cue and measure the proportion of times the corresponding response is recovered with at least 99% accuracy.
  - **Visualization:** Generate a heatmap that shows the accuracy of cued recall as a function of network size and the number of stored memories.

### 5. Simulate Contextual Drift
- **Objective:** Investigate how context perturbations affect memory retrieval:
  - **Setup:** Use a network with 100 neurons and encode a sequence of 10 memories. In this simulation:
    - Half of the neuron features represent the item.
    - The other half represent the "context" (initialized randomly, with a small proportion of features perturbed each time a new memory is stored).
  - **Task:** When the network is cued with the context of memory *i*, determine the probability of retrieving any memory as a function of its position relative to *i*.
  - **Visualization:** Create a plot with 95% confidence interval error bars showing the retrieval probability versus the relative position in the sequence.

## Grading Criteria
- **Accuracy of Implementation (45%)**
  - Correct implementation of the storage/retrieval functions and simulation tasks.
- **Small Network Example (5%)**
  - Accurate construction of the weight matrix and correct retrieval of stored memories.
- **Code Quality (25%)**
  - Clear, well-documented, and efficient code.
- **Correctness of Results (25%)**
  - **10% each:** For the two heatmaps (storage capacity and cued recall).
  - **5%:** For the contextual drift simulation plot.

## Submission Instructions
- Submit a single Google Colaboratory notebook that includes:
  - Your full Python implementation.
  - Markdown cells explaining your approach, methodology, and any design decisions.
  - Plots and results for each simulation task.
- Ensure that your notebook runs without errors in Google Colaboratory.

Good luck, and be sure to thoroughly test your implementation!
