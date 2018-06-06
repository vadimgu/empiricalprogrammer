In university I experimented with random numbers for fun. One such experiment
involved generating pairs of random numbers (from a uniform distribution in
range 0 to 1) and multiplying them. If you observe the first digit of such
products you will see that the products starting with digit 1 are the most
popular. The second most popular first digit is 2. And it continues downhill
until the least popular first digit 9. There's nothing wrong with my random
number generator.

    
```python
import random
digits = '123456789'
size = 100000
count_digits = {digit: 0 for digit in digits}
for i in range(size):
    product = '%e' % (random.random() * random.random())
    count_digits[product[0]] += 1

for digit in digits:
    print ('{} {:.3f}'.format(digit, count_digits[digit]/size))
```    
    
```    
    1 1 0.241
    2 2 0.185
    3 3 0.146
    4 4 0.118
    5 5 0.095
    6 6 0.077
    7 7 0.058
    8 8 0.046
    9 9 0.034
```    

You might have heard of the [Benford's
Law](https://en.wikipedia.org/wiki/Benford%27s_law) which deals with the first
digit frequency in the natural data. A set of numbers is said to satisfy the
Benford's Law if the first digit $d$ is observed with probability

$$P(d) = \log_{10}(1+\frac{1}{d})$$

On that Wikipedia page one of many distributions that's said to obey the
Benford's law are numbers that result from mathematical combination of
numbers: e.g. quantity × price. But the experiment above is not very natural
as it uses a uniform distribution which is not very common in nature. And I
think, because of that it follows the Benford's Law only in spirit. I derived
a precise formula for the distribution of the first digits of a product of two
random numbers. The first digit $d$ for a product of two random numbers
is observed with probability

$$ Q(d) = \frac{1}{9}\Big[1 - (d+1)\ln(d+1) + d\ln(d) + \frac{10}{9}\ln(10)\Big] $$

![](/images/first_digit_cmp.png)

This is my original work and I cover it in more details in that
[notebook](https://github.com/vadimgu/notebooks/blob/master/benfords.ipynb).
Here I'll just present the main idea with pretty pictures.

Let's make a 1 × 1 square and color-code the first digit of a product.

    
```python    
import numpy as np
import matplotlib
from matplotlib import pyplot as plt

X, Y = np.meshgrid(np.arange(0.001, 1, 0.001),
                   np.arange(0.001, 1, 0.001))
Z = X * Y
# Get the first digit of Z
D = np.floor(Z / 10 ** np.floor(np.log10(Z)))

plt.figure(figsize=(15, 8))
cmap = matplotlib.cm.get_cmap('gray', 9)
plt.imshow(D, cmap=cmap, extent=[0, 1, 0, 1],  origin='lower')
plt.colorbar()
plt.title('First Digit of $x \\times y$', fontsize=14)
plt.xlabel('x', fontsize=14)
plt.ylabel('y', fontsize=14)
plt.show()
```    

![](/images/first_digit_product.png)
The first digit of a product in a 1 × 1 square. The probability for a
randomly selected coordinate hitting the black area is equivalent to the
probability of a product's first digit to be 1.  

The black area represents the products starting with digit 1. We can visually
see it's predominance. We can also see how small is the white area
representing the digit 9. To derive the formula given above, I defined the
contour functions and with the help of some integrals and infinite sums I
arrived at a formula for each digit area. Since we're dealing with a 1 × 1
square we get straight probabilities from the area.

And for even more fun here are some "First Digit" plots for a few other
operations on two random numbers.

![](/images/first_digit_sum.png)

![](/images/first_digit_division.png)

![](/images/first_digit_exp.png)

