The Collatz conjecture describes a sequence of numbers starting from any
positive integer. The next term of a sequence is generated from the previous n
by $3n+1$ if $n$ is odd or $n/2$ otherwise. The conjecture states that this
sequence will always reach 1. It will be clear when you read the code.

    
```python
def collatz(n):
    yield n
    while n != 1:
        if n % 2 == 0:
            n = n // 2
        else:
            n = 3 * n + 1
        yield n
```    

When written in a programming language, it's easier to understand the bold
nature of that conjecture. I think that any self-respecting programmer would
never put such code in production based on a conjecture that it will terminate
for all positive integers. It's not clear how long it takes to reach 1, if
ever. I'm not committing that!

Let's look at four examples of the sequence for the initial values of 3, 10,
20 and 19

    
```python
for n in [10, 3, 20, 19]:
    print(list(collatz(n)))
```

<p/>    

```    
    [10, 5, 16, 8, 4, 2, 1]
    [3, 10, 5, 16, 8, 4, 2, 1]
    [20, 10, 5, 16, 8, 4, 2, 1]
    [19, 58, 29, 88, 44, 22, 11, 34, 17, 52, 26, 13, 40, 20, 10, 5, 16, 8, 4, 2, 1]
```    

Just generating a few sequences may reassure you but it's not a proof. And
what is the expected length of that sequence? Let's plot the length of all
sequences from all $n$ from 1 to 10000

    
```python    
n_range = list(range(1,10000))
sequence_length = []
for n in n_range:
    sequence_length.append(len(list(collatz(n))))

# plotting
plt.scatter(n_range, sequence_length, marker='.')
plt.xlabel('n')
plt.ylabel('Sequence Length')
plt.title('Collatz Sequence Length')
plt.show()
```    

![collatz_len](/images/collatz_len.png)

This is a very interesting pattern. Notice how the sequence length remains
small relative to n. From that plot it's possible to see that the minimum
sequence length follows some function. Actually the fastest way to get to 1 is
to always hit the even case and divide by two at each iteration. This is only
possible for $n = 2^k$ (for powers of two: $1, 2, 4, 8, 16, 32
…$). This means that the minimum value for the sequence length is given by
$log_2(n)$.

Despite its simple formulation the Collatz conjecture is still an open
problem. It touches not only the number theory but also decidability, theory
of computation and other fields that I hope exploring in the future.

