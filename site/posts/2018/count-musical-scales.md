The idea of counting musical scales was picked from a blog post
[here](https://www.johndcook.com/blog/2017/09/30/how-many-musical-scales-are-
there/) and from that
[question](https://math.stackexchange.com/questions/1810374/how-many-7-note-
musical-scales-are-possible-within-the-12-note-system). Both places agree on
the number of 266 heptatonic (7 notes) scales. Here I will attempt to define
some rules to generate all "nice" heptatonic scales.

A musical scale is a series of intervals that span 12 half-tones. Therefore a
musical scale can be encoded as a series of intervals. For example the major
scale can be encoded as a series of 7 intervals:

    
    
    2, 2, 1, 2, 2, 2, 1
    

Here's what we have:

  1. We have 7 intervals (notes).
  2. All intervals must sum to 12 half-tones so we end exactly one octave higher.
  3. The interval can be 1, 2 or 3 half-tones.
  4. There should be no more than two consecutive half-tones. This is my rule for a "nice" scale.

Using a constraint programming solver from [Google's OR-
Tools](https://developers.google.com/optimization/cp/cp_solver) I was able to
write a small program that prints all "nice" scales.

    
    
    from ortools.constraint_solver import pywrapcp
    
    solver = pywrapcp.Solver("nice_musical_scales")
    
    # Declare seven intervals
    intervals = [solver.IntVar(1, 3, f'interval_{i}') for i in range(7)]
    
    # End exactly one octave higher
    solver.Add(solver.Sum(intervals) == 12)
    
    # No consecutive 1, 1, 1 intervals
    for a, b, c in zip(intervals, intervals[1:], intervals[2:]):
        solver.Add(a + b + c > 3)
    
    db = solver.Phase(intervals,
                      solver.CHOOSE_FIRST_UNBOUND,
                      solver.ASSIGN_MIN_VALUE)
    solver.Solve(db)
    while solver.NextSolution():
        result_intervals = [interval.Value() for interval in intervals]
        print(', '.join(map(str, result_intervals)))
    

Running this code gives us 198 "nice" heptatonic scales

    
    
    1, 1, 2, 1, 1, 3, 3
    1, 1, 2, 1, 2, 2, 3
    1, 1, 2, 1, 2, 3, 2
    1, 1, 2, 1, 3, 1, 3
    1, 1, 2, 1, 3, 2, 2
    1, 1, 2, 1, 3, 3, 1
    1, 1, 2, 2, 1, 2, 3
    1, 1, 2, 2, 1, 3, 2
    1, 1, 2, 2, 2, 1, 3
    1, 1, 2, 2, 2, 2, 2
    1, 1, 2, 2, 2, 3, 1
    1, 1, 2, 2, 3, 1, 2
    1, 1, 2, 2, 3, 2, 1
    1, 1, 2, 3, 1, 1, 3
    1, 1, 2, 3, 1, 2, 2
    1, 1, 2, 3, 1, 3, 1
    1, 1, 2, 3, 2, 1, 2
    1, 1, 2, 3, 2, 2, 1
    1, 1, 2, 3, 3, 1, 1
    1, 1, 3, 1, 1, 2, 3
    1, 1, 3, 1, 1, 3, 2
    1, 1, 3, 1, 2, 1, 3
    1, 1, 3, 1, 2, 2, 2
    1, 1, 3, 1, 2, 3, 1
    1, 1, 3, 1, 3, 1, 2
    1, 1, 3, 1, 3, 2, 1
    1, 1, 3, 2, 1, 1, 3
    1, 1, 3, 2, 1, 2, 2
    1, 1, 3, 2, 1, 3, 1
    1, 1, 3, 2, 2, 1, 2
    1, 1, 3, 2, 2, 2, 1
    1, 1, 3, 2, 3, 1, 1
    1, 1, 3, 3, 1, 1, 2
    1, 1, 3, 3, 1, 2, 1
    1, 1, 3, 3, 2, 1, 1
    1, 2, 1, 1, 2, 2, 3
    1, 2, 1, 1, 2, 3, 2
    1, 2, 1, 1, 3, 1, 3
    1, 2, 1, 1, 3, 2, 2
    1, 2, 1, 1, 3, 3, 1
    1, 2, 1, 2, 1, 2, 3
    1, 2, 1, 2, 1, 3, 2
    1, 2, 1, 2, 2, 1, 3
    1, 2, 1, 2, 2, 2, 2
    1, 2, 1, 2, 2, 3, 1
    1, 2, 1, 2, 3, 1, 2
    1, 2, 1, 2, 3, 2, 1
    1, 2, 1, 3, 1, 1, 3
    1, 2, 1, 3, 1, 2, 2
    1, 2, 1, 3, 1, 3, 1
    1, 2, 1, 3, 2, 1, 2
    1, 2, 1, 3, 2, 2, 1
    1, 2, 1, 3, 3, 1, 1
    1, 2, 2, 1, 1, 2, 3
    1, 2, 2, 1, 1, 3, 2
    1, 2, 2, 1, 2, 1, 3
    1, 2, 2, 1, 2, 2, 2
    1, 2, 2, 1, 2, 3, 1
    1, 2, 2, 1, 3, 1, 2
    1, 2, 2, 1, 3, 2, 1
    1, 2, 2, 2, 1, 1, 3
    1, 2, 2, 2, 1, 2, 2
    1, 2, 2, 2, 1, 3, 1
    1, 2, 2, 2, 2, 1, 2
    1, 2, 2, 2, 2, 2, 1
    1, 2, 2, 2, 3, 1, 1
    1, 2, 2, 3, 1, 1, 2
    1, 2, 2, 3, 1, 2, 1
    1, 2, 2, 3, 2, 1, 1
    1, 2, 3, 1, 1, 2, 2
    1, 2, 3, 1, 1, 3, 1
    1, 2, 3, 1, 2, 1, 2
    1, 2, 3, 1, 2, 2, 1
    1, 2, 3, 1, 3, 1, 1
    1, 2, 3, 2, 1, 1, 2
    1, 2, 3, 2, 1, 2, 1
    1, 2, 3, 2, 2, 1, 1
    1, 3, 1, 1, 2, 1, 3
    1, 3, 1, 1, 2, 2, 2
    1, 3, 1, 1, 2, 3, 1
    1, 3, 1, 1, 3, 1, 2
    1, 3, 1, 1, 3, 2, 1
    1, 3, 1, 2, 1, 1, 3
    1, 3, 1, 2, 1, 2, 2
    1, 3, 1, 2, 1, 3, 1
    1, 3, 1, 2, 2, 1, 2
    1, 3, 1, 2, 2, 2, 1
    1, 3, 1, 2, 3, 1, 1
    1, 3, 1, 3, 1, 1, 2
    1, 3, 1, 3, 1, 2, 1
    1, 3, 1, 3, 2, 1, 1
    1, 3, 2, 1, 1, 2, 2
    1, 3, 2, 1, 1, 3, 1
    1, 3, 2, 1, 2, 1, 2
    1, 3, 2, 1, 2, 2, 1
    1, 3, 2, 1, 3, 1, 1
    1, 3, 2, 2, 1, 1, 2
    1, 3, 2, 2, 1, 2, 1
    1, 3, 2, 2, 2, 1, 1
    1, 3, 3, 1, 1, 2, 1
    1, 3, 3, 1, 2, 1, 1
    2, 1, 1, 2, 1, 2, 3
    2, 1, 1, 2, 1, 3, 2
    2, 1, 1, 2, 2, 1, 3
    2, 1, 1, 2, 2, 2, 2
    2, 1, 1, 2, 2, 3, 1
    2, 1, 1, 2, 3, 1, 2
    2, 1, 1, 2, 3, 2, 1
    2, 1, 1, 3, 1, 1, 3
    2, 1, 1, 3, 1, 2, 2
    2, 1, 1, 3, 1, 3, 1
    2, 1, 1, 3, 2, 1, 2
    2, 1, 1, 3, 2, 2, 1
    2, 1, 1, 3, 3, 1, 1
    2, 1, 2, 1, 1, 2, 3
    2, 1, 2, 1, 1, 3, 2
    2, 1, 2, 1, 2, 1, 3
    2, 1, 2, 1, 2, 2, 2
    2, 1, 2, 1, 2, 3, 1
    2, 1, 2, 1, 3, 1, 2
    2, 1, 2, 1, 3, 2, 1
    2, 1, 2, 2, 1, 1, 3
    2, 1, 2, 2, 1, 2, 2
    2, 1, 2, 2, 1, 3, 1
    2, 1, 2, 2, 2, 1, 2
    2, 1, 2, 2, 2, 2, 1
    2, 1, 2, 2, 3, 1, 1
    2, 1, 2, 3, 1, 1, 2
    2, 1, 2, 3, 1, 2, 1
    2, 1, 2, 3, 2, 1, 1
    2, 1, 3, 1, 1, 2, 2
    2, 1, 3, 1, 1, 3, 1
    2, 1, 3, 1, 2, 1, 2
    2, 1, 3, 1, 2, 2, 1
    2, 1, 3, 1, 3, 1, 1
    2, 1, 3, 2, 1, 1, 2
    2, 1, 3, 2, 1, 2, 1
    2, 1, 3, 2, 2, 1, 1
    2, 2, 1, 1, 2, 1, 3
    2, 2, 1, 1, 2, 2, 2
    2, 2, 1, 1, 2, 3, 1
    2, 2, 1, 1, 3, 1, 2
    2, 2, 1, 1, 3, 2, 1
    2, 2, 1, 2, 1, 1, 3
    2, 2, 1, 2, 1, 2, 2
    2, 2, 1, 2, 1, 3, 1
    2, 2, 1, 2, 2, 1, 2
    2, 2, 1, 2, 2, 2, 1
    2, 2, 1, 2, 3, 1, 1
    2, 2, 1, 3, 1, 1, 2
    2, 2, 1, 3, 1, 2, 1
    2, 2, 1, 3, 2, 1, 1
    2, 2, 2, 1, 1, 2, 2
    2, 2, 2, 1, 1, 3, 1
    2, 2, 2, 1, 2, 1, 2
    2, 2, 2, 1, 2, 2, 1
    2, 2, 2, 1, 3, 1, 1
    2, 2, 2, 2, 1, 1, 2
    2, 2, 2, 2, 1, 2, 1
    2, 2, 2, 2, 2, 1, 1
    2, 2, 3, 1, 1, 2, 1
    2, 2, 3, 1, 2, 1, 1
    2, 3, 1, 1, 2, 1, 2
    2, 3, 1, 1, 2, 2, 1
    2, 3, 1, 1, 3, 1, 1
    2, 3, 1, 2, 1, 1, 2
    2, 3, 1, 2, 1, 2, 1
    2, 3, 1, 2, 2, 1, 1
    2, 3, 2, 1, 1, 2, 1
    2, 3, 2, 1, 2, 1, 1
    3, 1, 1, 2, 1, 1, 3
    3, 1, 1, 2, 1, 2, 2
    3, 1, 1, 2, 1, 3, 1
    3, 1, 1, 2, 2, 1, 2
    3, 1, 1, 2, 2, 2, 1
    3, 1, 1, 2, 3, 1, 1
    3, 1, 1, 3, 1, 1, 2
    3, 1, 1, 3, 1, 2, 1
    3, 1, 1, 3, 2, 1, 1
    3, 1, 2, 1, 1, 2, 2
    3, 1, 2, 1, 1, 3, 1
    3, 1, 2, 1, 2, 1, 2
    3, 1, 2, 1, 2, 2, 1
    3, 1, 2, 1, 3, 1, 1
    3, 1, 2, 2, 1, 1, 2
    3, 1, 2, 2, 1, 2, 1
    3, 1, 2, 2, 2, 1, 1
    3, 1, 3, 1, 1, 2, 1
    3, 1, 3, 1, 2, 1, 1
    3, 2, 1, 1, 2, 1, 2
    3, 2, 1, 1, 2, 2, 1
    3, 2, 1, 1, 3, 1, 1
    3, 2, 1, 2, 1, 1, 2
    3, 2, 1, 2, 1, 2, 1
    3, 2, 1, 2, 2, 1, 1
    3, 2, 2, 1, 1, 2, 1
    3, 2, 2, 1, 2, 1, 1
    3, 3, 1, 1, 2, 1, 1
    

