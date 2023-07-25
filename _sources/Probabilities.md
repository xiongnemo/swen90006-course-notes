# A Brief Review of Some Probability Definitions

Probability theory is concerned with *chance*. Whenever there is an event or an activity where the outcome is uncertain, then probabilities are involved. We normally think of any activity or event with an uncertain outcome as an *experiment* whose outcome we observe. Probabilities are then measures of the likelihood of any of the possible outcomes.

An *experiment* represents an activity whose output is subject to chance (or variation). The output of the experiment is referred to as the *outcome* of the experiment. The set of all possible outcomes is called the *sample space*.

```{admonition} Definition
The *sample space* for an experiment is the set of all possible outcomes that might be observed.
```

```{admonition} Some examples of experiments and their outcomes
:class: tip
- An experiment involving the flipping of a coin has two possible outcomes Heads or Tails. The sample space is thus $\sf S = \{Heads,~Tails\}$.

- An experiment involving testing a software system and counting the number of failures experienced after T = 1 hour has many possible outcomes: we may experience no failures, 1 failure, 2 failures, 3 failures, .... The sample space is thus $\sf N = \{0, ~1, ~2, ~3, \ldots\}$.

- An experiment involving the testing of a software system and recording the time at which the first failure occurs has a real valued sample space $R$, although in practice this is more likely to be a time interval.
```

Probabilities are usually assigned to events.

```{admonition} Definition
Let $S$ be a sample space. An *event* is a subset $A$ of the sample space $S$, that is, $A\subset S$. An event is said to have *occurred* if any one of its elements is the outcome observed in an experiment.
```


The probability of an event $A$ is a non-negative real number that relates to the number of times we observe an outcome in $A$. Often this real number is just the fraction of times that we observe an outcome in $A$ over the total number of possible outcomes. We write: 

$$P\{A\} = \lim\begin{array}[c]{c} m \\\hline n \end{array}$$ 

where $m$ is the number of outcomes in $A$ and $n$ is the total number of possible outcomes, that is, the number of elements in $S$. For two events, $A$ and $B$, $A \cup B$ to refer to event $A$ occurring or event $B$ occurring, or both, and, $A \cap B$ to refer to event $A$ and event $B$ both occurring. Therefore $P\{A \cap B\}$ is the probability of events $A$ and $B$ both occurring. The *conditional probability* of an event $A$ is the probability of $A$ given that $B$ has occurred. This is written $P\{A|B\}$. In conditional probabilities $B$ is often called the *conditioning event*.

Probabilities must satisfy certain *probability laws* in order to be meaningful measures of likelihood. The following probability laws hold for any event $A$ and any state space $S$:

- $0\leq P\{A\} \leq 1$; and

- $P\{\lnot A\} = 1 - P\{A\}$

- $P(S) = 1$.

That is: (i) the probability of an event occurring is between $0$ and $1$ inclusive; (ii) the probability of $A$ not occurring is $1$ minus the probability that it will; and (iii) the sum of the probabilities of all events in the state space is $1$, and no events outside the sample space can occur.

Two events are said to be *independent* if the occurrence of one event does not depend on the other and the converse. For example, if we have two dice, the probability of one falling on the outcome $6$ is independent of the outcome of the other dice. However, if we throw both dice, but one falls on the floor out of sight, while the other shows a $3$, then the probability of their total equaling $7$ is dependent on the probability of the second dice.

If events $A$ and $B$ are *dependent*, then the following laws hold:

$P\{A \cap B\}$   =   $P\{A|B\} P\{B\}$                                                 *Multiplicative Law*


$P\{A|B\}$        =   $\begin{array}[c]{c} P\{A\cap B\} \\ \hline P\{B\} \end{array}$   *Conditional Probability*


If events $A$ and $B$ are *independent* then the following laws hold:

$P\{A \cap B\}$   =   $P\{A\} P\{B\}$   *Multiplicative Law*


$P\{A|B\}$        =   $P\{A\}$          *Conditional Probability*


The independent and dependent cases are related. For example, if $A$ and $B$ are independent, then $P\{A \cap B\}$ = $P\{A\} P\{B\}$, and therefore $P\{A|B\}$ is equal to $(P\{A\}P\{B\}) / P\{B\}$ (the first conditional probability law), which is clearly $P\{A\}$ (the second conditional probability law).

Two events are said to be *mutually exclusive* if they cannot both occur. For example, if we throw a single die, then it is not possible that both 1 and 2 will result. If events $A$ and $B$ are not mutually exclusive, then the following law holds:


$P\{A \cup B\}$   =   $P\{A\} + P\{B\} - P\{ A \cap B\}$   *Additive Law*


If events $A$ and $B$ are mutually exclusive, then the following laws hold:

$P\{A \cup B\}$   =   $P\{A\} + P\{B\}$   *Additive Law*


$P\{A \cap B\}$   =   $0$                 *Mutual Exclusion*

These laws are also related. If $A$ and $B$ are mutually exclusive, then $P\{A \cap B\}$, and therefore $P\{A\} + P\{B\} - P\{ A \cap B\}$ (the first additive law) is $P\{A\} + P\{B\} - 0$, which is equivalent to the second additive law.

## Random Variables

Now, suppose that we can represent each element of the sample space by a number. If we perform an experiment then for each element $E$ of the sample space $S$, there is a certain probability that we will observe $E$ as the outcome. A *random variable* assigns a number to each outcome in the sample space. Random variables that we will encounter in this subject are:

1. Number of failures at time $T$, which is *discrete* a integer number. The sample space is $T$ and the number that we assign to it as the random variable is the number of failures.

2. Time to first failure, which is a *continuous* real-valued number. The sample space is again $T$ and the random variable in this case is the time $\tau$ to the first failure observed.

As an example, consider an experiment in which we execute random test cases on a piece of software for a period of 1 hour and observe the number of failures.

1. The sample space that we will consider (and its not the only one possible either) is $ N = \{0, 1, 2, 3, \ldots\}$.

2. Let $X$ be a random variable that returns the number of failures experienced after 1 hour of testing (assuming we can suitably quantify failure).

3. Then we can ask questions such as what is the probability that there are fewer than $N$ failures after 1 hour -- written $P\{ X
  +<+ N\}$ -- or what is the probability that we will have no failures after 1 hour -- $P\{X\} = 0$. The random variable which we have called $X$ is the number of failures experienced in 1 hour.

## Probability Density Functions

Normally associated with each random variable is a *probability density function*, or sometimes a *probability law*, that assigns a probability to every outcome in the random variable's sample space. A random variable can be discrete or continuous.

- The sample space of a discrete random variable takes on specific values at discrete points $\{a_1,\ldots,a_k\}$.

- A continuous random variable takes on all values in the real line or an interval of the real line.

We think of the probability density function as a function $f$ that maps each value of the sample space $S$ to $[0,1]$ so that $f : S\rightarrow [0,1]$. For example, the probability density function of throwing a die is $f(o) = \frac{1}{6}$ for all outcomes $o$.


The two key properties that we require of probability density functions are as follows.

For Discrete Random Variables: we require that

- $ (f(X) \geq 0) for all (x\in S) $; and
- $ (\sum_{ x \in S}\, f(x) = 1) $.

For Continuous Random Variables: we require that

- $ (f(X) \geq 0) for all (x\in S)$; and
- $ (\int^{+\infty}_{\rm -\infty} f(x) = 1)$.

The cumulative density function for a discrete random variable $X$ is defined as 

$$F_X(T) = \sum_{X \leq T} f(X)$$

that is, a sum of all of the probabilities for outcomes less than or equal to $T$. If we wish to calculate the probability $P\{A < X \leq B \}$ then this is simply 

$$P\{A < X \leq B\} = F_X(B) - F_X(A).$$

For a continuous random variable $X$ the cumulative density function is defined as 

$$F_X(T) = P\{X \leq A\} = \int^{A}_{-\infty} f(X)$$

## Probability in Reliability Measurement

Random variables and their distributions will be important to us because many of our reliability measures are expressed in terms of random variables and their distributions. Some examples are:

1. Let $T$ be a random variable representing the time of failure of a particular system. Then the *failure probability* is expressed as a cumulative density function: 

$$F(t) = P\{ T \leq t\}.$$

2. Reliability is simply the converse -- it is the probability that a particular systems survives --- that is, does not experience a failure --- until after time t:

$$R(t) = 1 - F(t) = P\{T+>+ t\}.$$
