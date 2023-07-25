# Coverage-Based Testing

## Learning outcomes of this chapter

At the end of this chapter, you should be able to:

- Select test inputs for a program based on its control-flow graph, using the various control-flow coverage criteria.

- Identify when a variable in a program is defined, referenced, and undefined.

- Select test inputs from a control-flow graph annotated with variable information, using the various data-flow coverage criteria.

- Identify static data-flow anomalies from the variable information in a control-flow graph.

- Compare and contrast the different control-flow and data-flow coverage criteria.

- Describe the process of mutation analysis and apply it to a small-scale program.

- Argue why you think mutation analysis is useful or not.

- Define equivalent mutants, and their impact on mutation analysis.

- Describe the importance of and motivate the use of coverage metrics in testing.

## Chapter introduction

In this chapter, we discuss three software testing techniques that are based on achieving *coverage* of the software being tested. The notion behind of these techniques is to achieve some form of coverage of the , based on well-defined criteria, rather than partitioning the input domain of the program. While these techniques were originally designed as techniques to *generate* test inputs, in contemporary software engineering, they are more useful as *measures* of test suite quality. That is, we generate a test suite using some technique, such as input partitioning, and we use tools to automatically measure the coverage of the test suite; making improvements when we find parts of the program that should be covered, but are not.

The usual way of looking at the structure of the code is to look at the possible sequences of statements that can be executed. If the program contains an if statement then there are two possible sequence of statements: one in which the if condition evaluates to true; and one in which it evaluates to false. If the program contains a while loop then the actual sequences of statements that get executed will depend on the loop. In some cases it may execute exactly $n$ times for any execution, in others it may vary, while in some it may execute indefinitely. Thus, the test inputs are selected to execute each statement, branch, or path in the program itself, and are therefore *white-box* testing techniques.

There are three techniques that we discuss in this chapter:

1. **Control-flow testing** -- control-flow strategies select test inputs to *exercise* paths in the control-flow graph. They select paths based on the control information in the graph, typically given by predicates in if statements and while loops.

    Test inputs are selected to meet criteria for *covering* the graph (and therefore the code) with test cases in various ways. Examples of coverage criteria include:

    - Path coverage;
    - Branch coverage;
    - Condition coverage; and
    - Statement coverage.


2.  **Data-flow testing** --- data-flow strategies select inputs to exercise paths based on the flow of data between variables; for example, between the *definition* of a variable, such as assigning a value to a variable, and the *use* of that variable in the program

    As with control-flow strategies, test inputs are selected to cover the graph (and therefore the code) with test cases. Examples of coverage criteria include:

     - Execute every definition;
     - Execute every use;
     - Execute every path between every definition and every use.

3.  **Mutation analysis** --- mutation analysis is a technique for measuring the effectiveness of test suites, with the side effect that test cases are created and added to that test suite. The technique is based on seeding faults in a program, and then assessing whether or not that fault is detected by the test suite. If not, then that test suite is *inadequate* because the fault was not detected, and a new test case must be added to find that fault.

    Using specially designed operators, many copies of the program are created, each one with a fault, with the aim of the test suite killing all of these.


```{admonition} Remark
Note that these techniques are useful only for selecting test *inputs*; not test *cases*. That is, they are not useful for selecting test outputs. If both the test inputs and expected outputs are derived from the program itself, then we will never produce failures because the expected outputs and actual outputs will always be the same. As such, a white-box testing technique still requires a [test_oracles](test oracle), and therefore, a specification of the program behaviour.
```

## Control-Flow Testing

In this section, we discuss *control-flow testing* techniques.

### Control-Flow Graphs

The most common form of white-box testing is control-flow testing, which aims to understand the *flow of control* within a program. There are a number of ways of capturing the flow of control in a program, but by far the most common is by using a *control-flow graph* (CFG).

Informally, a CFG is a graphical representation of the control structure of a program and the possible *paths* along which the program may execute. More formally we have the following definition.

```{admonition} Definition
A *control-flow graph* is a graph $\rm G = (V,E )$ where:
- A vertex in the CFG represents a program statement;
- An edge in the CFG represents the ability of a program to flow from its current statement to the statement at the other end of the edge;
- If an edge is associated with a conditional statement, label the edge with the conditionals value, either true or false.
```

In diagrams, statements are usually represented by square boxes and branches by diamond boxes.


(f_4_1)=
```c
int power(int base, int n)
{
    int power, i;
    power = 1;
    for (i = 1; i <= n; i ++) {
        power = base * power;
    }
    return power;
}
```
<p style="text-align: center;">Figure 4.1: A C function to iteratively raise a base to a power.</p>

As a first example consider the power function in Figure [4.1](f_4_1), which for integer inputs base and n, calculates ${ base^n}$. All looping constructs decompose into *test-and-branch* control flow structures. The for loop in Figure [4.1](f_4_1) needs to be decomposed into a test i `<`= n and a branch. The control graph for the power function appears in Figure [4.2](f_4_2).


(f_4_2)=
```{figure} figures/CFG-Example-1.png
---
align: center
width: 45%
---
```

<p style="text-align: center;">Figure 4.2: The control-flow graph for the function in Figure 4.1</p>

We make two observations about the control-flow graph before continuing.


```{admonition} Remark
First, note that we have divided the CFG into a number of *basic blocks*. Each basic block is a sequence of statements with no branches and only a single entry point and a single exit point and so, only a single path through the block. The use of basic blocks makes large CFGs easier to visualise and analyse.

Second, we have labelled all of the nodes in the graph with letters. In general, since paths are sequences of nodes in the control-flow graph we will label nodes and not edges in the graph.
```

We introduce following definitions for our analysis for control flow graphs.

```{admonition} Definition

- An *execution path*, or just a *path*, is a sequence of nodes in the control flow graph that starts at the entry node and ends at the exit node.

- A *branch*, or *decision*, is a point in the program where the flow of control can diverge. For example, if-then-else statements and switch statements cause branches in the control flow graph.

- A *condition* is a simple *atomic* predicate or simple relational expression occurring within a branch. Conditions *do not* contain *and* (in C &&), *or* (in C ||) and *not* (in C !) operators.

- A *feasible path* is a path where there is at least one input in the input domain that can force the program to execute the path. Otherwise the path is an *infeasible path* and no test case can force the program to execute that path.
```


In terms of the control-flow graph, a branch is a node in the graph with two or more edges that leave that node:

```{figure} figures/branch.png
---
align: center
width: 35%
---
```

In turn branches are made up from conditions. For example, the branch given by `if (a > 1 and b == 0)` consists of the conjunction of two conditions: (1) `a > 1`; and (2) `b == 0`. Analysing the branches and conditions in a program gives us a great deal of insight into how to choose test cases to follow specific paths. For example, we need to select test inputs to make a branch true and false and in turn this means choosing values for a and b to make the branch take on the vales true and false.

In this example, there is one way to make the branch true: 

- `a > 1 and b == 0` 

and three ways to make the branch false:

- `a > 1 and b != 0`
- `a =< 1 and b == 0`
- `a =< 1 and b != 0`.

### Coverage-Based Criteria

The aim of coverage-based testing methods is to *cover* the program with test cases that satisfy some fixed coverage criteria. Put another way, we choose test cases to exercise as much of the program as possible according to some criteria. If some part of the program is not exercised by any test case then there may well be undiscovered faults lurking there.

Coverage-based testing for control-flow graphs works by choosing test cases according to well defined *coverage* criteria. The more common coverage criteria are the following.


- **Statement coverage** (or **node coverage**):  Every statement of the program should be exercised at least once.

- **Branch coverage** (or **decision coverage**): Every possible alternative in a branch (or decision) of the program should be exercised at least once. For if statements this means that the branch must be made to take on the values true and false, *even if the result of this branch does nothing*, for example, for an if statement with no else, the false case must still be executed.

- **Condition coverage**: Each condition in a branch is made to evaluate to true and false at least once. For example, in the branch `if (a and b)`, where `a` and `b` are both conditions, then we must execute a set of tests such as:
	- `a`  evaluates to true  at least once and false at least once; and 
	- `b`  evaluates to true at least once and false at least once.

- **Decision/Condition coverage**:  Each condition in a branch is made to evaluate to both true and false and each branch is made to evaluate to both true and false. That is, a combination of branch coverage and condition coverage. For example, in the branch `if a and b`, where `a` and `b` are both conditions, we must execute a set of tests such that:
	- `a`  evaluates to true  at least once and false at least once; 
	- `b`  evaluates to true at least once and false at least once; and
	- `a and b` evaluates to true at least once and false at least once.

  If `a` and `b` are both Boolean variables, this can be achieved using the following test inputs:

| a | b | a and b |
|---------|---------|----------|
| true     | true   | true    |
| false  | false  | false   |


- **Multiple-condition coverage**: 
  All possible combinations of condition outcomes within each branch should be exercised at least once. For example, in the branch `if (a and b)`, where `a` and `b` are both conditions, we must execute a set of tests such that all four combinations of `a` and `b` are true and false respectively.  That is, we must execute the following four conditions:

  | a | b | a and b |
  |---------|---------|----------|
  | true     | true   | true    |
  | false  | true   | false   |
  | true   | false  | false   |
  | false  | false  | false   |

  For this, we have  $2^n$ number of objectives to satisfy, where $n$ is the number of conditions. 

- **Path coverage**: 
  Every execution *path* of the program should be exercised at least once.

  **Note** that path coverage is impossible for graphs containing loops, because there are an infinite number of paths. A typical work around is to apply the *zero-to-many* rule, discussed in the [Domain Testing](domain_testing) section, which states that the minimum number of times a loop can execute is zero, so this is a boundary condition. Similarly, some loops have an upper bound, so treat this as a boundary. 

  A general guideline for loops is to select test inputs that execute the loop:

  - zero times -- so that we can test paths that do not execute the loop;

  - once -- to test that the loop can be entered and that the results for a single iteration are "correct";

  - twice -- to test that the results remain "correct" between different iterations of the loop;

  - N times (greater than 2) -- to test that an arbitrary number of iterations returns the "correct" results; and

  - N+1 times to test that after an arbitrary number of iterations the results remain "correct" between iterations.

  In the case that we know the upper bound of the loop, then set N+1 to be this upper bound.

(example_28)=
````{admonition} Example 28: 
:class: tip

To motivate the selection of test cases consider the following simple program in Figure [4.3](f_4_3).

(f_4_3)=
```c
void main(void)
{
    int a, b, c;
    scanf("%d %d %d", a, b, c);

    if ((a > 1) && (b == 0)) {
        c = c / a;
    }

    if ((a == 2) || (c > 1)) {
        c = c + 1;
    }

    while (a >= 2) {
        a = a - 2;
    }

    printf("%d %d %d", a, b, c);
}
```
<p style="text-align: center;">Figure 4.3: A simple program for control-flow testing.</p>


The first step in the analysis is to generate the control-flow graph which we show in Figure [4.4](f_4_4).


(f_4_4)=
```{figure} figures/cbtgraph-A.png
---
align: center
width: 50%
---
```

<p style="text-align: center;">Figure 4.4: The Control Flow Graph for the program in Figure 4.3</p>

Now, what is needed for statement coverage? If all of the branches are true at least once then we will have executed every statement in the graph. Put another way to execute every statement at least once we must execute the path ABCDEFGF.

Now, looking at the *conditions* inside each of the three *branches* we can derive a set of constraints on the values of `a`, `b` and `c` such that `a > 1` and `b == 0` in order to make the first branch true, `a =<  2` in order to make the third branch true, and `c > 1` to make the second branch true. A test case of the form `(a, b, c) = (2, 0, 3)` will execute all of the statements in the program.

Note that we have not needed to make every branch take on both values; nor have we made every condition evaluate to true and false; and nor have we traversed every path in the program.

We already have a test input `(2, 0, 3)` that will make all of the branches true. To meet the branch coverage condition all we need are test inputs that will make each branch false. Again looking at the conditions inside the branches, the test input `(1, 1, 1)` will make all of the branches false. So our two test inputs `(2, 0, 3)` and `(1, 1, 1)` is a test suite that will meet the branch coverage criteria.

For any of the criteria involving condition coverage we need to look at each of the five conditions in the program: 
- $C_1$: `a > 1`
- $C_2$: `b == 0`
- $C_3$: `a == 2`
- $C_4$: `c > 1`
- $C_5$: `a >= 2`. 
 
The test input `(1, 0, 3)` will make $C_1$ false, $C_2$ true, $C_3$ false, $C_4$ true and $ C_5$ false. Examples of sets of test inputs and the criteria that they meet are given in Table [4.1](t_4_1).


(t_4_1)=
| Coverage Criteria  |  Test Inputs |  Path Execution |
|:-----------| :-------------| :-----------|
|Statement  | (2, 0, 3)    | ABCDEFGF |
||||
|Branch     | (2, 0, 3)   | ABCDEFGF |
|           | (1, 1, 1)    | ABDF |
||||
|Condition  | (1, 0, 3)   | ABDEF |
|           | (2, 1, 1)    | ABDFGF |
||||
|Decision/Condition  | (2, 0, 4)   | ABCDEFGF |
|           | (1, 1, 1)    | ABDF |
||||
|Multiple condition   | (2, 0, 4)   | ABCDEFGF|
|           | (2, 1, 1)   | ABDEFGF|
|           | (1, 0, 2)   | ABDEF|
|           | (1, 1, 1)    | ABDF|
||||
|Path       | (2, 0, 4)   | ABCDEFGF |
|           | (2, 1, 1)   | ABDFGF|
|           | (1, 0, 2)   | ABDEF|
|           | (4, 0, 0)   | ABCDFGFGF|
|           | ...          | ...|

<p style="text-align: center;">Table 4.1: Test inputs for the various coverage criteria for the program in Figure 4.3</p>


The set of test cases meeting the multiple condition criteria is given in Table [4.2](t_4_2). In the table, we define the branches using the following labels:
- $B_1$:  `C_1 && C_2`
- $B_2$:  `C_3 || C_4`

(t_4_2)=
| Test inputs |  $C_1$ |  $C_2$ |   $B_1$   | $C_3$ | $C_4$ |   $B_2$    | $C_5$   |
|:----------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:---------------:|
|    | `a > 1`  | `b == 0`     |             | `a == 2`     | `c > 1`  |             | `a >= 2` |
| (1, 0, 3)  | F           | T           | F           | F           | T              | T           | F             |
| (2, 1, 1)  | T           | F           | F           | T           | F              | T           | T             |
| (2, 0, 4)  | T           | T           | T           | T           | T              | T           | T             |
| (1, 1, 1)  | F           | F           | F           | F           | F              | F           | F             |


<p style="text-align: center;">Table 4.2: Multiple condition coverage for the program in Figure 4.3</p>

````

```{admonition} Exercise 1
- Can you find an *infeasible path* in this example?

**Reminder**: A path is said to be *infeasible* if there are no test cases in the input domain that can execute the path.

- How many execution paths are there in the program?
```

(example_29)=
````{admonition} Example 29: **A Second Example**
:class: tip

For the second example, consider the function squeeze from Section [](programs) (shown again in Figure [4.5](f_4_5)).


(f_4_5)=
```c
void squeeze(char s[], int c)
{
    int i,j;
    for (i = j = 0; s[i] != '\0'; i++) {
    	if (s[i] != c) {
    		s[j++] = s[i];
    	}
    }
    s[j] = '\0';
}
```
<p style="text-align: center;">Figure 4.5: The squeeze function from Kernighan and Ritchie revisited.</p>

Our analysis begins with the standard analysis of input/output domains and then constructs a control-flow graph for the function. The input domain for the function squeeze is ${ char[] \times int}$ and the output domain is $ char[]$. The control-flow graph is shown in Figure [4.6](f_4_6).

(f_4_6)=
```{figure} figures/squeeze-CFG.png
---
align: center
width: 70%
---
```

<p style="text-align: center;">Figure 4.6: The control-flow graph for the squeeze function.</p>

The next step is to decide which of the coverage criteria will be used to select test cases. Typically, the exact criteria (statement, branch, condition, multiple condition, path) will depend on the project and what testing is meant to achieve.

For this example we will begin by determining the branches and conditions. Fortunately there are only two branches each of which contains only a single condition. Consequently, if we choose test inputs so that each condition evaluates to both true and false then we will also have achieved branch coverage.

Notice that the paths divide the input domain into subsets where each element of a subsets selects a specific path. For the squeeze functions the subsets can be characterised as follows.

| Path                    | Equivalence classes to select the path                                    |
|-------------------------------|-----------------------------------------------------------------------------|
| ${ABCG}$                    | Selected by the test input ("", $c$) for any character $c$. |
| ${ABCDFCG}$                 | Selected by any test input ({S}, $c$) where $S$ is a string of length 1, and $c$ occurs in $S$.                 |
| ${ABCDEFCG}$                | Selected by any test input ($S$, $c$) where $S$ is a string of length 1 and $c$ does not occur in $S$.                 |
| $\vdots$                        |                                                                             |
| ${ABCDFCDF\ldots CDFCG}$    | Selected by any test input ($S$, $c$) where $S$ is a string of length $> 1$ and $c$ does not occur in $S$.                |
| ${ABCDEFCDEF\ldots CDEFCG}$ | Selected by any test input ($S$, ${ c}$) where $S$ s a string of length $> 1$ and $c$ occurs in $S$.                 |

Next we need to derive the test cases. To do this there are two further questions that we need to answer.

- How many times do we need to execute the for loop?

- How do we determine the expected results for a test case?

If path coverage were to be demanded, then the coverage criteria could not be satisfied because there are an infinite number of paths. However, we can apply the guideline regarding loops and select test inputs that execute the loop 0, 1, 2, N, and N+1 times. In the squeeze example, the control-flow graph has two loops. Both start and end at node C, however, only one of them contains the node E. Therefore, the zero-to-many rule is applied to each of these. This gives us the paths $ABC(DFC)^nG$ and $ABC(DEFC)^nG$, for $n \in \{0, 1, 2, 4, 5\}$.

This example shows the weakness with the zero-to-many rule (and in path coverage with loops). The paths generated for this only execute the case in which the character c is in every position of the array, and the case in which it is in none. Instead of following this strictly, we use our intuition to combine the two cases, and interleave the loop paths; therefore, in some iterations the test case executes the statement at node E, and in others, it does not.

Now that the test inputs have been derived, the expected outputs must be determined. In our case we will use the design-level specification for the squeeze function to manually determine the expected outputs.

Table [4.3](t_4_3) outlines the test cases, and the paths that they exercise, for the squeeze program.

(t_4_3)=
| Coverage Criteria |  Test Input             | Expected Output |  Path        |
|-------------------------|------------------------------|-----------------------|-------------------|
| Statement Coverage      | `("c", 'c')`     | `""`            | ABCDEFCG          |
| Branch Coverage         | `("", 'c')`      | `""`            | ABCG              |
|                         | `("ab", 'b')`    | `"a"`           | ABCDFCDEFCG       |
| Path Coverage           | `("", 'c')`      | `""`            | ABCG              |
|                         | `("c", 'c')`     | `""`            | ABCDEFCG          |
|                         | `("ab", 'b')`    | `"a"`           | ABCDFCDEFCG       |
|                         | `("abcd", 'd')`  | `"abc"`         | ABC(DFC)$^3$DEFCG |
|                         | `("abcde", 'e')` | `"abcd"`       | ABC(DFC)$^4$DEFCG |

<p style="text-align: center;">Table 4.3: Test Cases for the squeeze function.</p>


````

### Measuring Coverage

Clearly, applying control-flow testing manually is a time-consuming process that is unlikely to give many benefits. However, there are aspects of the process that are easily automatable due to their mechanical nature. Deriving the control-flow graph can be automated by simply looking at the source code. Deriving test inputs from is considerably more difficult, especially for programs with non-linear domains, and for non-primitive data types.

However, one straightforward and valuable task is to measure the level of coverage of a given test suite, which can be automated easily with tools. This can be measured by executing the test suite over the program, seeing which coverage objectives are met, and providing both high-level and fine-grained feedback on the coverage.

A *coverage score* is defined as the number of test objects met divided by the number of total test objectives. The test objectives measured are relative to the particular criterion. For example, for statement coverage, the number of objectives is the number of statements, and the number of objectives met is the number of statements executed by *at least one test*. For branch coverage, the number of objects is the number of branches $\times$ two (each branch must be executed for both the true and false case).

As an example, consider the squeeze function from Figure [4.6](f_4_6). If we want to achieve branch coverage, there are four test objectives: two branches, each with two possible outputs. Now, consider the following test suite, consisting of two tests, which could be generated using any arbitrary technique: `(s = "abc", c = "d"),   (s = "", c = "d")`.

We can measure the coverage of this as follows, in which e.g. the FFFT in the first row indicates that branch $ C$ was executed *true* on the first three iterations of the loop, and *false* on the final iteration:

| Inputs                            | `s[i] != '\0'` | `s[i] != c` |
|-----------------------------------|---------------------|------------------|
| `(s = "abc", c = "d")` | TTTF                | FFF              |
| `(s = "", c = "d")`    | TTTF                | FFF              |


Scanning down the columns, we can see that the branch `s[i] != '0'` is executed for both true and false at least once, while the branch `s[i] != c` is executed only for the false case. The coverage score is calculated as:


$$\begin{array}{lll}
  &   &   \frac{objectives~met}{total~objectives} \\[1mm]
  & = & \frac{3}{4} \\[1mm]
  & = & 75\%
\end{array}$$


This means that we have achieved 75% branch coverage. Further, looking at the table, we can scan down the columns to see *which* coverage objectives were not met (the true case for `s[i] != c`), and can add a new test input to cover this case.

Thus, from a practical perspective, the idea is to generate a test suite using some method (generally a black-box method), and to then measure the coverage of that test suite. Test objectives that are not covered can then be added using the technique described in this section. Such an approach is cheaper than deriving tests from the control-flow graph directly, due to the fact that a reasonable set of black-box tests will generally achieve a high coverage initially.

#### Tool Support

There are many control-flow coverage tools available for most languages, although many of these are restricted only the statement coverage, and sometimes branch or decision/condition coverage. Some open-source tools for Java measuring Java code coverage can be found at <http://java-source.net/open-source/code-coverage>.

One excellent piece of software support is Atlassian's Clover tool (<https://www.atlassian.com/software/clover/overview>). Clover provides feedback on statement (node), branch, and method coverage, and reports fine-grained details such as which tests cover which parts of the code.

## Data-Flow Testing

It is hard to do better to motivate data-flow testing than that given by Rapps and Wuyeker:

> *It is our belief that, just as one would not feel confident about a > program without executing every statement in it as part of some test, > one should not feel confident about a program without having seen the > effect of using the value produced by each and every computation.*

Despite the analyses of input and output domains used in equivalence partitioning, boundary-value analysis, and control-flow graphs, testing still relies on personal experience to choose test cases that will uncover program faults. The problem is made harder if a programs has a large number of input variables. For example, if a program with 5 input variables and each variable's input domain is partitioned into 5 equivalence classes then there are $\rm 5^5 = 3125$ possible combinations to test.

*How can we select better test cases in order to increase the power of each test case*?

One way of improving our test cases is to find better criteria for test case selection. The other way is to find a testing a scheme that produces additional information (information other than the output of the program under test) to use for program analysis.

Data-flow analysis provides information about the creation and use of data definitions in a program. The information generated by data-flow analysis can be used to:

- detect many simple programming or logical faults in the program;

- provide testers with dependencies between the definition and use of variables;

- provide a set of criteria to complement coverage based testing.

Static analysis of programs is a large field. We will go into enough depth in these notes to give you the general idea, some techniques that you can use in practice and hopefully to extend later for specific applications.

### Static Data-Flow Analysis

In the simplest form of data-flow analysis, a programming language statement may act on a variable in 3 different ways. It may *define* a variable, *reference* a variable and *undefine* a variable:

- **Define (d)** -- A statement *defines* a variable by assigning a value to the variable. For example if the program variable `x` has been declared then the statements `x = 5` and  `scan(x)` in C both defined the variable `x`, but the statement `x = 3 * y` only defines `x` if `y` is defined.

```{admonition} Remark
Note that this is not the same as a variable being *declared*. In many languages, we can declare a variable without assigning a value to it; e.g. `int x` in C declares `x` as an integer but does not give it a value. Similarly, in many languages we can declare a value simply by defining it; e.g. `x = 5` in Python will define the value of `x`.
```

- **Reference (r)** -- A statement makes a *reference* to a variable by reading a value from the variable.

If the program variable `x` is defined then an example of `x` used as a reference is the statement `x = 5` or `scan(x)` in C, where `x` is used to store a value and must be referenced to obtain its current value.

- **Undefine (u)** -- A statement *undefines* a variable whenever the value of the variable becomes unknown. For example, the scope of a local variable ends.

The aim of data-flow analysis is to trace through the program's control-flow graph and detect *data-flow anomalies*. data-flow anomalies indicate the possibility of program faults.

```{admonition} Definition -- u-r anomaly
A *u-r anomaly* occurs when an undefined variable is referenced. Most commonly *u-r* anomalies occur when a variable is referenced without it having been assigned a value first --- that is, it is *uninitialised*. A common source of *u-r* anomalies arises when the wrong variable is referenced.

Formally, a u-r anomaly is when a variable becomes undefined (perhaps has not been defined at all) and it is referenced on *some* future definition-free path. Note that this only requires it to be referenced on at least one path. Even if the variable is defined before it is referenced, or never referenced at all, on other paths, *any* reference on a definition-free path is a potential fault, so this is flagged as an anomaly.
```

```{admonition} Definition -- d-u anomaly
A *d-u anomaly* occurs when a defined variable has not been referenced before it becomes undefined. This anomaly usually indicates that the wrong variable has been defined or undefined.

Formally, a d-u anomaly is when a defined variable is not referenced on any future path before it is undefined. Note  if there is at least one path that references the variable, there is **no** d-u anomaly.
```

```{admonition} Definition -- d-d anomaly
A *d-d anomaly* indicates that the same variable is defined twice causing a hole in the scope of the first definition of the variable. This anomaly usually occurs because of misspelling or because variables have been imported from another module.

Formally, a d-d anomaly is when a defined variable is not referenced on any future path before it is re-defined. Note that if there is at least one path that references the variable, there is **no** d-d anomaly.
```

````{admonition} Example 30: Static Data-Flow Anomalies
:class: tip

(f_4_7)=
```c++
/* Defining  p1 and p2 */

TestClass1 p1 = new TestClass1();  
TestClass2 p2 = new TestClass2();  

/* References to p1 and p2 */
p1->process();   
p2->process();   

/* Reference to p1 */
p1->process2();  

/* p2 is now  undefined */
delete p2;       

/* Reference to p2 which is now undefined */
p2->process2();  
```

<p style="text-align: center;">Figure 4.7: A program module with a u-r anomaly.</p>

As a first example consider the program fragment in Figure [4.7](f_4_7), which is written in C++. Perhaps the most obvious indication of a fault in the program is a *u-r* anomaly where we reference the variable `p2` which is undefined at the point where its value is needed. However, in large systems this kind of anomaly can be difficult to detect manually because of branching in the control-flow graph.

The block of program between the definition of `p2` and the deletion of `p2` is not harmful. However, the anomaly can indicate the premature deletion of `p2` which may effect the execution of the program in the region after the deletion of `p2`.

(f_4_8)=
```c
int Faulty_Fibonacci(int n)
{
    int i, j, sum;
    int *c;

    c = malloc(sizeof(double) * (n + 1));
    if(c == NULL) {
        FatalError(OUT_OF_SPACE);
    }

    c[0] = 1; 
    c[1] = 1; 
    sum = 2;

    for(i = 2; i <= n; i++) {
        c[i] = c[i - 1] + c[i - 2];
        sum += c[i];
        free(c);
    }
    return sum;
}
```

<p style="text-align: center;">Figure 4.8: A second example of a u-r anomaly.</p>


The second example of a *u-r* anomaly is the function Faulty_Fibonacci given in Figure [4.8](f_4_8). The control-flow graph for Faulty_Fibonacci is given in Figure [4.9](f_4_9).

(f_4_9)=
```{figure} figures/Fibonacci-CFG.png
---
align: center
width: 60%
---
```

<p style="text-align: center;">Figure 4.9: The control-flow graph for the program fragment in Figure 4.8, which contains a u-r anomaly.</p>


The fault in the Faulty_Fibonacci program is that we have freed up our memory, the array C in this case, too early. *Our data-flow analysis will only pick this up if the loop is executed two or more times*.

If we look firstly at the path ABCEFGJKL then the variable c undergoes the following sequence of transitions at each of the nodes on the path above.

| Node |  Action on the Variable c |
|------------|--------------------------------------|
| A          | no action                            |
| B          | define (d)                           |
| C          | reference (r)                        |
| E          | define (d)                           |
|            | define (d)                           |
| F          | no action                            |
| G          | no action                            |
| J          | reference (r)                        |
|            | reference (r)                        |
|            | define (d)                           |
| K          | undefine (u)                         |
| L          | no action                            |


If we execute the loop again we must now revisit nodes G and J. The next action to take on node J is a reference (r) action and so we have a u-r anomaly from the first iteration of loop. This kind of scenario is quite typical of u-r anomalies in C.

As our third example consider the following program fragment:

```
read (m);
read (n);
m = n + n;
```
In this example the program variable m is given a value by the read statement (a definition) and then the assignment (another definition), but is not referenced in between.

The *d-d* anomaly can indicate either: (1) that we have mistakenly used m to store the results of the computation n+n, or (2) we have read the wrong variable m as input. The data-flow analysis does not tell us what the fault actually is; it just tells us that there is potentially something wrong.

More generally what types of faults can data-flow analysis detect? Typically we can detect common types of programming mistakes, such as:

- typing errors

- uninitialised variables

- misspelling of names

- misplacing of statements

- incorrect parameters

- incorrect pointer references

````

### Dynamic Data-Flow Analysis with Testing

We will update our terminology to be consistent with that of Rapps and Wuyeker. The first change to note is that Rapps and Wuyeker distinguish between two different uses of a variable:


```{admonition} Definition

- A **C-use** of a variable is a *computation use* of a variable, for example, $ y~ =~ x * 2$;
- A **P-use** of a variable is a *predicate use* , for example, $ if~ (x~ <~ 2) \ldots$.
```


Secondly, we adopt the notation employed by Rapps and Wuyeker, as outlined in Definition 33 below.

```{admonition} Definition

- Let $d_n(x)$ denote a variable x that is assigned or initialised to a value at node (statement) n ( **Definition**).
- Let $u_n(x)$ denote a variable x that is used, or referenced, at node (statement) n (**Use**).
- Let $c_n(x)$ denote a computational usage of the variable x at the node n (**Computational Use**).
- Let $p_n(x)$ denote a predicate usage of the variable x at the node n (**Predicate Use**).
- Let $k_n(x)$ denote a variable x that is killed, or undefined, at a node (statement) n (**Kill**).

```

The data-flow annotations of Definition 33 are attached to nodes in the control-flow graph. The node **n** in the figure below



```{figure} figures/Weighted-Edges.png
---
align: center
width: 40%
---
```

is annotated with a definition for x. This means that the variable x at node n is defined from the node n onwards along any path containing **n**. The node **m** is annotated with a *computational* use of x, and a definition of y.

All of the defines, uses, and undefines of a single variable are collated, and are represented using a *data-flow graph*. A data-flow graph is simply control-flow graph, except that its nodes are annotated with information regarding the definition, use, and undefinition of all of the variables used in that node.

```{admonition} Exercise
Annotate the graph in Figure [4.8](f_4_8) with its definition, use, and undefinition information.
```


We define the following for data-flow graphs.


```{admonition} Definition

- A *definition clear path* $p$ with respect to a variable $ x$ is a sub-path of the control-flow graph where $ x$ is defined in the first node of the path $p$, and is not defined or killed in any of the remaining nodes in $p$.
- A *loop-free path segment* is a sub-path $p$ of the control-flow graph in which each node is visited at most once.
- A definition $d_m(x)$ reaches a use $u_n(x)$ if and only if there is a sub-path $p$ that is definition clear (with respect to x, and for which m is the head element, and n is the final element.

```

The aim is to define criteria with which to select and assess test suites. We will only look at some of the more popular of the full set of criteria discussed in the literature, and give some feel for their relative effectiveness.



In particular we will be looking at the *data-flow path selection* criteria due to Rapps and Weyuker. The work is reported in \[2\].

The aim in data-flow based testing methods is to select test cases that traverse paths from nodes that *define* variables to nodes that *use* those variables, and ultimately to nodes that *undefine* those variables.

### Coverage-Based Criteria

As with control-flow testing, the selection of test cases for data-flow testing involves achieving certain types of coverage on the graph, as defined by criteria. The most common forms of data-flow graph coverage criteria as the following.

- **All-Defs** --- For the All-Defs criterion we require that there is some definition-clear sub-path from *all definitions* of a variable to a single use of that variable. For example, consider the following data-flow graph:

    ```{figure} figures/All-Defs-Example.png
    ---
    align: center
    width: 50%
    ---
    ```

    For a test suite to satisfy that All-Defs criteria, we would need to test at least one path from the single definition of x, to at least one use. A single test case is sufficient for this. The paths 1, 2, 4, 6 or the path 1, 3, 4, 5 would be satisfactory.

- **All-Uses** --- The All-Uses criteria requires some definition-clear sub-path from *all definitions* of a variable to *all uses* reached by that definition. For example, consider the following data-flow graph:

    ```{figure} figures/All-Defs-Example.png
    ---
    align: center
    width: 50%
    ---
    ```

    The All-Uses criteria requires that we test $d_1(x)$ to each use and its successor nodes.

	1. $d_1(x)$ to $u_2(x)$;

	2. $d_1(x)$ to $u_3(x)$; and

	3. $d_1(x)$ to $u_5(x)$.

	A test suite that traverses the paths 1, 2, 4, 5, 6 and 1, 3, 4, 6 are satisfactory under this criteria.

- **All-Du-Paths** --- Here **DU** stands for *definition-use*. The All-DU-Paths criterion requires that a test set traverse *all definition-clear sub-paths* that are cycle-free or simple-cycles from *all definitions* to *all uses* reached by that definition, and every successor node of that use.

	```{figure} figures/All-Defs-Example.png
	---
	align: center
	width: 50%
	---
	```

	The All-DU-Paths criteria requires that we test $d_1(x)$ to each use.

	1. $d_1(x)$ to a $u_2(x)$;

	2. $d_1(x)$ to a $u_3(x)$;

	3. both paths from $d_1(x)$ to $u_5(x)$.

	Under this criteria, satisfactory paths are given by 1, 2, 4, 5, 6 and 1, 3, 4, 5, 6.

Recall from Definition 32, that a **P-use** of a variable is its use in a predicate, and a **C-use** of a variable is a use in a computation. Using these definitions, we can define the following additional data-flow test input selection criteria.

- **All-C-Uses, Some-P-Uses** --- The All-C-Uses, Some-P-Uses criteria requires a test set to traverse some definition-clear sub-path from each definition to each C-Use reached by that definition.

    If no C-Uses are reached by a definition, then some definition-clear sub-path from that definition to at least one P-Use reached by that definition.

- **All-P-Uses, Some-C-Uses** --- The All-P-Uses, Some-C-Uses requires that a test set to traverse some definition-clear sub-path from each definition to each P-Use reached by that definition and each successor node of the use.

    If no P-Uses are reached by a definition, then some definition-clear sub-path from that definition to at least one C-Use reached by that definition.

- **All-P-Uses** --- Some definition-clear sub-path from each definition to each P-Use reached by that definition and each successor node of the use

### Tool Support

As with control-flow testing, manually applying data-flow testing is unlikely to yield outstanding results. However, there are parts of the process that are automatable. For example, the derivation of the data-flow graph is quite easily automated, due to its mechanical nature.

Derivation of test inputs from a data-flow graph would be considerably more difficult, especially for programs with non-linear domains, and for non-primitive data types. However, as with control-flow coverage, it is significantly easier to measure that a test suite has achieved some criterion by executing the suite over the program.


````{margin}
```{admonition} Footnotes
:class: tip
Coverlipse: See <http://coverlipse.sourceforge.net/>
```
````

**Coverlipse** is an open-source application that automatically determines whether a test suite achieves all-uses coverage, and provides feedback as to the paths that are missed by the test suite.

(mutation_analysis)=
## Mutation Analysis

````{margin}
```{admonition} Footnotes
:class: tip
\[2\]: In R. DeMillo, R. Lipton, and F. Sayward, Hints on Test Data Selection: Help for the Practicing Programmer, *IEEE Computer*, 11(4):34--41, 1978.
```
````

> "*Programmers have one great advantage that is almost never exploited: > they create programs that are **close** to being correct!*" --- > Richard DeMillo [2]



Recall the squeeze function from Section [](programs), shown again below.

```c
void squeeze(char s[], int c)
{
    int i,j;
    for (i = j = 0; s[i] != '\0'; i++) {
    	if (s[i] != c) {
    		s[j++] = s[i];
    	}
    }
    s[j] = '\0';
}
```

Now consider and incorrect implementation of this function, in which the line

```
s[j++] = s[i];
```


is replaced with
```
s[j] = s[i];
```

That is, the variable j is never incremented, so the function places all references to c at position 0 in the array, and then terminates the string at position 0 (the last statement in the program). Clearly, this implementation contains a fault.

If we have a test suite for the squeeze function, and it is executed on the faulty version, one of two things can happen: 1) an error is encountered, and the programmer can debug the program to find the fault and repair it; or 2) an error is not encountered, and all test cases pass.

The latter occurs every day as part of every programmer's testing -- whether they like to admit it or not is another story. Faults in programs go undetected because the test cases do not produce failures. When a fault is subsequently found, the test suite is (hopefully!) updated to include a test case that finds this fault, the fault is repaired, and the test suite is run again. The fact that the fault went undetected in the original test suite, and that the test suite is updated, means that the initial test suite was *inadequate*: there was a fault in the program that went undetected by it.

So far in these notes, we have discussed methods that aim to find faults by achieving coverage, but which give us little idea as to how good the resulting test suite is. In this section, we present *mutation analysis*: a method for measuring the effectiveness of test suites, with the side effect that we produce new test cases to be added to that test suite.

```{admonition} Definition 35
Given a program, a *mutant* of that program is a copy of the program, but with *one* slight *syntactic* change. The term "mutant" is an analogy for a biological mutant, in which small parts of a nucleotide sequence are modified by access to ionising radiation etc.
```

The example of the fault in the squeeze function is a mutant. In this case, the slight syntactic change is the removal of the `++` operator in the array index.


```{admonition} Definition 36
Given a program, a test input for that program, and a mutant, we say that the test case *kills* the mutant if and only if the output of the program and the mutant differ for the test inputs.

- A mutant that has been killed is said to be *dead*.
- A mutant that has not been killed is said to be *alive*.
```


### The Coupling Effect

The theory behind the mutation analysis is related to the quote from DeMillo at the start of this section. Programmers do not create programs at random, but rather write programs that are close to being correct, and continue to improve them so that they are closer to being correct over time. During this process, they learn the types of faults that programmers commonly make, and this experience is valuable in software testing. Most of these faults are either incorrect control flow of a program, or an incorrect computation, but the programs are close to their expected behaviour.

```{admonition} Definition 37
The *coupling effect* states that a test case that distinguishes a small fault in a program by identifying unexpected behaviour is so sensitive that it will distinguish more complex faults; that is, complex faults in programs are *coupled* with simple faults.
```

This is perhaps nothing Earth-shattering to anyone with experience in programming. If one is to look back over their repository logs, they would likely agree that many of the failures produced by their test suites are a result of one minor fault, or are so incorrect that almost any simple test case would uncover it. However, the coupling effect does have an impact on how we select test cases, whether via mutation analysis, equivalence partitioning, or any method. It implies that we should select test cases to find simple faults, and this will result in the same test cases finding larger ones.

The coupling effect has not been proven, and in fact, can not be proven. However, empirical studies of the the types of faults that are made in programs gives significant support to the theory.

### The Coupling Effect, Mutants, and Testing

Recall the mutant of the squeeze program from the start of this section. We labelled any test suite that did not uncover this mutant as *inadequate*. But what relationship does this have to the test suites of programs that we are testing? In the squeeze example, we know where the fault is, so by not finding it, it is clear that the test suite is inadequate.

However, consider the following scenario. We are testing the original squeeze function with a test suite, and all of our test cases pass. To evaluate the quality of the test suite, we *deliberately* insert the fault into the program by removing the `++` operator in the array index, creating a *mutant* of the program, and then run the test suite again. If this produces a failure, then the fault has been uncovered, and the mutant is killed. However, if a failure is *not* produced, then we know that the test suite is inadequate, because there is at least one possible fault that it fails to uncover. If it fails to uncover this slight fault, then it would likely fail to uncover other faults. This is the process of *mutation analysis*.

In the latter case, the tester would then aim to find a test case that does kill the mutant, and add this to the test suite. Therefore, mutation analysis can be used to guide test input generation --- *we must find a test case that kills every mutant* --- as well as way of assessing test suite quality --- *a test suite that kills more mutants than another is of higher quality*. Specifically, if test suite $S$ kills all of the mutants that $T$ kills, *plus some additional mutants*, then $S$ subsumes $T$.

### Systematic Mutation Analysis via Mutant Operators

Like other approaches to testing, mutation analysis is only effective if applied systematically. This is done using *mutant operators*.

```{admonition} Definition 38
A *mutant operator* is a transformation rule that, given a program, generates a mutant for that program. 
```

(example_39)=
```{admonition} Example 39: Relational Operator Replacement rule for Java
:class: tip

The *relational operator replacement* rule takes an occurrence of a relational operator, `<`, `=<`, `>`, `>=`, `=`, or `!=`, replaces that occurrence with one of every other type of relational operator, and replaces the entire proposition in which that operator occurs with and .

Therefore, if the statement `if (x < y)` occurs in a program, the following seven mutants will be created:
- `if (x =< y)`
- `if (x > y)` 
- `if (x >= y)`
- `if (x == y)` 
- `if (x != y)`
-  `if (true)`
-  `if (false)`

This operator mimics some of the problems that programmers commonly make regarding the evaluation of Boolean expressions, which typically lead to incorrect paths.

```

(example_40)=
```{admonition} Example 39: Arithmetic Value Insertion rule for Java
:class: tip

The *Arithmetic Value Insertion* rules takes an arithmetic expression and replaces it with its application to the absolute value function, the negation of the absolute value function, and *fail-on-zero* function, in which the fail-on-zero throws an exception if the expression evaluates to zero.

Therefore, if the statement `x = 5` occurs in a program, the following three mutants will be created:
- `x = abs(5)` 
- `x = -abs(5)`
- `x = failOnZero(5)`

This operator is designed to enforce the addition of test cases that consider the case in which every arithmetic expression to evaluate to zero, a negative value, and a positive value. This mimics some of the problems that programmers commonly make, such as dividing by zero.

Mutants are *syntactic* changes to a program, so mutant operators are dependent on the syntax of programming languages. Therefore, the mutant operators of one programming language may not necessarily apply to other languages.
```

```{admonition} Remark
It is important to note that mutant operators must produce mutants that are *syntactically valid*. It is possible to create mutants of programs that are not syntactically valid, however, in most programming languages, a compiler will detect these, so they cannot be killed by a test case. One could consider that they are killed by the compiler, however, syntactically invalid mutants will always be caught by a correct implementation of a compiler, so they give us no insight into test suite quality or test input generation. Even if a language is dynamically interpreted, a good collection of mutant operators will ensure that any statically invalid programs are killed.
```

````{margin}
```{admonition} Footnotes
:class: tip
\[1\]: P. Ammann and J. Offutt, *Introduction to Software Testing*, Cambridge University Press, 2008.
```
````

In addition to the two mutant operators outlined above, Ammann and Offutt \[1\] define the following mutant operators for the Java programming language.

- *Arithmetic Operator Replacement*: Replace each occurrence of an arithmetic operator `+`, `-`, `*`, `/`, `**`, and `%` with each of the other operators, and also replace this with the left operand and right operand (for example, replace `x + y` with `x` and with `y`).

- *Conditional Operator Replacement*: Replace each occurrence of a logical operator `&&`, `||`, `&`, `|`, and `^` with each of the other operators, and also replace this entire expression with the left operand and the right operand.

- *Shift Operator Replacement*: Replace each occurrence of the shift operators `<<`, `>>`, and `>>>` with each of the other operators, and also replace the entire expression with the left operand.

- *Logical Operator Replacement*: Replace each occurrence of a bitwise logical operator `&`, `|`, and `^` with each of the other operators, and also replace the entire expression with the left and right operands.

- *Assignment Operator Replacement*: Replace each occurrence of the assignment operators `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|`=, `^=`, `<<=`, `>>=`, and `>>>=` with each of the other operators.

- *Unary Operator Insertion*: Insert each unary operator `+`, `-`, `!`, and $\sim$ before each expression of the correct type. For example, replace `if (x = 5)` with `if (!(x = 5))`.

- *Unary Operator Deletion*: Delete each occurrence of a unary operator.

- *Scalar Variable Replacement*: Replace each reference to a variable in a program by every other variable of the same type that is in the same scope.

- *Bomb Statement Replacement*: Replace every statement with a call to a special `Bomb()` method, which throws an exception. This is to enforce statement coverage, and in fact, only one call to `Bomb()` is required in every program block.

To systematically generate mutants, each of these operators is applied to every statement of a program to which it is applicable, generating a number of mutants for the program. Each of these mutants is then tested using the test suite, and the percentage of mutants killed by the test suite is noted.

```{admonition} Definition 42
The *mutation score* of a test suite for a program is the percentage of the mutants killed by that test suite, calculated by dividing the number of killed mutants by the number of total mutants:

$$
mutation~score = \frac{mutants~killed}{total~mutants}
$$
```

### Equivalent Mutants

A major problem with mutation analysis is the *equivalent mutant* problem.

```{admonition} Definition 43
Given a program and a mutation of that program, the mutant is said to be an *equivalent mutant* if, for every input, the program and the mutant produce the same output.
```


An equivalent mutant cannot be killed by any test case, because it is equivalent with the original program. As an example of an equivalent mutant, consider the squeeze function. Using the *Arithmetic Value Insertion* rule, the line

```
s[j++] = s[i];
```

can be mutated to

```
s[j++] = s[abs(i)];
```
in which abs is the absolute value function. However, the variable i only ever takes on values between $0$ and the size of the array, so abs(i) will be equivalent to i irrelevant of the test inputs that we choose. Therefore, this mutant is equivalent to the original program, and cannot be killed.

Equivalent mutants pose problems because they are impossible to kill, and difficult to detect. That is, once a test suite has been run over a collection of mutants, it is difficult to determine which mutants have not been killed due to the test suite being inadequate, or due to them being equivalent. Computing whether two programs are equivalent is undecidable, so a manual analysis needs to be undertaken for many instances, although some automated techniques exist that apply heuristics to eliminate equivalent mutants.

The equivalent mutant problem implies that, for many programs, an *adequate* test suite --- that is, one that kills every mutant --- is unachievable.

In many practical applications of mutation analysis, a *threshold* score is targeted --- for example, the tester aims to kill 95% percent of the mutants, and continues trying to kill mutants until that threshold is reached.

```{admonition} Exercise
Using the mutant operators from above, define all of the mutants for the squeeze function. Identify which of these mutants are equivalent.
```

### Tool Support

Mutation analysis is an expensive process. Firstly, it can can generate quite a large number of mutants. In fact, the number of mutants grow exponentially with the size of the program. Generating these mutants manually is not feasible for anything other than small programs. Secondly, it requires the tester to execute the test suite over every mutant, and most likely to iterate this execution a number of times.

For these reasons, a lot of work has been put into tools for automatic mutant generation, such as **MuJava**, a mutant generator for Java that uses the above rules, **Jumble**, a Java mutant generator that mutates bytecode instead of source code, and **PITest**, which also works on Java bytecode. These tools automatically generate the mutants for a program given its sourcecode or bytecode, and, given a test suite, automatically execute the mutants, and produce a report outlining the mutation score, and which mutants are still alive.

````{margin}
```{admonition} Footnotes
:class: tip

MuJava: See <http://cs.gmu.edu/~offutt/mujava/>.

Jumble: See <http://jumble.sourceforge.net/>.

PITest: See <http://pitest.org/>

```
````

Unfortunately, the equivalent mutant problem is not solved by the above tools. However, research into mutation analysis is leading to cheaper ways of automatically eliminating equivalent mutants using constraint solvers and compiler optimisation techniques. Applying these tools in practice is a long way off yet.

At this cost comes benefit: mutation testing is considered to be the most successful test coverage criterion for finding faults. In the next section, we compare the three coverage criteria presented in this chapter.

## Comparing Coverage Criteria

A question that often arises in practice is: *which criteria gives the best coverage*?

The way to compare these criteria is to defined a specific relation between the criteria. The relation in question is called *subsumption* and is defined as follows.

```{admonition} Definition
Criterion $A$ *subsume*s criterion $B$ if and only if for any control-flow graph $P$: 

$$P ~\textrm{satisfies}~ A ~\implies~ P~{\rm satisfies}~ B$$ 

Criteria A is equivalent to criteria B if and only if A subsumes B, and B subsumes A.
```

The *subsumes* relation is transitive, therefore, if $A$ subsumes $B$, and $B$ subsumes $C$, then $A$ subsumes $C$.

*How can we compare these criteria*?

Both data-flow criteria and coverage criteria select a set of paths that must be traversed by the test cases. To compare across different criteria Frankl and Weyuker have compared the paths that each criteria selects. Note, however, that the set of paths that satisfy a criterion are not necessarily unique. The results are shown in Figure [4.10](f_4_10).

(f_4_10)=
```{figure} figures/Comparisons.png
---
align: center
width: 60%
---
```

<p style="text-align: center;">Figure 4.10: Subsumption relations between sets of test cases chosen by various test case selection criteria.</p>

It is straightforward to see the relationships between the different data-flow techniques, and between the different control-flow techniques. For example, branch coverage (**All-Edges**) subsumes statement coverage (**All-Nodes**) because every statement is located within a branch.

The relationships between the different control- and data-flow criteria are less clear to see. Condition coverage does not subsume statement coverage. This can be illustrated by the example `if (a && b)`. Condition coverage mandates that a test suite must exercise a as both and , and b as both and . This can be achieved with two test cases: `(a == true, b == false)`, and `(a == false, b == true)`. However, these test cases never execute the case that `a` and `b` are *both* true, therefore, the statements in that branch are not executed.

Multiple condition coverage and the criteria that it subsumes do not subsume any of the data-flow criteria. This can be illustrated with the following simple program:
```
if (y == 1) {
    x = 2;
}

if (z == 1) {
    a = f(x)
}
```
in which `f` is some function. In this example, multiple condition coverage enforces that `y == 1` and `z == 1}` are both executed for the and cases. To achieve any of the data-flow criterion, at least one test must execute a definition-clear path from the statement `x = 2` to `a = f(x)`. However, multiple condition coverage does not enforce this, therefore, it does not subsume any of the data-flow criteria.

Similarly, none of the data-flow coverage criteria subsume any of the coverage metrics related to conditions or decisions. If we consider the weakest of these, condition coverage, the none of the data-flow criteria will evaluate the following statement to :
```
if (x == 1) {
    a := f(x)
}
```
because there is no block related to the case, and therefore no variable uses in it.

Decision/condition coverage clearly subsumes both branch and condition coverage, as it is a combination of the two. The *subsumes* relation is transitive, therefore, decision/condition coverage also subsumes statement coverage, because branch coverage does. Multiple-condition coverage subsumes decision/condition coverage, and path coverage (if possible) subsumes branch coverage.

Mutation analysis has not been discussed so far. It is difficult to evaluate because mutant operators are defined specific to programming languages. However, one can easily take the operators defined by Ammann and Offutt for Java, described earlier, and apply them to many programming languages.

These operators subsume many of the criteria in Figure [4.10](f_4_10), including multiple-condition coverage, and All-Defs coverage. This can be proved in these cases. For example, any test suite that kills all non-equivalent mutants generated by the *Bomb Statement Replacement* rule is guaranteed to achieve statement coverage (or node coverage in a control-flow graph), because every statement is replaced by a call to `Bomb()`. Similarly, any test suite that kills all non-equivalent mutants generated using the *Conditional Operator Replacement* rule is guaranteed to achieve multiple-condition coverage.

````{margin}
```{admonition} Footnotes
:class: tip
\[1\]: P. Ammann and J. Offutt, *Introduction to Software Testing*, Cambridge University Press, 2008.
```
````

In Section 5.2.2. of \[1\], Ammann and Offutt prove that mutation analysis is stronger than many of the coverage criteria in Figure [4.10](f_4_10). They hypothesise that even though some of them are not subsumed by mutation analysis, it is likely that specific mutant operators could be derived to achieve this, but that there is little benefit in doing so.

### Effectiveness of Coverage Criterion

The effectiveness of these criteria is important to consider. If we go to the effort of measuring coverage and adding tests to ensure that we achieve coverage, we'd like to know that we are adding value to our test suite.

````{margin}
```{admonition} Footnotes
:class: tip
\[3\]: L. Inozemtseva and R. Holmes, Coverage is not strongly correlated with test suite effectiveness, *Proceedings of the 36th International Conference on Software Engineering*. ACM, 2014.

\[4\]: R. Just, et al., Are mutants a valid substitute for real faults in software testing?, *Proceedings of the 22nd ACM SIGSOFT International Symposium on Foundations of Software Engineering*. ACM, 2014.

```
````


Several studies have looked into the effectiveness of test coverage criteria. For example, a 2014 study \[3\] looked into the correlation between coverage and fault-finding ability, considering statement coverage, branch coverage, and modified-condition coverage using 31,000 test suites, and found that there is a low correlation between test suite coverage and fault-finding effectiveness. Further, they found that stronger forms of coverage provide very little value. Another 2014 study \[4\] showed that mutation score provides is much better correlated with fault-finding ability, which is unsurprising because the mutation score is high if a test suite finds seeded faults.

These studies confirmed a long-held view in software engineering that structural coverage criteria, such as control- and data-flow criteria, should be used to identify areas of the program that remain untested, and not as measure of the quality of the test suite.

In short, structural test coverage is a *necessary but not sufficient* method for software testing.
