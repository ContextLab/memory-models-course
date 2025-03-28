# Assignment 2: Search of Associative Memory (SAM) Model

## Overview
In this assignment, you will implement the **Search of Associative Memory (SAM) model** as described in [Atkinson & Shiffrin (1968)](https://www.dropbox.com/scl/fi/rpllozjcv704okckjdy5k/AtkiShif68.pdf?rlkey=i0azhj9mqxws7bxocbl65j88d&dl=1). The SAM model is a probabilistic model of free recall that assumes items are stored in **short-term memory (STS)** and **long-term memory (LTS)**, with retrieval governed by associative processes. You will fit your implementation to [Murdock (1962)](https://www.dropbox.com/scl/fi/k7jc1b6uua4m915maglpl/Murd62.pdf?rlkey=i5nc7lzb2pw8dxc6xc72r5r5i&dl=1) free recall data and evaluate how well the model explains the observed recall patterns.

## Data Format and Preprocessing
The dataset consists of sequences of recalled items from multiple trials of a free recall experiment. Each row represents a participant’s recall sequence from a single list presentation.

- **Numbers (1, 2, ..., N)**: Serial position of recalled items.
- **88**: Denotes errors (recalls of items that were never presented)
- **Example:**
  
  ```
  6 1 4 7 10 2 8  
  10 9 6 8 2 1 88  
  10 7 9 8 1  
  ```

  This represents three recall trials. Each row contains the order in which items were recalled for one list.

## Model Description

You will implement and fit a **Search of Associative Memory (SAM)** model that consists of:

### 1. **Encoding Stage: Memory Storage**
When an item is presented, it is stored in **both STS and LTS**:
- **STS (Short-Term Store)**: Holds a **limited** number of items (size $S$), with older items **displaced** as new ones enter.
- **LTS (Long-Term Store)**: Each item is encoded with a **strength** $S_i$ that depends on:
  - The **number of times it was rehearsed in STS**.
  - A decay function over time.

#### **Encoding Rules**
- **Rehearsal Buffer Size ($S$)**: At any time, only **$S$ most recent items** are actively rehearsed.
- **Probability of Rehearsal ($P_r$)**: Given by:

  $$P_r(i) = \frac{R_i}{\sum_j R_j}$$

  where $R_i$ is the number of times item $i$ was rehearsed.

- **Strength in LTS ($S_i$)**: Memory strength decays with time and interference:

  $$S_i = \alpha \cdot R_i \cdot e^{-\beta (T - t_i)}$$

  where:
  - $\alpha$ is the **encoding rate parameter** (controls how well rehearsed items are stored).
  - $\beta$ is the **decay rate** (how quickly memory strength fades over time).
  - $T$ is the time since the start of the list, and $t_i$ is when the item was last rehearsed.

### 2. **Retrieval Stage: Memory Search**
The recall process is modeled as a **probabilistic search** through LTS.

#### **Retrieval Probability**
Each item's **retrievability** depends on its memory strength:

  $$P_{\text{recall}}(i) = \frac{S_i}{\sum_j S_j}$$

- Items with higher **$S_i$** are **more likely** to be retrieved.
- Retrieval **stochasticity** is modeled via a **retrieval threshold**—if an item’s strength is too low, recall terminates.

#### **Search Dynamics**
- Recall **starts with a high-probability item** (often first or last presented).
- **Associative transitions** determine what item comes next:
  - If item $i$ was just recalled, the next recall follows a probability based on the similarity (lag) between memories.

  $$P(l) = \frac{S_{i+l}}{\sum_j S_j}$$

  where:
  - $l$ is the **lag** (distance in serial position from the previously recalled item).
  - $S_{i+l}$ represents memory strength at that position.

- Recall **continues** until a failure occurs (e.g., no item surpasses a retrieval threshold).

### 3. **Fitting the Model**
You will fit **three key parameters** to optimize the match to human recall data:
1. **Rehearsal Buffer Size $S$**: Number of items actively rehearsed in STS.
2. **Encoding Rate $\alpha$**: Governs how well rehearsed items are stored in LTS.
3. **Decay Rate $\beta$**: Controls how quickly memory traces degrade over time.

## Implementation Tasks

### **Step 1: Implement Memory Encoding**
- Create an STS buffer of size $S$.
- Assign memory strengths $S_i$ to each item based on rehearsal frequency.
- Implement the decay function for LTS.

### **Step 2: Implement Retrieval Process**
- Use probabilistic sampling to simulate recall.
- Implement stopping rules for retrieval failures.
- Simulate **associative transitions** in recall.

### **Step 3: Load and Process Data**
- Read in the recall dataset and parse trials correctly.
- Compute observed recall probabilities.

### **Step 4: Fit Model Parameters**
- Adjust $S$, $\alpha$, and $\beta$ to optimize match to observed data.

### **Step 5: Generate Key Plots**
For each combination of **list length** and **presentation rate**, generate the following plots:

#### **Plot 1: Probability of First Recall as a Function of Serial Position**
- **X-axis:** Serial position in the presented list.
- **Y-axis:** Probability that the item is the **first item** recalled.
- Overlay **model predictions** on observed data.

#### **Plot 2: Conditional Recall Probability as a Function of Lag**
- **X-axis:** Lag ($l$) (distance between successive recalls).
- **Y-axis:** Probability of recalling an item at position $i+l$ after recalling $i$.
- Overlay **model predictions** on observed data.

#### **Plot 3: Overall Probability of Recall as a Function of Serial Position**
- **X-axis:** Serial position in the presented list.
- **Y-axis:** Probability of recall at **any output position**.
- Overlay **model predictions** on observed data.

Each plot should include:
- **Observed human recall data** (solid line).
- **Model predictions** (dashed line).
- **Error bars** (95% confidence intervals).

### **Step 6: Evaluate the Model**
- Write a **brief discussion** (3-5 sentences) addressing:
  - **Does the model explain the data well?**
  - **Which patterns are well captured?**
  - **Where does the model fail, and why?**
  - **Potential improvements or limitations of SAM.**

## Grading Criteria
- **Model Implementation (50%)**:
  - Correct encoding, retrieval, and stopping rule logic.
  - Accurate probability calculations.
  - Clear and well-documented code.

- **Plot Accuracy (45%)**:
  - **15%** for each of the three required plots.
  - Must correctly overlay observed data and model predictions.

- **Interpretation and Discussion (5%)**:
  - Thoughtful analysis of model fit.
  - Identifies strengths and weaknesses of SAM.

## Submission Instructions
- Submit a **Google Colaboratory notebook** (or similar) that includes:
  - Your **full implementation** of the SAM model.
  - **Markdown cells** explaining your code, methodology, and results.
  - **All required plots** comparing model predictions to observed data.
  - **A short written interpretation** of model performance.

Good luck, and happy modeling!