# Boundary-Value Analysis

## Learning outcomes of this chapter

At the end of this chapter, you should be able to:

-   Explain why boundary-value analysis is valuable compared to standard equivalence partitioning

-   Given a set of equivalence classes for a program, identify the boundaries of those equivalence classes, including the on points and off points.

-   From a set of equivalence classes, select test inputs that adequately cover the boundaries of a program.

## Chapter Introduction

The input partitioning methods presented in Chapter 2 can be improved by trying to find better test cases within equivalence classes. *Boundary-value analysis* is both a refinement of input partitioning and an extension of it. Boundary-value analysis selects test cases to explore conditions on, and around, the edges of equivalence classes obtained by input partitioning.

```{admonition} Definition
*Boundary conditions* are predicates that apply directly on, above, and beneath the boundaries of input equivalence classes and output equivalence classes.
```

Intuitively, boundary-value analysis aims to select test cases to explore the boundary conditions of a program. Boundary-value analysis and input partitioning are closely related. Both of them exploit the idea that each element in an equivalence class should execute the same paths in a program. However, boundary-value analysis works on the theory that, if a programmer makes a mistake in the logic of the program, such that some inputs in an equivalence class execute the incorrect paths, then those mistakes will often occur at the *boundary* between equivalence classes, because these boundaries are related to flow control constructs such as if statements and while loops in programs.



```{admonition} Definition
A *computational fault* is a fault that occurs during a computation in a program; for example, an arithmetic computation or a string processing error.
```

Consider the following statement to calculate the average of two numbers:

        average = (left + right) / 2

A computational fault would be:

        average = left + right / 2

In the above, the division operator binds tighter than the addition operator, so the computation is incorrect as it calculates ```average = left + (right / 2)```.


```{admonition} Definition
A *boundary shift* is when a predicate in a branch statement is incorrect, effectively 'shifting' the boundary away from its intended place.
```


For example, if our program should do something if a list has more than 10 elements, we would have:

        if (length(list) > 10) then ...
        else ...

However, a programmer could make a simple mistake of using `>=` instead of `>`:

        if (length(list) >= 10) then ...
        else ...

Here, the boundary has 'shifted' across one value from where it should be *according to the specification*.

Boundary-value analysis attempts to find these faults near the boundary by selecting test cases on and around the boundary. In the example above, after identifying the equivalence classes, we would select *at least* test cases that satisfy the condition cases: ${ length(list) = 10}$ (the top end of the first EC); and ${ length(list) > 10}$ (the bottom end of the second EC).

If we arbitrarily choose values of the equivalence classes, such as a list of length 5 and one of length 15, then our tests would fail to find the above fault.

Collected defect data supports the theory behind boundary-value analysis. Many faults are boundary shifts introduced at boundary conditions because programmers either: (1) are unsure of the correct boundary for an input condition; or (2) have incorrectly tested the boundary.

Many computational faults are also found in defect data, however, it is important to note the tests on the boundaries can detect both computational faults and boundary shifts, while those away from the boundary defect only computational faults. Therefore, test cases that explore the boundary conditions have a more important role that test cases that do not.

Boundary-value analysis requires one *or more* test cases be selected from the edge of the equivalence class or close to the edge of the equivalence class, whereas equivalence partitioning simply requires that any element in the equivalence class will do. Boundary-value analysis also requires that test cases be derived from the output conditions. This is different to equivalence partitioning where only the input domain is usually considered.

(values-and-boundaries)=
## Values and Boundaries

Before we discuss how to apply boundary-value analysis, we define some related terms.


```{admonition} Definition --- Path condition, domain, and domain boundary
A *path condition* is the condition that must be satisfied by the input data for that path to be executed.

A *domain* is the set of input data satisfying a path condition.

A *domain boundary* is the boundary of a domain. It typically corresponds to a simple predicate.
```

Test inputs are chosen to be *on* or around the boundary. However, there are two distinctly different types of boundary: *closed boundaries* and *open boundaries*.



```{admonition} Definition --- Open and closed boundaries
A *closed boundary* is a domain boundary where the points on the boundary belongs to the domain, and is defined using an operator that contains an equality. For example, ${ x \leq 10}$ and ${ y = 0}$ are both closed boundaries.

An *open boundary* is a domain boundary which is not closed, and is defined using a strict inequality. For example, ${ y < 10}$ is an open boundary, because $10$ does not fall withing the boundary.
```

Now that we have a better understanding of what a boundary is, we can define the on and off points of the boundary, which are the test inputs we will choose for boundary-value testing. The inputs that are chosen depend on the type of boundary.

```{admonition} Definition -- On and off points
An *on point* is a point on the boundary of an equivalence class. 

For example, the value $10$ is an on point for the boundary $x \leq 10$. Counter-intuitively, $10$ is also the on point for the boundary $x < 10$. Therefore, for a closed boundary, an on point will be a member of the equivalence class, and for an open boundary, it will not.

An *off point* is a point just off the boundary between equivalence classes. For example, if $x$ is an integer and a boundary is $x \leq 10$, then the off point is $11$. If $x$ is a floating point number, the off point would be $10.001$ (assuming that $0.001$ is the smallest floating point on the specific architecture).

For the open boundary $y < 10$, the off point is $9$ if $y$ is an integer, and $9.999$ is ${ y}$ is a floating point number.

The reverse of the on point then holds: for a closed boundary, the off point will fall outside of the equivalence class, and for an open boundary, it will fall inside the equivalence class.

Two special cases of off points are for strict equalities and non-equalities. 

For equalities, which are boundaries of the form $x = 10$, there is one onpoint ($10$), and there are two off points: $9$ and $11$.

Similarly, for non-equalities, which are boundaries of the form $x \neq 10$, there is one onpoint ($10$), and there are two off points: $9$ and $11$.
```

Note that the on and off points for equalities and non-equalities are the same. This may seem strange. Why would the on points for $x=y$ be the same as the on points for $x \neq y$ when they are the opposite of each other? Clearly, the input $(x=0, y=0)$ is an on point for $x=y$, but wouldn't the on point for $x \neq y$ be something like $(x=0,y=1)$?

Not quite! What we have to remember is that we are analysing *boundaries*, not equivalence classes. For the equivalence class $x \neq y$, the boundary is at $x = y$. This is the set of inputs that we can point to on the boundary. We cannot point to a boundary $x \neq y$ --- there is no boundary there. It describes all of the points that do not lie on the boundary $x=y$. So, even though the points on $x=y$ do not fall into the class $x \neq y$, those are the on points of the boundary.

## Guidelines for Boundary-Value Analysis

Given a set of domain boundaries to test, we can apply the following guidelines to select test cases.


1. If a boundary is a strict equality, for example ${ x  = 10}$ select the on point, $10$, and both off points, $9$ and $11$.

2. If a boundary is a strict non-equality, for example ${ x \neq 10}$ select the on point, $10$, and both off points, $9$ and $11$.

3. For an open boundary, for example ${ x < 10}$, select the on point $10$, and the off point $9$.

4. For a closed boundary, for example ${ x \leq 10}$, select the on point $10$, and the off point $11$.

5. If a boundary is an inequality with an open boundary, for example ${ x < 10}$, select the on point $10$, and the off point $9$.

6. For boundaries containing unordered types, that is, types such as Booleans or strings, choose one on point and one off point. An on point is any value that is in the equivalence class, while an off point is any value that is not. For example, if the equivalence class for a string variable is "contains no spaces", then test one string containing no spaces, and one string containing at least one space.

7. Analyse boundaries of equivalance classes; not individual equivalence classes. If we have the equivalences classes ${ x \leq 0}$ and ${ x > 0}$, then $0$ is the on point for both, so analyse that boundary not each class in isolation.

(remark:on-off-points)=
```{admonition} Remark
It may seem strange that the on points for the boundaries $x < 10$ and $x \leq 10$ are both 10, but the off points are 9 and 11 respectively. Shouldn't 10 be the *off point* for $x < 10$, because it is not in the equivalence class $x < 10$? 

Not quite! Remember that we analyse the boundaries *between* equivalence classes. 


If $x < 10$ is an equivalence class, then there must be another equivalence class on the other side of $10$; for example, $x \geq 10$. The boundary at $x < 10$ is the same as the boundary at $x \geq 10$. Clearly, $10$ is the on point for the boundary $x \geq 10$. So, it must also be the on point for $x < 10$. Whether $10$ is inside the class $x < 10$ is irrelevant -- each on point falls inside one equivalence class on the boundary, and not in any other equivalence class on that boundary.

However, the *off points* for $x < 10$ and $x\leq 10$ are different! For $x< 10$ the off point is $9$, while for $x \leq 10$, it is $11$. Confused? Think of it like this: the on point $10$ is NOT in the equivalence class $x<10$. We need to choose a value on each equivalence class either side of the boundary, which means we choose on inside $x < 10$, which is $9$ if $x$ is an integer, and $9.9^*$ is $x$ is a real number.

But for $x \leq 10$, the value $10$ is inside the equivalence class $x \leq 10$, so we need to choose a value on the other side of the boundary, which is $11$ if $x$ is an integer, and $10.001$ (or smaller) if $x$ is a real number.
```

```{admonition} Remark
It is not always easy to see the boundaries between equivalence classes; boundaries may not exist in such simple forms or make sense. When they do, they give better test cases than equivalence partitioning.
```

(example_23)=
```{admonition} Example 23: Boundary-Value Analysis for the Triangle Program
:class: tip

Consider again the triangle classification program from Example [14](example_14). To show the boundary-value analysis method more clearly we will change the specification slightly to input floating point numbers instead of integers.

> The program reads floating point values from the standard input. The three values are interpreted as representing the lengths of the sides of a triangle. The program then prints a message to the standard output that states whether the triangle, if it can be formed, is equilateral, isosceles, scalene, or invalid.

Given the equivalence classes identified above using equivalence partitioning we now have the following possible boundary conditions. Let x, y and z be the three floating point numbers input to the program.

- For an *equilateral* triangle the sides must all be of equal length and we have only one boundary where $x = y \land x = z$ and we can explore this boundary using the following test cases:

  | <!-- -->  |   |
  |---|---|
  | $(3, ~3, ~3)$      | (on point)              |
  | $(2.999,~ 3,~ 3)$  | (off point below for x) |
  | $(3.001,~ 3,~ 3)$  | (off point above for x) | 

  Plus the five additional cases that correspond to $x = y \neq z$ (one above, one below), $x \neq z=y$ (one above, one below) and $x \neq y \neq z$.

- For an *isosceles* triangle two sides must be equal. This gives the boundary conditions $x = y \land y \neq z$, $y = z \land z \neq x$ or $x = z \land z \neq y$. The boundary $x = y \land y \neq z$ can be explored using the following test cases:

  | <!-- --> |   |
  |---|---|
  |  $(3, ~3, ~4)$     |  (on point)
  |  $(2.999,~ 3,~ 4)$ |  (off point below)
  |  $(3.001,~ 3,~ 4)$ |  (off point above)


  Plus four additional cases for the off points of y and z.

- For a *scalene* triangle, all sides must be of a different length. This equivalence class is $x \neq y \land y \neq z \land z \neq x$. This can be explored using the following test cases:

  | <!-- -->  |   |
  |---|---|
  |$(3, ~3, ~3)$     |   (on point) |
  |$(2.999, ~3, ~3)$  |  (off point below) |
  |$(3.001, ~3, ~ 3)$ |  (off point above) |


  Plus four additional cases for the off points of y and z. However, note that all nine of these values have already been tested. The on and off points for this are the same as for the equilateral triangle.
  
  Why is $(3, 3, 3)$ the on point when the equivalence class is $x \neq y \land y \neq z \land z \neq x$. Shouldn't this be the off point? 
  
  No! Recall the discussion from the [remark about on and off points](remark:on-off-points). The boundary between equivalence classes falls at $x=y=z$, because $x=y=z$ is a neighbouring equivalence class. The point $(3,3,3)$ just happens to be outside of the equivalence class is $x \neq y \land y \neq z \land z \neq x$. 

- For invalid triangles, we have the cases in which the sides are of size $0$ or below; that is $x \leq 0$. The following test cases explore this for x:

  | <!-- -->  |   |
  |---|---|
  |$(0, ~3, ~3)$   |    (on point)|
  |$(0.001, ~3, ~3)$  | (off point)|


  and similarly for y and z.

  For invalid triangles, we also have the cases in which one of the sides is at least as long as the sum of the other two. The following test cases explore the equivalence class ${ x + y \leq z}$:

  | <!-- -->  |  |
  |---|---|
  |$(1, ~2, ~3)$   |    (on point)|
  |$(1, ~2, ~2.999)$ |  (off point)|


```

### Domains with Multiple Variables

So far, we have discussed strategies for choosing on and off points for domains containing only single boundaries. However, it is common for domains to have more than one variable. In fact, the triangle program in Example [23](example_23) does have equivalence classes with multiple variables, (for example, the invalid case of ${ x + y > z}$), however, we conveniently did not discuss this.

First, let us consider test cases for two-dimensional linear boundaries. Consider an equivalence class made up of the following linear boundaries: ${ x \geq 1}$, ${ y \leq 10}$, and ${ y \leq x + 5}$, in which x and y are integers. This can be visualised using the two-dimensional graph in Figure [3.1](f_3_1), with the shaded section identifying the equivalence class.

(f_3_1)=
```{figure} figures/BVA-Multiple-Variables.png
---
name: input-domains-valid-invalid
align: center
width: 70%
---
```
<p style="text-align: center;">Figure 3.1: On points and off points in a two-dimensional, linear boundary</p>

In this case, we have to choose on point values that satisfy all three of the linear boundaries. However, the intersections of the lines lead to fewer test cases, because they test more than one on point in a single test case. This is advantageous because it reduces the number of test cases, and gives us test cases that are more sensitive to boundary-related faults. However, it can also make it a bit more difficult to debug, since it is not clear which boundary a test refers to. In the above, we choose two on points: ${ x = 1 \land y = 6}$, and ${ x=5 \land y = 10}$.

Similarly, the off points near intersections can reduce the numbers of test cases outside the equivalence class. In Figure [3.1](f_3_1), we choose the test inputs ${ x = 0 \land y = 6}$, and ${ x = 5 \land y = 11}$. The former is an off point for both ${ x \geq 1}$ as well as ${ y \leq x + 5}$. The latter is an off point for ${ y \leq 10}$ as well as ${ y \leq x + 5}$.

(example_24)=
```{admonition} Example 24: The Triangle Program --- Again!
:class: tip
If we return to the triangle example again, we see that there are equivalence classes based on three variables. Therefore, we need to consider an three-dimensional boundary. This is harder to visualise (and anything above three dimensions even more so!), but selecting the on and off points is still straightforward for this particular program.

If we consider the invalid case in which ${ x + y \leq z}$ (that is, the length of the side z is greater than the sum of the other two sides), then we need to select an on and an off point for this. Similar cases exist for when x and y are too long. The inequality, ${ x + y < z}$, describes a *plane* in a three-dimensional graph, rather than a line in a two-dimensional graph. To choose test inputs, we must choose three on points on the plane, and one off point.

If we return to the test cases that we derived for these in Example [23](example_23), one can see that the test cases selected were on and off points for this plane:

  |  <!-- --> |  |
  |---|---|
  |$(1, ~2, ~3)$       |    (on point 1)|
  |$(100, ~200, ~300)$ |  (on point 2)|
  |$(400, ~2, ~402)$   |  (on point 3) |
  |$(1, ~2, ~2.999)$   |  (off point)|


The point $(1, 2, 3)$ is on the plane ${ x + y = z}$, while the point $(1, 2, 2.999)$ is just below it. However, is this enough to detected a boundary shift?

We also have the following changes compared to the earlier example:

- Equilateral:

  | <!-- -->  |  |
  |---|---|
  |$(3, ~3, ~3)$     |    (on point 1)|
  |$(100, ~100, ~100)$ |  (on point 2 -- far away from on point 1)|
  |$(2.999,~ 3,~ 3)$  |   (off point below for x)|
  |$(3.001,~ 3,~ 3)$   |  (off point above for x)|


- Isosceles:

  | <!-- -->  |  |
  |---|---|
  |$(3, ~3, ~4)$     |  (on point 1)|
  |$(100, ~100, ~4)$ |  (on point 2 -- far away from on point 1)|
  |$(2.999,~ 3,~ 4)$ |  (off point below)|
  |$(3.001,~ 3,~ 4)$ |  (off point above)|


- Scalene:

  | <!-- -->  |   |
  |---|---|
  |$(3, ~3, ~3)$    |     (on point 1)|
  |$(100, ~100, ~100)$ |  (on point 2 -- far away from on point 1)|
  |$(2.999, ~3, ~3)$   |  (off point below)|
  |$(3.001, ~3, ~ 3)$  |  (off point above)|

```

### Detecting Boundary Shifts in Inequalities

Figure [3.2](f_3_2) shows a boundary with an on and an off point. The solid line represents the boundary that has been identified (for equivalence partitioning, this is the expected boundary derived from the functional requirements, while for domain testing, this is the actual boundary in the program), while the dashed line represents a shifted boundary (for equivalence partitioning, this is the boundary in the program, while for domain testing, this is the boundary derived from the functional requirements). The black dot represents the on point of the boundary, and the white dot the off point.


(f_3_2)=
```{figure} figures/BVA-boundary-shifts.png
---
name: BVA-boundary-shifts
align: center
width: 60%
---
```
<p style="text-align: center;">Figure 3.2: Possible inequality boundaries shifts with on and off points</p>


From this figure, one can see that a boundary shift down --- in other words, the equivalence class is smaller than expected --- will be detected because the on point is identified as being in the wrong domain. However, for the other two, it is possible to have a boundary shift that is not identified.

In the case of a shift up (the equivalence class is larger than expected), the shift has to be great enough that the off point can detect it. For example, if the boundary ${ x \leq 10.0}$ is identified from the functional requirements, then we would select the on point $10.0$, and the off point $10.001$. However, if this boundary is incorrectly implemented as ${ x \leq 10.0000000001}$ (highly unlikely I'm sure, but this is for illustrative purposes only), then the on point $10.0$ still falls inside the boundary, and the off point $10.001$ still falls outside the boundary. Therefore, these values will not detect the shift. This means that if the off point is a certain distance away from the boundary, then only shifts at least as big as this distance can be detected by that off point. Fortunately, as Beizer points out in his book *Black-Box Testing --- Techniques for Functional Testing of Software and Systems*, faults in programs generally occur naturally, and are not the result of sabotage, so choosing an off point that is close to the boundary is almost always likely to detect boundary shifts.

In the case of the tilted boundary, the given boundary and the shifted boundary intersect at some point. In the diagram above, the on point falls within the boundary, and the off point falls outside of the boundary, therefore, these cases will not detected the boundary shift.

Looking at Figure [3.2](f_3_2), it is clear that select one on point and one off point is not sufficient to detect shifts. For the middle case, the best that can be done is to select the off point as close to the boundary as possible, which may be impossible in some domains.

However, we can improve on the case of the tilted boundary in some cases. If the values that can be selected have a minimum and a maximum (the *extremum points*), then we choose both of those as on points. This guarantees that, for any tilted boundary, at least one of the points must lie outside the shift boundary. Figure [3.3](f_3_3) demonstrates this. The position of the tilted boundary does not have an impact on this --- as long as some of the boundary falls below and some above, then one of the on points will fall outside the domain. If the two on points are close together, then this reduces the chance of the boundary shift being detected.


(f_3_3)=
```{figure} figures/BVA-tilted-boundary.png
---
name: BVA-tilted-boundary
align: center
width: 50%
---
```
<p style="text-align: center;">Figure 3.3: Tilted inequality boundary with extremum and non-extremum on points</p>

In the case that the boundary has no extremums (or only a minimum or maximum but not both) due to it being infinite, then the further away the two on points are from each other, the greater chance there is of producing a failure.

Once the two on points are selected, the best off point to select is one that is as close to the middle as possible between the two on points. This is known as the centroid. Alternatively, one could select more than one off point, but this would likely have little value.

If we return to Figure [3.1](f_3_1), this implies that we are required to add both point on the line ${ y = x + 5}$, but it must be at a point where ${ y < 10}$ to remain inside the boundary.

### Detecting Boundary Shifts in Equalities

For boundaries defined by equalities, the technique is slightly different. This is because the domain is defined by a line (in two dimensional cases) or a plane (in three dimensional cases), rather than a block. Therefore, if we consider the tilted boundary from Figure [3.2](f_3_2), the on point, represented by the black dot, would not fall inside the boundary, because the entire equivalence class is given by the line, rather than the area below the line.

Recall from Section [](values-and-boundaries), that for equalities involving one variable, we select the on point, and both off points (above and below). For a boundary shift up or down, these are enough to detect the shifts larger than the distance between the on point and off points. However, if the on point chosen happens to intersect with the given and shifted boundary, as in Figure [3.4](f_3_4), then the tilted shift will not be detected.

(f_3_4)=
```{figure} figures/BVA-tilted-equality.png
---
name: BVA-tilted-equality
align: center
width: 30%
---
```
<p style="text-align: center;">Figure 3.4: Tilted equality boundary</p>

This is similar to the problem with shifted boundaries for inequalities, and as with that problem, choosing two on points removes this. However, unlike inequality boundaries, equality boundaries are strictly equal, therefore choosing extremum on points is unnecessary. If we consider the boundary tilt in Figure [3.4](f_3_4), then *any* on point other than the point at the intersection will detect the boundary shift. Therefore, if we have *any* two on points, then if the given and shifted boundaries intersect, by definition, at most one of them can be at this intersection. From this, we conclude that we need to chose two arbitrary points.

The same argument goes for non-equalities --- that is, defined by predicates such as ${ x \neq y + 10}$.

From the above discussion, we conclude the following about selecting boundary values for two-dimensional boundaries:

- For boundaries defined by an inequality, select two on points that are as far apart as possible (or reasonable for infinite domains), and one off point as close to the middle of the two on points as possible;

- For boundaries defined by an equality, select two on points, and two off points (one below and one above); similarly for non-equalities; and

- If the off point is of distance $d$, then only boundary shifts of magnitude greater than $d$ can be detected.

We can generalise this idea to $N$ dimensional input spaces.

- $N$ on points and $1$ off point are needed for boundaries based on inequalities, with the on points as far apart as possible;

- $N$ on points and $2$ off points if are needed boundaries based on equalities and non-equalities.

- If the off point test case is a distance $d$ from the boundary, then only boundary shifts of magnitude greater $d$ can be detected.

### Boolean Boundaries

Some equivalence classes just specify a Boolean condition. For example, in input could be a Boolean or the equivalent class could just represent a "must be" situation, such as: the first character of an input must be a numeric character.

In these cases, the boundary is just the true/false value of the class.

```{admonition} Example -- Boolean boundaries
:class: tip

If we recall [Example 13](example_13) from the [Input Partitioning](Input-Partitioning.md) chapter, we have two equivalence classes:

1. $\{s \mid {\rm the~first~character~of}~ s~{\rm is~a~numeric}\}$
2. $\{s \mid {\rm the~first~character~of}~ s~{\rm is~ not~ a~ numeric}\}$

The on point for class 1 is just $true$ while the off point is $false$

For class 2, the off point is $true$ while the on point is $false$.

In this case, clearly the on point for each class is the off point for the other.

```