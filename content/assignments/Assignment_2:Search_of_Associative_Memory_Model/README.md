# Assignment 2: Search of Associative Memory (SAM) Model

## Overview
In this assignment, you will implement the **Search of Associative Memory (SAM) model** as described in [Kahana (2012), Chapter 7](https://www.dropbox.com/scl/fi/ujl8yvxqzcb1gf32to4zb/Kaha12_SAM_model_excerpt.pdf?rlkey=254wtw4fm7xnpzelno2ykrxzu) (which is, in turn, adapted from [Atkinson and Shiffrin, 1968](https://www.dropbox.com/scl/fi/rpllozjcv704okckjdy5k/AtkiShif68.pdf?rlkey=i0azhj9mqxws7bxocbl65j88d)). The SAM model is a probabilistic model of free recall that assumes items are encoded into a **short-term store (STS)** and **long-term store (LTS)**, with retrieval governed by associative processes. You will fit your implementation to [Murdock (1962)](https://www.dropbox.com/scl/fi/k7jc1b6uua4m915maglpl/Murd62.pdf?rlkey=i5nc7lzb2pw8dxc6xc72r5r5i&dl=1) free recall data and evaluate how well the model explains the observed recall patterns.

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

  The file names (e.g., `fr10-2.txt`) denote the list length (first number) and presentation rate, in seconds (second number), respectively.

## Model Description

The SAM model defines how memories are **encoded** and **retrieved**, as follows:

### 1. **Encoding Stage: Memory Storage**
When an item is presented, it is stored in **both STS and LTS**:
- **STS (Short-Term Store)**: Holds a **limited** number of items (size $r$), with older items **displaced** as new ones enter.  The probability of being displaced is goverened by a displacement parameter, $q$, along with the item's age in short term memory (i.e., the number of timesteps elapsed since it entered the STS; $t$):

  $$
  p(\text{displacement of item with age }t) = \frac{q (1 - q)^{t - 1}}{1 - (1 - q)^r}
  $$

- **LTS (Long-Term Store)**: Holds an (effectively) **unlimited number of associations** between (a) **pairs of items** and (b) **items and context**.  In this model, "context" acts like an "item" that is ever-present in the STS (and can never be recalled).  The association strength between items $i$ and $j$ is denoted $S(i, j)$ and the association strength between item $i$ and $context$ is denoted $S(i, context)$.  With each new item presentation, $S(i, j)$ is incremented by $a$ for every pair if items $i$ and $j$ that occupy STS at the same time:
  
  $$
  a =
    \begin{cases}
    a_{\text{forward}}, & \text{if } t(i) \geq t(j) \\
    a_{\text{backward}}, & \text{if } t(i) < t(j)
    \end{cases},
  $$

  where $t(i)$ and $t(j)$ denote the ages of items $i$ and $j$ in the STS, respectively.  In addition, $S(i, context)$ is also incremented by $a_{\text{forward}}$ for each item in STS at a given timestep.  In other words, after studying a given list, the associations $S(i, context)$ for each item $i$ will be proportional to the number of timesteps that each item remained in STS.

### 2. **Retrieval Stage: Memory Search**
The retrieval process happens in several stages, first involving **STS** and then involving **LTS**:
- **Recall from STS**: while items remain in STS, they are selected at random, recalled, and then removed from STS.  Item selection may either be uniform, or may be set to be inversely proportional to each item's age, $t_{\text{rel}}$, relative to the youngest item still in STS (and assuming that $k$ items remain in STS):

  $$
  p(\text{recall of item with relative age }t_{\text{rel}}) \propto \frac{1 - (1 - q)^k}{q (1 - q)^{t_{\text{rel}} - 1}}
  $$
- After STS has been emptied, we begin to retrieve items from LTS through two pathways: either associations with **context** or associations between *both* **context and other items**.  In addition, all retrievals happen in a two-part process (until either the process is halted as described below, or until all studied items have been recalled):
  - First, an item is *sampled*.  This is how an item is "chosen" for consideration as a recall candidate.
  - Second, the sampled item is *potentially recalled*.  Recall is a probabilistic process (i.e., not guaranteed to happen for every sampled item).

#### Sampling

The probability of sampling item $i$ through its associations with context is given by

  $$
  p(\text{sampling item }i | context) = \frac{S(i, context)^{W_c}}{\sum_n^N S(n, context)^{W_c}},
  $$

where $W_c$ is a scalar parameter that governs the "contextual" cueing process.  Similarly, the probability of sampling item $i$ through its associations with *both* item $j$ *and* context is given by:

  $$
  p(\text{sampling item }i | j, context) = \frac{S(i, j)^{W_e}S(i, context)^{W_c}}{\sum_n^N S(n, j)^{W_e}S(n, context)^{W_c}},
  $$

where $W_e$ is an analogous scalar parameter that governs the "episodic" cueing process.

We also place an additional constraint on the sampling procedure to prevent repeated recalls: if the sampled item has already been recalled, we re-run the sampling procedure.  This occurs as many times as needed to sample a not-yet-recalled item.

#### Recall

If an item $i$ is sampled through its associations with *context*, then it is *recalled* with probability given by:

 $$
 p(\text{recall item }i | context) = 1 - \exp{\\( -W_c S(i, context)\\)}.
 $$

Alternatively, if an item $i$ is sampled through its associations with both item $j$ and context, then:

 $$
 p(\text{recall item }i | j, context) = 1 - \exp{\\( -W_e S(i, j) - W_c S(i, context)\\)}.
 $$

To decide whether a given recall (of item $i$) occurs, draw a random number $\theta$ uniformly from the interval $[0, 1]$.  If $\theta < p(\text{recall item }i)$ then the item is recalled (and two "counter" parameters, $m_1$ and $m_2$, are both reset to 0).  Otherwise the recall failure procedure is called next.

#### Recall failures

In the event that an item is sampled but *not* recalled, the model edges closer to a stop condition by incrementing a counter.  If the item was sampled via *context alone*, then we increment $m_1$.  If $m_1 > m_{1_{\text{max}}}$, the recall procedure is halted.  Alternatively, if the item was sampled via *context and its associations with another item*, then we instead increment $m_2$.  If $m_2 > m_{2_{\text{max}}}$, the recall procedure is halted.  In either scenario, if the relevant threshold has not yet been reached, the next candidate item is sampled using *context alone* as a retrieval cue.


### 3. **Fitting the Model**
You will fit **eight parameters** to optimize the match to human recall data:
1. $r$: number of items that can fit in STS
2. $q$: STS displacement parameter
3. $a_{\text{forward}}$: LTS memory strength increment in the *forward* direction
4. $a_{\text{backward}}$: LTS memory strength increment in the *backward* direction
5. $W_c$: contextual association parameter
6. $W_e$: episodic association parameter
7. $m_{1_{\text{max}}}$: maximum number of *contextual* association cueing failures
8. $m_{2_{\text{max}}}$: maximum number of *episodic* association cueing failures

You can choose any approach you wish to fit these parameters.  My "recommended" approach is to use [scipy.optimize.minimize](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html) to minimize the mean squared error between the point-by-point observed vs. model-predicted values for the following behavioral curves:
  - $p(\text{first recall})$: probability of recalling each item **first** as a function of its *presentation position*
  - $p(\textit{recall})$: probability of recalling each item at *any* output position as a function of its presentation position
  - lag-CRP: probability of recalling item $i$ given that item $j$ was the previous recall, as a function of $lag = i - j$.

The "right" way to do this is to use a subset of the data to estimate the parameters (i.e., training data), and then plot the observed and predicted results for the remaining (held-out)  test data.  You'll need to do some experimenting to determine an appropriate proportion of the data to assign to the training vs. test datasets.  (Randomly assigning half of the data to each group is a good place to start.)

## Implementation Tasks
You can use the [example notebook](https://contextlab.github.io/memory-models-course/assignments/Assignment_2%3ASearch_of_Associative_Memory_Model/sam_assignment_template.html) to help get you started with implementing the SAM model.

### **Step 1: Implement Memory Encoding**
- Fill in the missing code for the `present` method in the `STS` class
- Fill in the missing code for the `update` method in the `LTS` class

### **Step 2: Implement Retrieval Process**
- Fill in the missing code for the `retrieve` method in the `SAM` class

### **Step 3: Load and Process Data**
- Read in the recall dataset
- Write functions for assigning trials to the training and test datasets

### **Step 4: Generate Behavioral Curves**
- Generate the following averaged curves, for each list length and presentation rate:
  - $p(\text{first recall})$
  - $p(\textit{recall})$
  - lag-CRP
- To help with computing mean squared error, it will be useful to have a function that takes in a dataset as input and returns a vector comprising each of these curves, for each list length and presentation rate, concatenated together into a single vector.

### **Step 4: Fit Model Parameters**
- To compute mean squared error for a given set of model parameters, use the function you wrote above to compute the concatenated behavioral curves for the *observed recalls* and the *model-predicted recalls*.  The average squared point-by-point difference between the vectors is the mean squared error.  You'll want to set up [scipy.optimize.minimize](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html) to find the set of model parameters that minimizes the mean squared error between the observed and predicted curves, using only the training dataset.
- Importantly, you should use the same parameters across all trials and experimental conditions.  You're fitting the *average* performance, not data from individual trials or participants.

### **Step 5: Generate Key Plots**
For each combination of **list length** and **presentation rate**, plot the *observed* and *model-predicted* behavioral curves for the *test* data:
  - $p(\text{first recall})$
  - $p(\textit{recall})$
  - lag-CRP

Use dotted lines for the observed curves and solid lines for the model-predicted curves.  Each metric should get its own figure (or sub-figure).  You can either plot the curves for different list lengths and presentation rates on the same axes (e.g., using different colors for each experimental condition) or on different axes (one per experimental condition).

### **Step 6: Evaluate the Model**
- Write a **brief discussion** (3-5 sentences) addressing:
  - **Does the model explain the data well?**
  - **Which patterns are well captured?**
  - **Where does the model fail, and why?**
  - **Potential improvements or limitations of SAM.**

## Optional extensions
- Pick a behavioral curve and a parameter.  Holding all of the other parameters fixed to their best-fitting values, vary the given parameter.  How does the predicted behavioral curve change?  Which predictions are sensitive to which parameters?
- Can you figure out a way to generate *distributions* of parameter estimates?  For example, what happens if you fit the model to individual trials (or small subsets of trials)?  Extra challenge: explore the covariance structure between different combinations of parameters.  Describe any interesting relationships between parameters that you observe.


## Submission Instructions
- [Submit](https://canvas.dartmouth.edu/courses/71051/assignments/517354) a **Google Colaboratory notebook** (or similar) that includes:
  - Your **full implementation** of the SAM model.
  - **Markdown cells** explaining your code, methodology, and results.
  - **All required plots** comparing model predictions to observed data.
  - **A short written interpretation** of model performance.

Good luck, and happy modeling!