In 1994 Peter Shor published a [quantum factoring
algorithm](https://arxiv.org/abs/quant-ph/9508027) with a polynomial number of
steps. The best known classical factoring algorithm [General number field
sieve](https://en.wikipedia.org/wiki/General_number_field_sieve) runs in
exponential time. In this post I will implement the Shor's factoring algorithm
in Python.

The factoring algorithm goes like this. To factor a number $N$, randomly
choose $a$ smaller than $N$. Then find the smallest period $
r$ of $a^x \bmod N$. Finally recover the factors from $gcd(a^{r/2}
\pm 1, N)$.

The $gcd$ function calculates the greatest common divisor of two integers. It
's the greatest integer that divides both numbers. The [Euclidean
algorithm](https://en.wikipedia.org/wiki/Euclidean_algorithm) that dates 300
BC runs in polynomial time.

First we need that period searching function. I will cheat here, and instead
of a quantum algorithm, I will use a naive classical algorithm.

    
```python    
def modexp_period(a, N):
    "Return the period of a^x mod N"
    seen = {}
    y = 1
    for x in range(1, N):
        y = a * y % N
        if y in seen:
            return x - seen[y]
        else:
            seen[y] = x
```    

To calculate the sequential values of $a^x \bmod N$ I used the property  
  
$$ ab \bmod N = (a \bmod N) (b \bmod N) \bmod N $$  

From that property we can deduce the following for all $a < N$ .  
  
$$ a^{x+1} \bmod N = a(a^x \bmod N) \bmod N $$  

Now that we're done cheating with that period finding function, we can get to
the factoring algorithm.

    
```python
from math import gcd
from random import randint


def factor(N):
    while 1:
        # Choose random number a
        a = randint(2, N)
        
        # --- QUANTUM PART: Find the period of a^x mod N
        r = modexp_period(a, N)
        # --- END QUANTUM PART
        
        # If period is odd restart
        if r % 2 == 1:
            continue
        
        # Another restart condition
        if a ** (r // 2) % N + 1 == N:
            continue
        
        # Extract two common factor
        p = gcd(a ** (r // 2) + 1, N)
        q = gcd(a ** (r // 2) - 1, N)
        
        return p, q
```

This simple looking algorithm exposes some deep questions. Why is it possible
to recover the factors from the period? How the quantum period finding works?
How big is the average period $r$?

For now let's just factor some number.

    
```python
factor(391)
```

<p/>
    
```    
(17, 23)
```    

