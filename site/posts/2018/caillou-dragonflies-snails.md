I have a "Caillou Dragonflies & Snails" board game at home. It's an adaptation
of the [Snakes and Ladders](https://en.wikipedia.org/wiki/Snakes_and_Ladders)
game. This game comes with one regular die and one colored die. The rules
describe two ways to play the game.

  1. When playing with a regular die a player throws and advances the number of squares indicated on the die.
  2. When playing with a colored die a player throws and advances to the next closest square of the color indicated by the colored die.

When I play with my daughter, I always pick the regular die and my daughter
picks the colored die. After a few such games it became clear that I loose too
often. My daughter also noticed that effect and theorized that it was due to
her extraordinary luck. But I started to suspect that it was related to the
choice of a die.

Did the game designers conspire with Caillou animators to make children
luckier than their number literate parents? Continue reading to find out the
truth.

![](/images/caillou-dragonflies-snails.jpg)

This game has four different types of special squares. Each square has a
number and a color.

  * A "Snail" and a "Dragonfly" squares redirect the player to a different location on the board.
  * A "Lucky clover" square instructs the player to play again.
  * A "Spiderweb" square instructs the player to skip one turn.
  * The "End" square is the last square on the board and it has 4 colors.

To win the game a player must reach the "End" square first. In the colored die
mode a player must obtain one of the "End" square colors to be a able to reach
it. When playing with a regular die the "End" square is reached with an exact
throw.

I'm using the letters "y", "b", "r", "g", "p" and "B" for colors yellow, blue,
red, green, purple and black respectively.

    
```python    
from collections import namedtuple

Square = namedtuple('Square', ('number', 'colors', 'increment', 'goto'))

board = [Square(number=0, colors='y', increment=1, goto=0),
         Square(number=1, colors='b', increment=1, goto=1),
         Square(number=2, colors='r', increment=1, goto=2),
         Square(number=3, colors='g', increment=1, goto=22),   # Dragonfly
         Square(number=4, colors='y', increment=1, goto=4),
         Square(number=5, colors='p', increment=1, goto=5),
         Square(number=6, colors='g', increment=1, goto=6),
         Square(number=7, colors='b', increment=1, goto=17),   # Dragonfly
         Square(number=8, colors='r', increment=1, goto=8),
         Square(number=9, colors='B', increment=2, goto=9),    # Spiderweb
         Square(number=10, colors='y', increment=1, goto=10),
         Square(number=11, colors='p', increment=1, goto=11),
         Square(number=12, colors='r', increment=1, goto=2),   # Snail
         Square(number=13, colors='b', increment=1, goto=13),
         Square(number=14, colors='B', increment=0, goto=14),  # Lucky clover 
         Square(number=15, colors='p', increment=1, goto=15),
         Square(number=16, colors='y', increment=1, goto=16),
         Square(number=17, colors='b', increment=1, goto=17),
         Square(number=18, colors='g', increment=1, goto=18),
         Square(number=19, colors='y', increment=1, goto=19),
         Square(number=20, colors='r', increment=1, goto=20),  
         Square(number=21, colors='B', increment=2, goto=21),  # Spiderweb
         Square(number=22, colors='g', increment=1, goto=22),
         Square(number=23, colors='b', increment=1, goto=23),
         Square(number=24, colors='r', increment=1, goto=24),
         Square(number=25, colors='p', increment=1, goto=43),  # Dragonfly
         Square(number=26, colors='g', increment=1, goto=26),
         Square(number=27, colors='y', increment=1, goto=16),  # Snail
         Square(number=28, colors='B', increment=0, goto=28),  # Lucky clover
         Square(number=29, colors='g', increment=1, goto=29),
         Square(number=30, colors='r', increment=1, goto=30),
         Square(number=31, colors='B', increment=0, goto=31),  # Lucky clover
         Square(number=32, colors='y', increment=1, goto=32),
         Square(number=33, colors='b', increment=1, goto=23),  # Snail
         Square(number=34, colors='g', increment=1, goto=34),
         Square(number=35, colors='r', increment=1, goto=35),
         Square(number=36, colors='p', increment=1, goto=36),
         Square(number=37, colors='y', increment=1, goto=37),
         Square(number=38, colors='b', increment=1, goto=38),
         Square(number=39, colors='p', increment=1, goto=39),
         Square(number=40, colors='r', increment=1, goto=40),
         Square(number=41, colors='g', increment=1, goto=29),  # Snail
         Square(number=42, colors='B', increment=2, goto=42),  # Spiderweb
         Square(number=43, colors='p', increment=1, goto=43),
         Square(number=44, colors='y', increment=1, goto=44),
         Square(number=45, colors='b', increment=1, goto=45),
         Square(number=46, colors='r', increment=1, goto=46),
         Square(number=47, colors='g', increment=1, goto=47),
         Square(number=48, colors='B', increment=0, goto=48),  # Lucky clover
         Square(number=49, colors='p', increment=1, goto=49),
         Square(number=50, colors='y', increment=1, goto=50),
         Square(number=51, colors='ybrg', increment=1, goto=51)]
```    

## Simulating

Following is the code to play the game as described by the rules. The function
`play_regular()` implements a game with a regular die. The function
`play_color()` implements a game with a colored die. Both functions are
generators yielding `(throw, location, increment)` tuples.

### Regular die

    
```python
import random

def play_regular(board):
    iloc = 0
    finished = False
    while not finished:
        # Throw a regular die
        r = random.randint(1, 6)
        
        # Land exactly on a finishing square
        if iloc + r == board[-1].number:
            finished = True
            iloc = board[-1].number
            increment = board[iloc].increment
        
        # Land beyond the End square
        elif iloc +r &gt; board[-1].number:
            iloc = iloc
            increment = 1
        
        # Advance to a square and follow all the rules
        else:
            iloc = board[iloc + r].goto
            increment = board[iloc].increment
        
        yield r, iloc, increment
```    

### Colored die

    
```python    
def play_color(board):
    iloc = 0
    finished = False
    while not finished:
        # Throw a colour die
        random_color = random.choice('ybrgpB')
        
        # Advance to the next square with a matching color
        r = iloc
        while r &lt; board[-1].number:
            r += 1
            
            # Reached the &quot;End&quot; square
            if r == board[-1].number and random_color in board[-1].colors:
                iloc = r
                increment = 1
                finished = True
                break
            
            # Move to the next square of a randomly selected color
            if random_color in board[r].colors:
                iloc = board[r].goto
                increment = board[r].increment
                break
                
        # No valid move was found. Skip your turn.
        else:
            iloc = iloc
            increment = 1

        yield random_color, iloc, increment 
```    

## Sampling

Now we can play many games and calculate the averages of moves for regular and
colored games.

    
```python  
import statistics

def sample_nb_moves(board, play, n=100000):
    'Play `n` games and return then number of moves for each game'
    games = [play(board) for i in range(size)]
    return [sum(increment for _, _, increment in moves) for moves in games]

print ("A regular die game takes on average {:.1f} moves".
        format(statistics.mean(sample_nb_moves(board, play_regular))))
print ("A colored die game takes on average {:.1f} moves.".
        format(statistics.mean(sample_nb_moves(board, play_color))))
```    

## Results

    
    
    1 A regular die game takes on average 18.1 moves.
    2 A colored die game takes on average 12.7 moves.
    

This clearly shows that a player with a colored die has more chances than a
player with a regular die, thus confirming the Caillou conspiracy theory. I
knew it!

On a more serious note, I think that giving an advantage for the colored die
is unfortunate because it discourages the kids to learn to count. I think that
the odds must be equal or, better yet, reversed. Otherwise the message sent to
the kids is "Don't bother learning to count, because your probability of
winning will be higher if you play by color."

Can you find a set of minimal changes to the board to "correct" this game?

