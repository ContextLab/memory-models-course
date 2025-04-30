# Assignment 3: Context Maintenance and Retrieval (CMR)

## Overview
In this assignment, you will implement the **Context Maintenance and Retrieval (CMR) model** as described in [Polyn, Norman, & Kahana (2009)](https://www.dropbox.com/scl/fi/98pui63j3o62xu96ciwhy/PolyEtal09.pdf?rlkey=42sc17ll573sm83g4q8q9x9nq). CMR is a **context-based model of memory search**, extending the **Temporal Context Model (TCM)** to explain how **temporal, semantic, and source context** jointly influence recall. You will fit your implementation to Polyn et al. (2009)'s task-switching free recall data and evaluate how well the model explains the observed recall patterns.

## Data Format and Preprocessing
The dataset comprises sequences of presented and recalled words (concrete nouns) from multiple trials of a free recall experiment. As they were studying each word, participants were either asked to judge the referent's *size* (would it fit in a shoebox?) or *animacy* (does it refer to a living thing?).  The dataset also includes information about the similarities in meaning between all of the stimuli (semantic similarities).

Code for downloading and loading the dataset into Python, along with a more detailed description of its contents, may be found in the [template notebook for this assignment](https://github.com/ContextLab/memory-models-course/blob/main/content/assignments/Assignment_3%3AContext_Maintenance_and_Retrieval_Model/cmr_assignment_template.ipynb).

## High-level Model Description

The Context Maintenance and Retrieval (CMR) model comprises three main components:

### 1. **Feature layer ($F$)**

The feature layer represents the experience of the *current moment*.  It comprises a representation of the item being studied (an indicator vector of length number-of-items + 1) concatenated with a representation of the current "source context" (also an indicator vector, of length number-of-sources).

### 2. **Context layer ($C$)**

The context layer represents a *recency-weighted average* of experiences up to now.  Analogous to the feature layer, the context layer comprises a representation of temporal context (a vector of length number-of-items + 1, representing a transformed version of the item history) concatenated with a representation of the source context (a vector of length number-of-sources, representing a transformed version of the history of sources).

### 3. **Association matrices**

The feature and context layers of the model interact through a pair of association matrices:

- $M^{FC}$ controls how activations in $F$ affect activity in $C$
- $M^{CF}$ controls how activations in $C$ affect activity in $F$

## Model dynamics

### Encoding

Items are presented one at a time in succession; all of the steps in this section are run for each new item.  As described below, following a task shift an "extra" (non-recallable) item is "presented," causing $c_i$, $M^{FC}$, and $M^{CF}$ to update.

1. As each new item (indexed by $i$) is presented, the feature layer $F$ is set to $f_i = f_{item} \oplus f_{source}$, where:
  - $f_{item}$ is an indicator vector of length number-of-items + 1.  Each item is assigned a unique position, along with an additional "dummy" item that is used to represent non-recallable items.
  - $f_{source}$ is an indicator vector of length number-of-sources.  Each possible "source" (i.e., unique situation or task experienced alongside each item) gets one index.
  - $\oplus$ is the concatenation operator.

2. Next, the feature activations project onto the context layer:
  - We compute $c^{IN} = M^{FC} f_i$
  - Then we evolve context using $c_i = \rho_i c_{i - 1} + \beta c^{IN}$, where
    - $\rho_i = \sqrt{1 + \beta^2\left[ \left( c_{i - 1} \cdot c^{IN}\right)^2 \right]} - \beta \left( c_{i - 1} \cdot c^{IN}\right)$.
    - In setting $\rho_i$, the computations are performed separately for the "item" and "source" parts of context, where $\beta_{enc}^{temp}$ is used to evolve context for the item features and $\beta_{source}$ is used to evolve context for the source features.
    - After a task shift, a "placeholder" item is "presented", and a fourth drift rate parameter ($d$) is used in place of $\beta$.

3. Next, we update $M^{FC}$ (initialized to all zeros):
    - Let $\Delta M^{FC}_{exp} = c_i f_i^T$
    - $M^{FC} = (1 - \gamma^{FC}) M^{FC}_{pre} + \gamma^{FC} \Delta M^{FC}_{exp}$

4. Also update $M^{CF}$:

    - $M^{CF}_{pre}$ is fixed at the matrix of LSA $\cos \theta$ across words, multiplied by $s$
    - $M^{CF}_{exp}$ is initialized to all zeros
    - Let $\Delta M^{CF}_{exp} = \phi_i L^{CF} f_i c_i^T$, where
      - $L^{\text{CF}} = 
\left[
\begin{array}{cc}
L_{tw}^{\text{CF}} & L_{ts}^{\text{CF}} \\
L_{sw}^{\text{CF}} & L_{ss}^{\text{CF}}
\end{array}
\right]$
      - $t$ represents temporal context
      - $s$ represents source *context* if listed first, or source *features* if listed second
      - $w$ represents item features
      - $L^{CF}_{sw}$ is a parameter of the model (all set to the same value-- size is number-of-sources by (number-of-items + 1))
      - $L^{CF}_{ts}$ is set to all zeros; size: (number-of-items + 1) by number-of-sources
      - $L^{CF}_{ss}$ is set to all zeros; size: number-of-sources by number-of-sources
      - $L^{CF}_{tw}$ is set to all ones; size: (number-of-items + 1) by (number-of-items + 1)
      - $\phi_i = \phi_s \exp\{-\phi_d (i - 1\}$ + 1, where $i$ is the serial position of the current item
    - $M^{CF} = M^{CF}_{pre} + M^{CF}_{exp}$

### Retrieval

Recall is guided by *context* using a *leaky accumulator*.  Given the current context, the leaky accumulator process runs until either (a) any item crosses a threshold value of 1 (at which point the item is recalled, its features are reinstated in $F$, context is updated as described below, and the retrieval process restarts), **or** (b) more than 9000 time steps elapse without any item crossing the threshold (1 timestep is roughly equivalent to 1 ms).

1. First compute $f^{IN} = M^{CF} c_i$, where $c_i$ is the current context

2. Next, use $f^{IN}$ to guide the leaky accumulator:
  - Initialize $x_s$ to a vector of number-of-items zeros ($s$ indexes the number of steps in the accumulation process)
  - While no not-yet-recalled element (also ignoring the last "unrecallable" item) of $x_s$ is greater than or equal to 1:
    - Set $x_s = x_{s - 1} + \left( f^{IN} - \kappa x_{s - 1} - \lambda N x_{s - 1} \right) d \tau + \epsilon \sqrt{d \tau}$, where
      - $dt = 100$
      - $d \tau = \frac{dt}{\tau}$
      - $N_{ij} = 0$ if $i = j$ and $1$ otherwise.
      - $\epsilon \sim \mathcal{N}\left(0, \eta \right)$
    - If any *already recalled* item crosses the threshold, reset its value to 0.95 (this simulates "[inhibition of return](https://en.wikipedia.org/wiki/Inhibition_of_return)").
    - If any elements of $x_s$ drops below 0, reset those values to 0.
  - When an item "wins" the recall competition:
    - Reinstate its features in $F$ (as though we were presenting that item as the next $f_i$)
    - Update context from $f_i$ using the same equation for $c_i$ as during presentation.
    - Don't update $M^{CF}$ or $M^{FC}$.
    - Recall the item.

## **Fitting the Model**

In total, there are 13 to-be-learned parameters of CMR (each is a scalar):
1. $\beta_{enc}^{temp}$: drift rate of temporal context during encoding
2. $\beta_{rec}^{temp}$: drift rate of temporal context during recall
3. $\beta^{source}$: drift rate of source context (during encoding)
4. $d$: temporary contextual drift rate during "placeholder item" presentations after source changes
5. $L_{sw}^{CF}$: scale of associative connections between source context and item features
6. $\gamma^{FC}$: relative contribution of $\Delta M_{exp}^FC$ vs. $M_{pre}^{FC}$
7. $s$: scale factor applied to semantic similarities when computing $M_{pre}^{CF}$
8. $\phi_s$: primacy effect scaling parameter
9. $\phi_d$: primacy effect decay parameter
10. $\kappa$: decay rate of leaky accumulator
11. $\lambda$: lateral inhibition parameter of leaky accumulator
12. $\eta$: noise standard deviation in leaky accumulator
13. $\tau$: time constant for leaky accumulator

Fit the model to the following curves and measures from the Polyn et al. (2009) dataset (provided in the template notebook):
  - Probability of first recall
  - Serial position curve
  - Lag-CRP
  - Temporal clustering factor
  - Source clustering factor

There are several possible ways to accomplish this.  My recommended approach is:
1. Split the dataset into a training set and a test set
2. Compute the above curves/measures for the training set and concatenate them into a single vector
3. Use [scipy.optimize.minimize](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html#scipy.optimize.minimize) to find the set of model parameters that minimizes the mean squared error between the observed curves and the CMR-estimated curves (using the given parameters).
4. Compare the observed performance vs. CMR-estimated performance (using the best-fitting parameters) for the test data


## Summary of Implementation Tasks
1. Use the descriptions above to implement CMR in Python
2. Write code for constructing the behavioral curves/measures listed above
3. Fit CMR's parameters to the dataset provided in the template notebook (compare with Table 1 in Polyn et al., 2009)
4. Plot the observed vs. CMR-estimated curves/measures
5. Write a **brief discussion** (3-5 sentences) addressing:
  - **Does the model explain the data well?**
  - **Which patterns are well captured?**
  - **Where does the model fail, and why?**
  - **Potential improvements or limitations of CMR.**

## Submission Instructions
- Submit (on [canvas](https://canvas.dartmouth.edu/courses/71051/assignments/517355)) a **Google Colaboratory notebook** (or similar) that includes:
  - Your **full implementation** of the CMR model.
  - **Markdown cells** explaining your code, methodology, and results.
  - **All required plots** comparing model predictions to observed data.
  - **A short written interpretation** of model performance.

Good luck, and happy modeling!