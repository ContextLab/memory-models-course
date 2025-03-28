# Assignment 4: Laplace Temporal Context Model (Laplace TCM)

## Overview
In this assignment, you will implement the **Laplace Temporal Context Model (Laplace TCM)** as described in **Shankar & Howard (2012)**. The Laplace TCM provides a **scale-invariant representation of time** by maintaining a **Laplace-transformed representation of temporal history**, which can be used to predict future stimuli based on past experience.

Unlike previous assignments, instead of fitting the model to an experimental dataset, you will use **synthetic examples** to explore its behavior. Specifically, you will **reproduce Figures 2, 3, and 4 from the paper**, analyze how the quality of reconstructions changes with different numbers of nodes, and reflect on the model’s strengths and limitations.

## Model Description

The Laplace TCM constructs a **compressed representation of time** based on two key steps:
1. **Encoding Past Events as a Laplace Transform**  
   - A stimulus function **$f(\tau)$** (past stimulus history) is encoded into a **set of leaky integrators** with different decay rates **$s$**.
   - This encoding follows:

     $$
     t(\tau, s) = \int_{-\infty}^{\tau} f(\tau') e^{s (\tau' - \tau)} d\tau'
     $$

     where **$t(\tau, s)$** represents the Laplace transform of the stimulus history up to time **$\tau$**.

2. **Reconstructing Time from the Laplace Representation**  
   - The **inverse Laplace transform** is approximated using a set of neurons called **time cells** with different peak latencies **$\tau^*$**:

     $$
     T(\tau, \tau^*) = (-1)^k \frac{k!}{(s^{k+1})} \frac{d^k}{ds^k} t(\tau, s) \Bigg|_{s=-\frac{k}{\tau^*}}
     $$

   - The function **$T(\tau, \tau^*)$** reconstructs past stimulus history, allowing **temporal predictions**.

## Implementation Tasks

### **Step 1: Implement the Model**
- Implement the **Laplace encoding** function to generate **$t(\tau, s)$** from a stimulus function.
- Implement the **approximate inverse Laplace transform** using the **Post (1930) inversion formula** to reconstruct **$T(\tau, \tau^*)$**.
- Implement a simple **Hebbian learning rule** to associate time cells with past stimuli.

### **Step 2: Reproduce Key Figures from the Paper**
You will **generate synthetic stimuli** and reproduce the following figures:

#### **Figure 2: Leaky Integrators Encoding Temporal History**
- **Stimulus:** Two pulses at different times.
- **Plot:** The responses of **leaky integrators** with different decay rates **$s$**.
- **Insight:** Larger **$s$** values decay quickly, while smaller **$s$** retain long-term information.

#### **Figure 3: Time Cell Representation of Stimulus History**
- **Stimulus:** Same as Figure 2.
- **Plot:** Activity of **time cells** with different peak latencies **$\tau^*$**.
- **Insight:** Each time cell **stores a different temporal segment** of the past.

#### **Figure 4: Time Cells Over Time**
- **Stimulus:** Two pulses.
- **Plot:** Evolution of **two time cells** over time.
- **Insight:** Cells peak at fixed latencies, but **older events are reconstructed with increasing uncertainty**.

### **Step 3: Explore Model Performance**
- **Vary the number of time cells** (length of **$T$** nodes) and evaluate how well the past is reconstructed.
- **Hypothesis:** With more nodes, reconstructions become more precise.

### **Step 4: Write Reflections**
- Discuss the strengths of the **Laplace TCM**.
- Consider its limitations (e.g., loss of precision for older memories).
- Suggest potential improvements.

## Grading Criteria
- **Model Implementation (50%)**  
  - Correct encoding and reconstruction of time.
  - Accurate implementation of **leaky integrators** and **time cells**.

- **Figure Reproduction (30%)**  
  - **10% each** for Figures **2, 3, and 4**.

- **Exploration of Node Count Effects (10%)**  
  - Vary **number of nodes** and analyze reconstruction quality.

- **Reflections (10%)**  
  - Thoughtful discussion of **model strengths, weaknesses, and improvements**.

## Submission Instructions
- Submit a **Google Colaboratory notebook** (or similar) that includes:
  - Your **full implementation** of the Laplace TCM.
  - **Markdown explanations** of your approach.
  - **Reproduced figures (2, 3, and 4)**.
  - **Analysis of node count effects**.
  - **A short written reflection** on the model.

Good luck, and happy modeling!
