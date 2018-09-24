.. title: Collatz Tag System
.. slug: collatz-tag-system
.. date: 2018-06-01 12:40:42 UTC-04:00
.. tags: collatz conjecture
.. category: 
.. link: 
.. description: 
.. type: text

As programmers we develop a sense for program execution. We can step through
code and follow the execution of simple programs. A simple while loop
shouldn’t puzzle us right?

.. TEASER_END

Let’s look at this simple function:

.. code-block:: python

    def compute(n:int):
        m = 2
        rules = {'a': 'bc', 'b': 'a', 'c': 'aaa'}
        s = 'a' * n
        while s != 'a':
            s = s[m:] + rules[s[0]]

Will this function terminate for any positive integer n?

On each iteration we always remove two letters. But we add 1, 2 or 3 letters.
Probabilistically, *if there are no loops*, it should terminate after stumbling
on a sequence with enough 1s and 2s. But it may run forever if our assumption
is false.

Actually answering this question is equivalent to solving the Collatz
Conjecture. This function is based on a set of rules for a tag system
representing the Collatz function developed by Liesbeth De Mol.

Here's an execution trace for n=5.

::

    n = 5
    s = aaaaa

    iteration |s[:2]|s[2:] + rules[s[0]]        
    ----------+-----+-------------------------------------
     0        |   aa|aaabc
     1        |   aa|abcbc
     2        |   ab|cbcbc
     3        |   cb|cbcaaa
     4        |   cb|caaaaaa     
     5        |   ca|aaaaaaaa    8 = (3*5 + 1)/2
     6        |   aa|aaaaaabc
     7        |   aa|aaaabcbc
     8        |   aa|aabcbcbc
     9        |   aa|bcbcbcbc
    10        |   bc|bcbcbca
    11        |   bc|bcbcaa
    12        |   bc|bcaaa
    13        |   bc|aaaa        4 = 8/2
    14        |   aa|aabc
    15        |   aa|bcbc
    16        |   bc|bca
    17        |   bc|aa          2 = 4/2
    18        |   aa|bc
    19        |   bc|a           1 = 2/2
    ----------+-----+-------------------------------------


- :doc:`collatz-conjecture`