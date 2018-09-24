The secretary problem is a famous mathematical problem.

> Imagine an administrator who wants to hire the best secretary out of n
> rankable applicants for a position. The applicants are interviewed one by one
> in random order. A decision about each particular applicant is to be made
> immediately after the interview. Once rejected, an applicant cannot be
> recalled. During the interview, the administrator can rank the applicant among
> all applicants interviewed so far, but is unaware of the quality of yet unseen
> applicants.
>
> — Citation: Wikipedia [Secretary Problem](https://en.wikipedia.org/wiki/Secretary_problem)

The term "rankable" means that we can only compare the applicants to each
other and place them in order.

The secretary problem is solved with a stopping strategy. The idea is to
decide on a stopping index $c$. The first $c - 1$ applicants are
rejected. Any following applicant is selected if better than anyone seen
before or if the applicant is the last. The optimum stopping index is given by
the very elegant rule $^n/_e$.

## Experiment

This result is suggested as the algorithm to use when confronted with a
secretary problem like scenario in real life. Therefore, I decided to run an
experiment.

Before I continue I must admit that I made a mistake. I misunderstood the
meaning of the optimization objective "to hire the best secretary".

First I assumed that such a complex value as a secretary quality must be
normally distributed in a population of secretaries. Then I defined a
Secretary Quality (SQ) distribution as a normal distribution with an average
of 100 and variance of 15. I continued with code that generates many random
samples of size 100 from $\mathcal{N}(100, 15)$ and simulates every possible
stopping index from1 to 99.

    
```python
def select(applicants, c):
    best_skipped = max(applicants[:c])
    for applicant in applicants[c:]:
        if applicant > best_skipped:
            return applicant
    return applicants[-1]
```    

<p/>

```python    
def experiment(selection_function):
    n = 100
    c = list(range(1, 99))
    sample_size = 3000
    measures = np.zeros((len(c), sample_size))
    for i, ci in enumerate(c):
        for j in range(sample_size):
            applicants = np.random.normal(loc=100, scale=15, size=n)
            measures[i][j] = selection_function(applicants, ci)
    return c, measures
```

Now I run the experiment and plot the average outcome for each stopping rule
c.

    
```python
c, measures = experiment(select)
```    

![secretary_problem_1](/images/secretary_problem_1.png)

This clearly shows that $n/e$ is not the optimal strategy. Reading more
closely on the objective function I found my mistake. You can probably spot my
mistake if you read the optimization objective as it was stated by Thomas S.
Ferguson.

> You (the administrator) are very particular and will be satisfied with
> nothing but the very best. That is, your payoff is 1 if you choose the best of
> the n applicants and 0 otherwise
>
> — Citation: Who Solved the Secretary Problem

Ferguson is polite and states that the administrator is very _particular_. I
will not be so polite and will cast a moral judgement on such an
administrator. This type of optimization objective serves only the vanity of
the said administrator.  "Look, I have the best secretary!". Not only it's
deplorable, it's also suboptimal. It's the classical case of "perfect is the
enemy of good". The stopping rule of $n/e$ is optimizing for the BEST at
the expense of GOOD. Please stop advising using this rule.

## Cardinal Payoff

A modified version of a secretary problem with a cardinal payoff objective was
proposed and solved by Bearden in 2006. In that version the objective is
changed to optimize for the rank of the selected applicant. Bearden showed
that the optimal stopping index is $\sqrt{n}$.

![secretary_problem_2b](/images/secretary_problem_2b.png)

## Quality Payoff

Inspired by the work of Bearden, a group of four mathematicians in 2017
proposed a variation in which the objective is to maximize the expected
quality of the selected applicant. In their
[paper](https://arxiv.org/pdf/1605.06478.pdf) they worked out the optimum
stopping rules for various distributions of secretary quality. The optimal
stopping for the normal distribution is given by:

$$ c(n) \leq \frac{(n - 2) \log{\log{n}}} {\log({\frac{(n-1)^2}{2\pi}})} + 1 $$

![secretary_problem_3c](/images/secretary_problem_3c.png)

It still doesn't look right. In the process of calculating the stopping rule,
the applicants' qualities are assumed to follow the distribution $\mathcal{N}(0, 1)$.
And I used different parameters $\mathcal{N}(100, 15)$. It's possible that the variance
of the distribution can have an effect on the optimum stopping value. It's
also possible that I didn't understand correctly that formula and what we see
is the upper bound for $c$ and not the optimum.

## Recap

When solving a secretary like problem don't be fooled by the vanity of the
$^n/_e$ rule.

Variant         | Optimizes | Stopping Rule  
----------------|-----------|---------------------- 
Binary Payoff   | Vanity    | $^n/_e$
Cardinal Payoff | Rank      | $ \sqrt\{n\} $
Quality Payoff  | Utility   | $\frac\{(n - 2)\log\{\log\{n\}\}\} \{\log(\{\frac\{(n-1)^2\}\{2\pi\}\})\} + 1$

