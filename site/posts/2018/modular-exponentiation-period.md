The modular exponentiation function plays a central role in the Shor's
algorithm that I covered it in a [previous
post](2018/03/15/shors-factoring-algorithm/).
The factoring of a number N is reduced to find the period of the modular
exponentiation.

$$f(x) = a^x \bmod N $$

Using a simple period finding function bellow.

    
```python    
def modexp_period(a, N):
    seen = {}
    y = 1
    for x in range(1, N*2):
        y = y * a % N
        if y in seen:
            period_start = seen[y]
            period_end = x
            period = period_end - period_start
            return period_start, period
        else:
            seen[y] = x
```    

Let's compute some periods for random values of $N$ and $a$.

```python

experiment = []

for i in range(10000):
    N = random.randint(4, 2**18)
    a = random.randint(2, N)
    period_start, period = modexp_period(a, N)
    experiment.append((N, a, period_start, period))

df = pd.DataFrame.from_records(experiment,
                                columns=('N', 'a', 'period_start', 'period')

```

Here's a sample of that experiment. We can see various period values. Not all
periods start at 1.

```python

df.sample(10)

```

<p/>

```

               N       a  period_start  period 
    5113  257501  148409             1  257500 
    7859  134749   63133             1   64860 
    8138  136138   49563             1    1582 
    9900   66478   47070             1    8106 
    253   221351  129069             1   34052 
    2350  152328   98726             3      96 
    7834  205466  157669             1    2703 
    6888  194818  112643             1    7308 
    2963    6911    6564             1    1382 
    8864   62897   31598             1   62896 
```    

Now let's plot the period as a function of $N$.

    
```python
plt.scatter(df.N, df.period, s=1)
```

![](/images/modexp1.png)

We can clearly see that the period tends to stick to lines $\frac{N}{1},
\frac{N}{2}, \frac{N}{3}, \frac{N}{4}, …$. The frequency plot of the $
\frac{r}{N}$ is also very interesting.

    
```python    
df['ratio'] = df.period / df.N
df.ratio.hist(bins=200)
xticks = list(range(1,9))
locs = [1/loc for loc in xticks]
labels = [r'$\frac{1}{%d}$' % loc for loc in xticks]
plt.xticks(locs, labels)
```    

![](/images/modexp2-1.png)

It looks like all the numbers that produce a period $r = N - 1$ are
primes. Here's a sample where period is equal to $N - 1$

    
```python    
df[df['period'] == df['N'] - 1].sample(10)
```    
    
```    
               N      a  period_start  period     ratio
    656    28087   1445             1   28086  0.999964
    6292  164767  73848             1  164766  0.999994
    1931  120689  20070             1  120688  0.999992
    9798   48247  20127             1   48246  0.999979
    2439   80557  20640             1   80556  0.999988
    3262   49937  22800             1   49936  0.999980
    4704   73517  50342             1   73516  0.999986
    8664   77747   9059             1   77746  0.999987
    5351   93893  57853             1   93892  0.999989
    9830    9929    867             1    9928  0.999899
```    

Here's a conjecture. Given a prime numbers $P$ and a positive integer
$a \in [2, P[$. The periodic function $a^{x} \bmod P$ will have a
period $r \equiv -1 \bmod P$.

Update: Just googling for primality tests I found that the above conjecture is
the [Fermat primality
test](https://en.wikipedia.org/wiki/Fermat_primality_test) based on [Fermat's
little theorem](https://en.wikipedia.org/wiki/Fermat%27s_little_theorem).

