# Software Reliability Theory and Practice

## Learning outcomes of this chapter

At the end of this chapter, you should be able to:

-   Explain the difference between testing for reliability and testing for functional correctness.

-   Construct a reliability block diagram from a system architecture.

-   Given reliability estimates for the sub-systems in a reliability block diagram, calculate an estimate for the overall system using reliability and failure logic.

-   Construct a Markov model from a system description.

-   Calculate the Markov state of a model after a specified number of transitions.

-   Explain reliability growth.

-   Critique the basic execution time model.

-   Given some initial failure data, use the formula of the basic time execution model to provide reliability and failure estimates.

-   Describe the process of error seeding for fault estimation.

-   Motivate the use of the four models (reliability block diagrams, Markov models, basic execution time model, error seeding model) in this chapter in engineering software and systems.

## Chapter Introduction

So far we have looked at testing methods based that are aimed at finding the faults in programs. This leaves open some big questions such as when to stop testing and what sort of assurances can you give when you do stop testing.

More on that later. For now, we will begin this chapter by taking a different view of testing; one that is more consistent with system reliability and its measurement.

We have already made the point that software is part of a much larger *system* consisting of hardware and communications as well as programs. Often one is required to build a system that is far more *reliable* than any one of its components. For example, safety standards and high integrity systems standards often demand the reliability of systems to have a failure probability in the order of $10^{-9}$(but we will talk at some length about numbers later). This means a failure every 114,000 years on average!

Similar kinds of numbers are required for many commercial systems as well, such as web-servers. For example, how reliable/available must a web-server be to handle the number of transactions the site receives? In fact, software is invading many new areas. With these areas comes a demand for reliability that must be quantifiable and often justifiable. Examples of such domains include, but are not limited to:


- **The automotive industry** --- electronics and computers now feature in many new cars and include systems such as ABS, Traction Control, Electronic Stability Control, or Engine Management Systems.

- **The avionics (aeroplanes) industry** --- where *fly-by-wire*, that is flying planes by computer is becoming common-place.

- **Process control industries** --- such as the food and beverage industry, or chemical plants, where automation is playing a larger role. Here robots and computer systems need to be operating for long lengths of time without maintenance.

- **The Internet** --- where web-sites or ISPs hosting data ware-houses and providing other services need to be operational for long periods of time in in order to provide high qualities of service.


Software reliability can be used in a number of ways in development.


```{admonition} Definition
**Evaluation of New Systems**: Software reliability can be used to evaluate systems by *testing* the system and gathering reliability measures. The measures can be useful in deciding what third party software, such as libraries, to use in a project.

**Evaluation of Development Status**: Software reliability can be used to evaluate development status during testing. This is often done by comparing current failure rates with desired failure rates. Large discrepancies may indicate the need for action.

**Monitor the Operational Profile**: Software reliability can be used to monitor the operational profile of systems and evaluate the effects of changes or new features as they they are added to the system.

**Understanding Quality Attributes for the System**: Provided that we adopt appropriate definitions of a *failure* then software reliability can be used to explore quality attributes of a project and the factors affecting it by functional testing.

```

Viewed from a certain philosophical perspective, reliability is interesting not for its theory and methods, but what it says about programs. So far, we have conducted a meticulous analysis of input/output domains, the specifications of units and components in order to derive test cases that will detect systematically detect failures.

In reliability growth models, the aim is to select tens of thousands of test inputs and see how many of them *fail*. In reliability assessment and modelling, we treat components as black boxes and perform *experiments* on them to assess their reliability.

In this chapter we will look at the following topics to gain an appreciation of the scope and methods for software reliability.

1. **Software Reliability Engineering Methods**

2. **Random Testing**

3. **Reliability Evaluation Methods**

4. **Reliability Growth Models**

The aim is for you to have a good appreciation of the ideas behind reliability, how to distinguish between system and software reliability, and to gain an appreciation of what reliability engineering can contribute to a project.

Along the way we will need to review our basic probability theory, stochastic processes and Markov models. The mathematical theory serves to justify the results that we will present but in practice we can often ignore the mathematical theory provided we understand how to interpret the results.

## What is Reliability?

Reliability is one of a number of dependability attributes of a program. Dependability is usually analysed in terms of:

The attributes of dependability: are not like the attributes of quality where dependability is broken down into a set of measurable attributes. Rather, it is a collective name for a set of attributes that have been discussed in the literature for a number of years. The attributes themselves are shown in Figure
 [8.1](f_8_1).

The means for achieving dependability: are approaches, techniques and methods, for improving the outcomes for a particular attribute. For example, *Fault Tolerance* is the means for improving reliability.

The threats to dependability: are the things act against the attribute, for example, faults actively lower system reliability.

The taxonomy for dependability attributes, the means for achieving it and the threats to it are given in Figure [8.1](f_8_1).

In the specific case of reliability, we have the following definition.

(d_52)=
````{admonition} Definition
Reliability is the probability that a system will operate without failure for a specified
time in a specified environment.
````

(f_8_1)=
```{figure} figures/Reliability-Dependability-Taxonomy.png
---
align: center
width: 50%
---
```

<p style="text-align: center;">Figure 8.1: The dependability attributes of a system (not just a program) according to Laprie.</p>

We need to look into this definition in some more detail. To see how it works, consider the two simple systems consisting of two writers and a reader communicating via a shared memory on a bus in Figure [8.2](f_8_2).


(f_8_2)=
```{figure} figures/Reliability-Single-Channel-Bus-Example.png
---
align: center
width: 70%
---
```

```{figure} figures/Reliability-Two-Channel-Bus-Example.png
---
align: center
width: 70%
---
```

<p style="text-align: center;">Figure 8.2: Two bus architectures for communicating between reader and writer.</p>

Suppose that the probability of failure of each channel of the bus is $10^{-5}$ per operational hour. For the single channel bus therefore, the probability of a bus failure, such a message becoming corrupt or the bus failing to transmit a message, is $10^{-5}$ per operational hour, or one failure every 11.4 years. If we add the second channel then the failure probability decreases to $10^{-10}$, or one failure in every 1,114,552 years, because both channels must fail at the same time for this to occur.

We have to be careful in not over interpreting these numbers. While the improvement is significant it is, after all, only a *probability*.

Note that the definition of reliability that we have used depends heavily on determining, and in practice detecting, failures.

```{admonition} Definition
A *failure* is the departure of the results of a program from requirements.
```

```{admonition} Remark
The definition of failure is extremely general and can include performance failures such as excessive response times. Also note that the definition of failure is a *dynamic concept*: we must execute the program for a failure to occur. Intuitively, the ways in which a program may fail are by:

- activating a single fault, or even a series of faults, within the program itself that results in a failure; or

- interacting with the environment in such a way that the interaction causes a failure of the program.

Such failures are typically caused by design faults.

In their book on software reliability, Musa define reliability as follows:

> "*Software reliability concerns itself with how well the software functions to meet the customer requirements. ... \[Reliability\] is the probability that the software will work without failure for a specified period of time.*"

Thus, we think of reliability as a *dynamic* concept that relates to an executing program. You can contrast Definition [52](d_52) with the more *static* concept of defect counts that try and determine quality from the software design or other statically determined artifacts.
```


### Classifying Failures

Our definition of reliability depends quite critically on the interpretation of *failures*. Examples of failures include:

- *functional failures* where the actual behaviour of the system deviates from the specified functional behaviour;

- *timing failures* where the program may deliver correct results but the program fails to meet its timing requirements;

- *safety failures* where an accident resulting in harm or injury is deemed to be a failure of the system;

In practice we will need a way of *observing* failures and logging each failure as it arises. We also need a way of determining the time at which a failure occurs, called the *failure time*, or the rate at which failures occur, called the *failure rate*. From the failure times or the failure rates we can estimate a system's reliability. We will also need assurance that the estimates are accurate with respect to the program we are measuring.

The definition also implies that we should understand the environment in which the system must operate and that the reliability of the system may, and often will, be effected if it is used in different environments.

We can discern a number of different types of failure and our program, as well as our methods for observing failures, must account for them.

**Transient Failure** -- In this case the program gives and incorrect result, but the > program continues to execute.
**Hard Failure** -- In this case the program crashes (stack overrun, heap overrun, broken thread).
**Cascaded Failure** -- In this case the program crashes and takes down other programs.
**Catastrophic Failure** -- In this case the program crashes and takes down the operating  system or the entire system; a total system failure

### Software Reliability Engineering

Software reliability engineering is our first real engineering method. The general phases and activities of SRE are set out in Table [8.1](t_8_1).


(t_8_1)=
|      Phase                    |      SRE Activities                                           |
|-------------------------------|---------------------------------------------------------------|
| Requirements                  | Determine the functional profile of the system.               |
|                               | Determine and classify important failures.                    |
|                               | Identify client reliability needs.                            |
|                               | Conduct trade-off studies.                                    |
|                               | Set reliability objectives.                                   |
| Design                        | Design and evaluate systems to meet reliability goals.        |
|                               | Allocate reliability targets to components.                   |
|                               | (Reliability Evaluation Methods here!)                        |
| Implementation                | Measure and monitor the reliability during implementation.    |
|                               | Manage fault introduction and propagation.                    |
| System and Field Testing      | Measure reliability growth (using reliability growth models). |
|                               | Track testing progress against reliability growth.            |
| Post-delivery and Maintenance | Monitor field reliability against reliability objectives.     |

<p style="text-align: center;">Table 8.1: The phases and activities for Software Reliability Engineering.</p>

We can make some general observations that apply to many software engineering methods.

- The activities of software reliability engineering span the entire software development process but the activities themselves do not constitute a development process.

- The activities themselves need to be supported by theoretical tools for the analysis steps. The analytical tools take the form of *reliability models* and evaluations methods such as *Markov models*.

- Further, design and implementation of reliable systems is supported by design principles and algorithms such as those that we take from the discipline of *fault tolerant systems*.

In the remainder of this chapter we will begin by taking a brief look at reliability block models, and then at Markov models as a model of reliability. From Markov models we proceed to the reliability growth models of Musa . In the final section of this chapter, we discuss error seeding as a means of measuring reliability.

## Random Testing

As one can derive from the name, in random testing, the test inputs are chosen at random. The main applications of random testing are reliability measurement and security testing (Chapter 9), because it gives a good statistical spread of the inputs to a program. Random testing has also been used with some success for testing functional correctness.

Random testing does not mean abandoning the analysis of the input and output domain and the word "random" does not mean that the test cases are chosen completely *arbitrarily*. What it means is that the test inputs are chosen with a particular distribution over the input domain that somehow reflects the actual usage of the program.

For random testing the distribution of inputs comes from an *operational profile* for the program. The operational profile of a program is the probability distribution of its input domain when the program is used in actual operation. Put another way it is the probability that an element in the input domain will be chosen if the program is actually being used.


````{margin}
```{admonition} Footnotes
:class: tip
\[4\]: R. Denney, Succeeding with use cases: working smart to deliver quality, Pearson Education India, 2005.
```
````

Figure [8.3](f_8_3) shows a use case diagram, taken from \[4\], for an order handling system. The diagram contains six base cases, three inclusions, and three extensions.

(f_8_3)=
```{figure} figures/use-case-overview-diagram.png
---
align: center
width: 80%
---
```

<p style="text-align: center;">Figure 8.3: A use case diagram for an order handling system</p>

Figure [8.4](f_8_4) shows an operational profile for the use case model from Figure [8.3](f_8_3). Clearly, this was derived with additional information not presented here, so this operational profile is for illustrative purposed only. A cell in the matrix estimates the probability that the included or extended use case is invoked for the given base case; e.g. when a local order is placed, 80% of the time, the customer details must be entered.


(f_8_4)=
```{figure} figures/operational-profile-with-matrix.png
---
align: center
width: 90%
---
```

<p style="text-align: center;">Figure 8.4: An example operational profile for the system described in Figure 8.3</p>


In these notes, we do not cover how to generate operational profiles, however, clearly there are several ways, including: looking at historical data; estimation based on personal usage; surveys; and looking at probability distributions in similar systems.

````{margin}
```{admonition} Footnotes
:class: tip
For a discussion of good random number generator, see, e.g., D.E. Knuth, The Art of Computer Programming, vol. 2: Semi-numerical Algorithms, 2nd Ed., Addison Wesley, 1981.
```
````

In random testing, test inputs are randomly generated according to the operational profile\[*\], and data, such as failure times, are recorded when a random sample of test cases are executed. The data obtained from random testing is then be used to estimate reliability (see Chapter 8). No other testing method can be used in this way to estimate reliability. However, we need to take care with operational profiles -- the distribution of inputs taken from an operational profile will effect reliability predictions.

To generate a random test input for an operational profile, *Place Local Order* would be selected with a probability of 3%, and then *Enter Customer Details* would be selected with a probability of 80% of these times (so a total probability of 2.4% of the time).

## Reliability Block Diagrams

What is clear from the reliability engineering process in Table [8.1](t_8_1) is that we need some way of predicting and evaluating the reliability of our designs. For systems there are well established modelling methods:

1. Reliability block diagrams; and

2. Markov models.

These models are *stochastic* in nature. A stochastic process is one in which there is a sequence of events in time where each event is part of a probability distribution.

### Series---Parallel Systems

Reliability block diagrams seek to decompose systems into parallel and serial blocks where each block interacts with other blocks to effect the reliability of the system as a whole.

Consider again the example of the dual channel bus in Figure [8.5](f_8_5) where a redundant pair of writers put values from the source onto the bus, and a reader that reads the values from the bus. The system fails if no message is received by the reader in the given time interval.

(f_8_5)=
```{figure} figures/Reliability-Reader-Writer.png
---
align: center
width: 50%
---
```

<p style="text-align: center;">Figure 8.5: The dual channel bus.</p>


If we consider a message starting at the source then it is passed to both writers and both writers pass the message to both channels. We might model this situation as in Figure [8.6](f_8_6)


(f_8_6)=
```{figure} figures/Reliability-Reader-Writer-Block.png
---
align: center
width: 70%
---
```

<p style="text-align: center;">Figure 8.6: A reliability block model for the readers and writers in Figure 8.5.</p>


In the model, each writer acts in serial with the bus and both

channels of the bus act in serial with the reader. Thus, to analyse this situation we must calculate the reliabilities for the *serial* and *parallel* blocks in the model.

### Reliability and Failure Logic for Serial Blocks

(f_8_7)=
```{figure} figures/Reliability-Serial-Parallel.png
---
align: center
width: 60%
---
```

<p style="text-align: center;">Figure 8.7: The serial composition of three blocks.</p>

Consider the serial system in Figure [8.7](f_8_7). For this system to function without failure at a given time $T$, each of the components $B_1$, $B_2$, and $B_3$ must function independently without failure for the time period $T$. Therefore, for the system to fail in the time $T$ either **Block 1** must not fail in time $T$, **Block 2** must not fail in time $T$, and **Block 3** must not fail in time $T$.

If the reliability of each block is independent of the other blocks then a direct application of the law of conditional probability yields 

$$P\{  B_1~{\rm functions}~ \land~ B_2~ {\rm functions}~ \land~ B_3~{\rm functions}\} = R(B_1) R(B_2) R(B_3)$$ 

where $R(B_i)$ denotes the probability of **Block i** functioning without failure for time $T$. Put another way, $R(B_i)$ is the probability that the first failure occurs *after* time $T$, or not at all. $R(B_i)$ is the reliability of **Block i**.

```{admonition} Definition
In general, for systems in series, $R(B) = \prod_{n} R(B_i)$ using reliability logic. 
```

Failure logic is the converse to reliability logic. For the system to fail in the time period $T$ only one of the components needs to fail. Thus the probability of of failure is 

$$ P\{ B_1~{\rm fails}~ \lor ~ B_2~{\rm fails}~ \lor~ B_3~{\rm fails}\}. $$

The law of additive law of probability gives us:

$$
  \begin{array}{lll}
  F(B) & = & F(B_1) + F(B_2) + F(B_3) - F(B_1) F(B_2) - F(B_1)F(B_3)  - F(B2)F(B_3) \\
       & + & F(B_1)F(B_2)F(B_3)
  \end{array}
$$

where $F(B_i)$ is the probability that the first failure in $B_i$ will occur before time $T$. This equation says that the probability of the parallel block failing is the probability of any of the blocks failing independently ($F(B_1) + F(B_2) + F(B_3)$), minus the chance that any of the blocks fail simultaneously (because one failing will mean that all fail).

```{admonition} Definition
In general, for systems in series, $F(B) = 1 - \prod_{n} (1 - F(B_i))$ using failure logic.
```

### Reliability Logic for Parallel Blocks

For parallel systems *all* blocks must fail before the system fails and only one block needs to function for the system to function. Parallel blocks, it turns out, work almost inversely to serial blocks.

For a parallel block above to fail in a time $T$ we need all of the blocks to fail within the time $T$ and so 

$$ F(B) \begin{array}[t]{l} =  P\{B_1~{\rm fails}~ \land ~ B_2~ {\rm fails} ~ \land
  ~B_3~ {\rm fails}\} =  F(B_1)  F(B_2)  F(B_3)\end{array}$$

provided that $B_1$, $B_2$ and $B_3$ fail independently.

```{admonition} Definition
In general for a parallel system to fail $F(B) = \prod_n F(B_i)$ using failure logic. 
```

For parallel blocks to be reliable for a time $T$ we have must experience no failure in time $T$ and therefore, to be reliable, a parallel block only requires that a single block is functioning. This gives us the following formula for reliability. 

$$
\begin{array}{lll}
  R(B) & = &  P\{B_1~ {\rm functions}~
    \lor ~ B_2 ~ {\rm functions} ~ \lor ~B_3 ~ {\rm functions}\}\\
     & = &   R(B_1) + R(B_2) + R(B_3) -R(B_1)R(B_2) - R(B_1)R(B_3) - R(B_2)R(B_3)\\
     & + &   R(B_1)R(B_2)R(B_3).
  \end{array}
$$ 
    
This comes from the additive law of probabilities.

```{admonition} Definition
In general for parallel blocks we have $R(B) = 1 - \prod_n ( 1 - R(B_i))$ using reliability logic.
```


The key relationships between failure logic and reliability (or success) logic lay in the equation $$\label{eq:reliability} R(B) = 1 - F(B)$$

where 

$$R(B) = P\{ {\rm First~Failure~at~time}~\tau +>+ T\}$$ 

and 

$$F(B) = P\{ {\rm First~Failure~at~time}~\tau +<+ T\}.$$

### Creating Reliability Block Diagrams from System Architectures

Here are some general guidelines for creating reliability block models from system architecture, especially if presented as a set of function block diagrams.

**Guideline 1**: Determine what constitutes a *system* failure. This in turn determines which component failures cause a system to fail.

**Guideline 2**: Determine which components need to fail in order to cause a system failure. Some guidelines for constructing reliability block models is to consider how messages, signals or data flows through the system and the consequence of what happens if these are corrupted or interrupted.

**Guideline 3**: Try to ensure that each block in the reliability block model captures one function of the system.

**Guideline 4**: Try to ensure that you capture the parallel/serial connections from the system accurately in your reliability block model.

**Guideline 5**: There may be more than one mode for the system --- you will need to create a reliability block diagram for each mode of the system. For example, Windows operating systems can operate in a *safe mode*, which is more reliable because it is limited to basic functionality.

### Back to the Reader/Writer Example

Each writer acts independently of the other writer and then communicates via the bus to the reader. So now we use the guidelines above to get the reliability block model in Figure [8.8](f_8_8).

(f_8_8)=
```{figure} figures/Reliability-Reader-Writer-Block.png
---
align: center
width: 70%
---
```

<p style="text-align: center;">Figure 8.8: The reader/writer example revisited.</p>


Suppose we measure the failure probabilities by and arrive at the estimates in Table [8.2](t_8_2).

(t_8_2)=
| Component | Failure Probability in Time \(T\)             |
|-----------------|-----------------------------------------|
| Writers         | $F(W) = 10^{-4}$                        |
| Bus Channels    | $F(B) = 10^{-2}$                        |
| Reader          | $F(R) = 10^{-4}$                        |

<p style="text-align: center;">Table 8.2: Failure times for the reader/writer in Figure 8.8.</p>

Firstly, we have only failure probabilities but we are after reliability figures. So, note that the reliability of a writer is $R(W) = 1 - F(W)$. Now, using reliability logic for parallel blocks as in Block 1 we calculate the reliability of two writers as follows. 

$$
 \begin{array}{lll}
  R(B_1)  & = &  1 - (1 - R(W))(1 - R(W))\\
          & = &  1 -(1 -(1 - F(W)) )(1 - ( 1 - F(W))) \\
          & = &  1 - F(W)^2
  \end{array}
$$

With the square deriving from the fact that there are two writers in that block, so the probability of the block failing is the product of the probabilities of the either writer failing.

For Block 2 we likewise calculate the reliability of two channels in parallel:

$$
\begin{array}{lll}
 R(B_2) & = &  1 -(1 - (1 - F(B)))(1 - (1 -  F(B)))  \\
        & = &  1 - F(B)^2
\end{array}
$$

Finally we have a system in series so to calculate the reliability of the system we need to apply reliability logic again: 

$$R(System) = R(B_1) R(B_2) R(B _3)$$ 

which gives:

$$
\begin{array}{lll}
  R(System) & = &  (1 - F(W)^2)( 1 - F(B)^2)(1 - F(R)) \\
            & = &  (1 - 10^{-8})( 1 - 10^{-4})(1 - 10^{-4})) \\
            & \approx & 0.998
\end{array}
$$

The system reliability is the probability of failure free operation over a fixed time period $T$. It does not tell us how reliability changes over time. Determining how the reliability of a system changes over time is a stochastic property.