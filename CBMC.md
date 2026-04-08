# SRDM: Statistical Retest Decision Method


## Method


### Data
$t$-th test Result of each product:

$z_t = (z_t^1, \ldots, z_t^{N_t}) \in \lbrace 0, 1 \rbrace^{N_t}$
- Total Fails: $F_t = \sum_{n=1}^{N_t} z^n_t \quad (\leq N_t)$
- Total Inputs: $N_t \quad (\leq F_{t-1})$ 
- $R_t := N_t / F_t$


### Modeling: SBMC (**S**urvival **B**inary **M**arkov **C**hain)

#### Bernoulli-Initialized State
Prime test result ($Z_1 \in \{0, 1\}$) of individual product :

$$ \begin{equation}
Z_1 = X_1 \lor Y, \text{ where } X_1 \sim Ber(p), Y \sim Ber(q)
\end{equation} $$


#### Markov Transition
Retest result ($Z_t \in \{0, 1\}$ for $t > 1$) :

$$ \begin{equation}
Z_t = \begin{cases}
    X_t \in \{0, 1\} & \text{if } Z_{t-1} = 1 \\
    \text{Undefined} & \text{if } Z_{t-1} = 0
\end{cases} \quad \text{ with } \quad P[X_t \neq X_{t-1} | X_{t-1}] = c
\end{equation} $$


#### Probability of data observation

$$ \begin{align}
P[Z_1 = 1] &= P[X = 1 \text{ or } Y = 1] \nonumber \\
&= P[X = 1] + P[Y = 1] - P[X = 1, Y = 1] \nonumber \\
&= p + q - pq
\end{align} $$

$$ \begin{align}
P[Z_2=1 | Z_1=1] &= P[X_2=1 | Z_1=1] \nonumber \\
&= P[X_2=1, Z_1=1] / P[Z_1=1] \nonumber \\
&= \cdots \nonumber \\
&= \dfrac{p + c\{(p + q - pq) - 2p\}}{p + q - pq}
\end{align} $$

$$ \begin{align}
P[Z_3=1 | Z_2=1] &= P[X_3=1 | X_2=1] \nonumber \\
&= 1-c
\end{align} $$

$$
\cdots
$$


### Learning (Parameter Estimation)
Since sample proportion is the MLE (Maximum Likelihood Estimator) of population proportion, denoting $\hat{\cdot}$ as the MLE of $\cdot$,

$$
\begin{cases}
    \widehat{P(Z_1 = 1)} = R_1 \\
    \widehat{P(Z_2 = 1 | Z_1 = 1)} = R_2 \\
    \widehat{P(Z_3 = 1 | Z_2 = 1)} = R_3 \\
\end{cases}
$$


Using invariance property of MLE, which states that $\widehat{g(\theta)} = g(\hat{\theta})$,
$$ \begin{equation}
\begin{cases}
    \hat{p} + \hat{q} - \hat{p} \hat{q} = R_1 \\
    \dfrac{\hat{p} + \hat{c}(R_1 - 2\hat{p})}{R_1} = R_2 \\
    1 - \hat{c} = R_3
\end{cases}
\end{equation} $$

which leads to the following solution:
- $\hat{p} = \dfrac{S_1 S_2}{N_1 N_2}$
- $\hat{q} = \dfrac{S_1 N_2 - S_1 S_2}{N_1 N_2 - S_1 S_2}$


$$ \begin{equation}
Z_t = \begin{cases}
    X_t \in \{0, 1\} & \text{if } Z_{t-1} = 1 \\
    \text{Undefined} & \text{if } Z_{t-1} = 0
\end{cases} \quad \text{ with } \quad P[X_t \neq X_{t-1} | X_{t-1}] = c
\end{equation} $$


## Problem
$$
P[X_{T+1} = 1 | Z_T = 0] = c > \epsilon \text{ ? }
$$


## Apendix: derivation


### Notation $(4)$
$$ \begin{align}
P[X_2=1, Z_1=1] =
& P[(X_1=1 \text{ or } Y=1) \text{ and } X_2=1] \nonumber \\
=
& P[Y=1, X_1=0, X_2=1] + \nonumber \\
& P[Y=1, X_1=1, X_2=1] + \nonumber \\
& P[Y=0, X_1=1, X_2=1] \nonumber \\
=
& P[Y=1] \cdot P[X_1=0] \cdot P[X_2=1 | X_1=0] + \nonumber \\
& P[Y=1] \cdot P[X_1=1] \cdot P[X_2=1 | X_1=1] + \nonumber \\
& P[Y=0] \cdot P[X_1=1] \cdot P[X_2=1 | X_1=1] \nonumber \\
=
& P[Y=1] \cdot P[X_1=0] \cdot P[X_2=1 | X_1=0] + \nonumber \\
& P[Y=1] \cdot P[X_1=1] \cdot P[X_2=1 | X_1=1] + \nonumber \\
& P[Y=0] \cdot P[X_1=1] \cdot P[X_2=1 | X_1=1] \nonumber \\
=
& q (1-p) c + q p (1-c) + (1-q) p (1-c) \quad \because (1), (4) \nonumber \\
=
& p + c(p + q - pq - 2p) \nonumber
\end{align} $$


### Notation $(6)$
$$
\widehat{P(Z_1 = 1)} = \widehat{p + q - p q} = \hat{p} + \hat{q} - \hat{p} \hat{q} = R_1
$$

$$
\widehat{P(Z_2 = 1 | Z_1 = 1)} = \dfrac{\hat{p} + c\{(\hat{p} + q - \hat{p}q) - 2\hat{p}\}}{\hat{p} + q - \hat{p}q} = \dfrac{\hat{p} + c\{(\hat{p} + q - \hat{p}q) - 2\hat{p}\}}{\hat{p} + q - \hat{p}q} = R_2
$$

$$
\begin{cases}
    \widehat{P(Z_3 = 1 | Z_2 = 1)} = 1 - \hat{c} = 1 - \hat{c} = R_3 \\
\end{cases}
$$
