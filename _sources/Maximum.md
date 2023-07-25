# Maximum Likelihood Estimation

Maximum likelihood estimation is by far the better technique for estimating model parameters. Unfortunately it also often requires numerical solutions to solve sets of equations for those very parameters.

Intuitively the maximum likelihood estimator tries to pick values for the parameters of the basic execution time model that maximise the probability that we get the observed data. For example suppose that we had the failure intensity data shown in Figure [B.1](f_b_1).

(f_b_1)=
```{figure} figures/failure-intensity-data.png
---
align: center
width: 70%
---
```

<p style="text-align: center;">Figure B.1: Failure intensity against time derived from failure time data for a system.</p>

Then we start exploring the parameter space consisting of pairs of values $(\lambda_0,~\nu_0)$ for values that will maximise the likelihood of obtaining the observed values. When we start exploring the values of $\lambda_0$ and $\nu_0$ we may find that the curves like something like those in Figure [B.2](f_b_2)

(f_b_2)=
```{figure} figures/failure-intensity-estimates.png
---
align: center
width: 70%
---
```

<p style="text-align: center;">Figure B.2: Different choices for the parameters result in different curves. The probability of getting the observed data on the curve that we want must give maximum probability of obtaining the data.</p>

To estimate the parameters, maximum likelihood now works as follows. Suppose that we have only one parameter $\theta$ instead of the two parameters in the Basic Execution time model. Now, if we make $n$ observations $x_1$, $x_2$, ..., $x_n$ of the failure intensities for our program the probabilities are:

$$ L(\theta) \,=\, P\{X(t_1) = x_1\} P\{X(t_2) = x_2\} \ldots P\{X(t_n) = x_n\} $$

To function $L(\theta)$ reaches its maximum when the derivative is $0$, that is,

$$
\begin{array}[c]{c}
   \rm d L(\theta) \\ \hline \rm d \theta
  \end{array}\, = \, 0
$$

For example, if we have an exponential probability law $\theta e^{-\theta T}$ with a parameter $\theta$ and we make $n$ observations $x_1$...$x_n$ at times $t_1$ ...$t_n$ then from the exponential distribution, $\rm L(\theta)$ becomes 

$$ \theta e^{-\theta t_1} \theta e^{-\theta t_2}\ldots \theta e^{-\theta t_n} = (\theta)^n e^{-\theta \Sigma t_i} $$ 

An estimate for the parameter is then value of $\theta$ making 

$$
  \begin{array}[c]{c}
   \rm d  (\theta)^n e^{-\theta \Sigma t_i} \\ \hline \rm d \theta \end{array}\, = \, 0
$$ 

which gives a maximum. In general, when the exponential function $e$ is involved take the natural log of $\rm L(\theta)$ and take the derivative. Doing this gives the same value for $\theta$ as the derivative in. In the case of $\rm L(\theta)$ we get 

$$
  \begin{array}[c]{c} \rm d  \ln (\theta)^n e^{-\theta \Sigma t_i} \\ \hline \rm d \theta
  \end{array}\, = \, \begin{array}[c]{c} n \\ \hline \theta \end{array}
 - \Sigma t_i \, = \, 0
$$

and we can easily solve for $\rm \theta$ to get $\rm \theta = \begin{array}[c]{c} n \\\hline \Sigma t_i \end{array}$.

The situation with the basic execution time model is not so easy because we have two parameters. To simplify the procedure let $\beta = \frac{\lambda_0}{\nu_0}$ and let $X$ be our random variable whose probability density function is $\rm \lambda_0 e^{-\beta_1 \tau}$. Next, assume that we have been testing for an execution time period of $T_F$ seconds and have experienced a total of $M_F$ failures to this point. If we repeat the idea above and take *partial derivatives*: 

$$
  \begin{array}[c]{c}
    \rm \partial\, L(\lambda_0,\,\beta_1)\\\hline
    \partial \,\lambda_0 x
  \end{array} \,=\,0
  \qquad\qquad 
  \begin{array}[c]{c}
    \rm \partial \, L(\lambda_0,\,\beta_1)\\\hline
    \partial\,\beta_1
  \end{array}\,=\, 0
$$
  
we again arrive at our maximum. We again need to take the natural logarithms of both sides to get

$$
  \begin{array}[c]{c}
    \rm \partial\, \ln L(\lambda_0,\,\beta_1)\\\hline
    \partial \,\lambda_0 x
  \end{array} \,=\,0
  \qquad\qquad 
  \begin{array}[c]{c}
    \rm \partial \, \ln L(\lambda_0,\,\beta_1)\\\hline
    \partial\,\beta_1
  \end{array}\,=\, 0.
$$

The resulting equations that need to be solved often require numerical methods that are outside of the scope of these notes. For completeness, the two estimators are included below.

$$
\begin{aligned}
  \begin{array}[c]{l}
    M_F \,T_F \\\hline \sum_{i=1}^{M_F} t_i + T_F({\nu_0} -i + 1)
  \end{array} + \sum_{i = 1}^{M_F} 
  \begin{array}[c]{c} 1\\\hline \nu_0 - i + 1
  \end{array}& = & 0
\end{aligned}
$$

$$
\begin{aligned}
  \begin{array}[c]{l}
  M_F \\\hline \beta_1
  \end{array}  - 
  \begin{array}[c]{l} M_F \, T_F \\\hline e^{\beta_1 T_F} - 1 
  \end{array} - \sum_{i=1}^{M_F} t_i & = & 0
\end{aligned}
$$
