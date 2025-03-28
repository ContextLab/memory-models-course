# Assignment 3: Context Maintenance and Retrieval (CMR)

## Overview
In this assignment, you will implement the **Context Maintenance and Retrieval (CMR) model** as described in [Polyn, Norman, & Kahana (2009)](https://www.dropbox.com/scl/fi/98pui63j3o62xu96ciwhy/PolyEtal09.pdf?rlkey=42sc17ll573sm83g4q8q9x9nq&dl=1). CMR is a **context-based model of memory search**, extending the **Temporal Context Model (TCM)** to explain how **temporal, semantic, and source context** jointly influence recall. You will fit your implementation to [Murdock (1962)](https://www.dropbox.com/scl/fi/k7jc1b6uua4m915maglpl/Murd62.pdf?rlkey=i5nc7lzb2pw8dxc6xc72r5r5i&dl=1) free recall data and evaluate how well the model explains the observed recall patterns.

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

You will implement and fit a **Context Maintenance and Retrieval (CMR)** model that consists of:

### 1. **Encoding Stage: Context Representation**
- Each studied item is **associated with a context representation** composed of:
  - **Temporal context** (slowly drifting across time).
  - **Semantic context** (pre-existing associative relations).
  - **Source context** (internal or external cues such as task or modality).

- When an item is presented, its **item features** interact with the **current context**. Context is **gradually updated** via:

  $$ c_i = \rho_i c_{i-1} + \beta c_{in} $$

  where:
  - $c_i$ is the context at position $i$,
  - $\rho_i$ scales how much prior context persists,
  - $\beta$ controls the **drift rate** of context,
  - $c_{in}$ represents the new item-driven input.

- **Semantic context** is pre-defined by prior associations, while **temporal and source contexts evolve dynamically**.

### 2. **Retrieval Stage: Memory Search**
Recall is modeled as a **probabilistic competition** where **activated context cues** retrieve stored items:

#### **Activation of Memory Traces**
- Each item has an **activation score** based on how well it matches the current context:

  $$ f_{in} = M_{CF} c_i $$

  where:
  - $M_{CF}$ represents learned **context-to-item** associations.
  - Higher $f_{in}$ means a greater probability of recall.

- **Items compete** for recall through a **leaky, competitive accumulation process**:

  $$ x_s = (1 - \tau \kappa - \tau \lambda N) x_{s-1} + \tau f_{in} + \epsilon $$

  where:
  - $\kappa$ controls **memory decay**,
  - $\lambda$ determines **inhibition between competing items**,
  - $\tau$ sets the **speed of evidence accumulation**.

- Once an item **crosses a recall threshold**, it is **recalled and updates context**, influencing **subsequent recalls**.

### 3. **Fitting the Model**
The following **three key parameters** should be optimized to best match human recall data:
1. **Context Drift Rate ($\beta$)**: How quickly temporal and source context updates.
2. **Memory Decay ($\kappa$)**: Governs how quickly items lose activation.
3. **Inhibition ($\lambda$)**: Controls competition between retrieved items.

## Implementation Tasks

### **Step 1: Implement Context Evolution**
- Implement **temporal and source context updates** during study.
- Encode **semantic relations** using a **fixed association matrix**.

### **Step 2: Implement the Recall Process**
- Compute **retrieval strengths** using **context-to-item activation**.
- Implement the **competitive accumulation** process.
- Simulate **context reinstatement** from retrieved items.

### **Step 3: Load and Process Data**
- Read in the **recall dataset** and parse trials correctly.
- Compute **observed recall probabilities** for comparison.

### **Step 4: Fit Model Parameters**
- Optimize **$\beta$ (context drift)**, **$\kappa$ (memory decay)**, and **$\lambda$ (inhibition)** to best match recall patterns.

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
  - **Potential improvements or limitations of CMR.**

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
  - Identifies strengths and weaknesses of CMR.

## Submission Instructions
- Submit a **Google Colaboratory notebook** (or similar) that includes:
  - Your **full implementation** of the CMR model.
  - **Markdown cells** explaining your code, methodology, and results.
  - **All required plots** comparing model predictions to observed data.
  - **A short written interpretation** of model performance.

Good luck, and happy modeling!