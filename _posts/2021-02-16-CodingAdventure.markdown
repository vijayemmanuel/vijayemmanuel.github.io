---
layout: post
title: Coding Adventures with Party Parrot
date: 2021-02-16 19:20:23 +0900
category: general
---

![PartyParrot](/assets/general/partyparrot.jpg){:class="img-responsive"}

This puzzle appeared on https://coding-adventures.sml.io/game hosted by SoftwareMill

### Task 1 - Warsaw

At last! After all the time spent meeting everybody online, we can’t wait to embark on a journey to experience the conference vibe again. Remember Scalar 2019? Do you miss the atmosphere? Let’s go there with Party Parrot. Help her get ready and pack.

The Parrot is not strong enough to carry everything with her. Each item the Parrot would like to take has a given weight and a value. Unfortunately, weight that she can carry is limited, so she can’t take everything she wants.

Your task is to help the Parrot calculate the maximum possible value of the items she could carry.

```
Sample input data
7
1 1
2 6
3 10
5 16
```

Data description
A backpack with maximum allowed weight 7, 4 items with weights: 1, 2, 3, 5 and values (accordingly): 1, 6, 10, 16

```
Sample output data
22
```

Data description
The maximum value of 22 can be obtained by packing the second (2 6) and fourth (5 16) item.

```
Your input data
234
86 33
78 8
74 59
60 41
71 81
70 38
26 79
24 15
88 5
17 23
83 84
```

```
Your answer

First order the items in ascending order

88 5
78 8
24 15
17 23
86 33
70 38
60 41
74 59
26 79
71 81
83 84

We can find the maxiumum weight with the following values
24	15
17	23
26	79
71	81
83	84

Total items 15 + 23 + 79 + 81 + 84 = 282

``` 
------

### Task 2 - London
s
Magically, the wild parrot has appeared in London! Before we head out to ScalaX, let’s meet fellow programmers in a local pub and share a pint. Help Party Parrot remain inside the pub and enjoy geeky convos after a few pints.

Let’s assume the pub is a 10x10 board and the Parrot starts the party on a given field (X, Y) (row X, column Y, starting from top left, indexed from 1). The Parrot can move randomly in one of four directions (up, down, right, left) with an equal probability for each direction (0.25). The goal of your program is to calculate the probability of the Parrot leaving the board after N random moves. The solution should be a single floating point number with accuracy of exactly three decimal places.

```
Sample input data
4 2 5
```

Data description
Starting field is (4, 2) and the Parrot performs 5 moves.

```
Sample output data
0.238
```

Data description
The probability of the Parrot leaving the board, with accuracy of three decimal places.

```
Your input data
8 5 15
```

```
N = 10
 
# Direction vector for the Knight
dx = [1, 0, 0, -1]
dy = [0, 1, -1, 0]
 
# returns true if the knight
# is inside the chessboard
 
def inside(x, y):
    return (x >= 0 and x < N and y >= 0 and y < N)
 
# Bottom up approach for finding the
# probability to go out of chessboard.
 
def findProb(start_x, start_y, steps):
 
    # dp array
    dp1 = [[[0 for i in range(N+15)] 
            for j in range(N+15)]
            for k in range(steps + 15)]
 
    # For 0 number of steps, each
    # position will have probability 1
    for i in range(N):
        for j in range(N):
            dp1[i][j][0] = 1
 
    # for every number of steps s
    for s in range(1, steps + 1):
 
        # for every position (x,y) after
        # s number of steps
        for x in range(N):
 
            for y in range(N):
                prob = 0.0
 
                # For every position reachable from (x,y)
                for i in range(4):
                    nx = x + dx[i]
                    ny = y + dy[i]
 
                    # if this position lie inside the board
                    if (inside(nx, ny)):
                        prob += dp1[nx][ny][s-1] / 4.0
 
                # store the result
                dp1[x][y][s] = prob
 
    # return the result
    return dp1[start_x-1][start_y-1][steps]
 
# Driver code
 
# number of steps
K = 15
 
# Function Call
print ("Probibility of piece lying inside : " , findProb(8, 5, K))

print ("Probibility of piece lying outside : ", 1 - findProb(8, 5, K))


Final Answer : 
0.360
```

----

### Task 3 - Lausanne

Party Parrot is a little bit dizzy from the after party with the London Scala community. The phone rings. She needs to make it to Scala Days tomorrow! She will travel together with John de Goes, Valentin Kasas and Adam Warski. Help them decide which tube station is the most convenient starting point for their journey.You’re given a list of places where the speakers are staying with their GPS coordinates and a list of tube stations – also with their coordinates. The goal of your program is to calculate which tube station would be the closest one to all of them, where closest means that the sum of the distances that everyone has to travel to the station is the lowest possible. For the GPS coordinates you can assume that they are all in the northern hemisphere (N) and east of Greenwich (E). The coordinates are represented as decimal degrees. The solution should be a single capital letter.

```
Sample input data
45.59848 41.66455
41.47243 48.99
45.06939 43.77605

A 45.05055 48.52611
B 44.42962 43.03904
C 40.61444 41.4761
D 49.19854 44.44444
E 49.17351 49.6315
```

Data description
The first set of lines before the blank one are the coordinates of the speakers. The second set after the blank line are the coordinates of the tube stations preceded by their names.

```
Sample output data
B
```

Data description
The name of the closest tube station.

```
Your input data
46.55735 6.59908
46.57565 6.61936
46.5461 6.67947

A 46.56291 6.71306
B 46.52034 6.65557
C 46.53136 6.64018
D 46.52694 6.66658
E 46.54172 6.59267
```

```
Your answer:
	Lat1	    Long1				
	46.55735	6.59908				
	46.57565	6.61936				
	46.5461	    6.67947		

	Lat2	    Long2	Distance 1	Distance 2	Distance 3	Sum
A	46.56291	6.71306	8736.452298	7301.539466	3176.601234	19
B	46.52034	6.65557	5966.964246	6744.836531	3398.098144	16
C	46.53136	6.64018	4269.906376	5175.750079	3423.072985	12
D	46.52694	6.66658	6171.384648	6509.589647	2347.575846	15
E	46.54172	6.59267	1805.779135	4289.374196	6656.2786	12

Take the minimum Sum which is C here.
```
------

### Task 4 - Rome:

The da Vinci’s secret notes include some arithmetic expressions written in a strange fashion, e.g. “1 2 + 3 *”. After looking at the side notes, you have already learned that the expressions are using a postfix notation called RPN (or Reverse Polish Notation). Your goal is to help the Parrot calculate the value of a given expression - the solution should be a single floating point number with accuracy of exactly two decimal places.;

```
Sample input data
1 2 + 3 *
```

Data description
Decimal numbers and operators: +, -, *, /, separated by single whitespaces.

```
Sample output data
9
```

Data description
The result of evaluating the given expression, which is the equivalent of (1+2)*3, with accuracy of two decimal places

```
Your input data
54.38 14.84 + 50.29 * 21.78 38.39 + 0.90 - *
```

```
Your answer
54.38	14.84	+	50.29	*	21.78	38.39	+	0.9	-	*
69.22	50.29	*								
3481.0738	21.78	38.39	+	0.9	-	*				
3481.0738	60.17	0.9	-	*						
3481.0738	59.27	*								
206323.24

Final answer 206323.24

```
-----

### Task 5 - Porto:

After participating in Scala Swarm, Party Parrot jumps on the Atlantic cruise from Porto to New York. There’s no better way of traveling than cruising on the sea. Such an enchanting escape helps relax and rejuvenate. Or does it? Help Party Parrot navigate the seas!

The cruise is divided into segments, where every segment is a line between two given waypoints with integer coordinates, with (0, 0) at the bottom left. The bearing (B) is the compass direction towards which the yacht is sailing, i.e. the angle (in degrees) between the North and the direction of sailing, measured clockwise, where North is the direction of the Y axis from (0, 0) upwards.
When sailing in a given direction, the yacht is drifted off by the wind. The drift (D) is the angle that you need to add/subtract to/from the original bearing to obtain the Course Over Ground (COG), i.e. the effective direction of sailing. Whether to add or subtract depends on the side of the yacht from which the wind blows – when it blows from the left (when the observer is looking forward), you add the drift (COG = B + D), when it blows from the right – you subtract (COG = B - D). When the wind blows directly from the bow or from the stern, it doesn’t cause a drift, so D would be 0 in such cases.

Note that the side from which the wind blows depends both on the bearing and on the wind direction. The wind direction is the compass direction from which the wind blows, e.g. 90 degrees means a wind from E, 270 degrees means a wind from W. When the wind blows from 90 degrees (E), it will be from the right side when the bearing is 45 degrees, but from the left side if the bearing is 190 degrees.

The goal of your program is to calculate the COGs for a given segment. A segment consists of the coordinates of the start and end waypoints, the direction of the wind and the value of the drift. The solution should be a single integer number representing the COG (thus in range [0, 359]).

```
Sample input data
5 5 5 10 45 3
```


Data description
A segment starting at (5, 5), ending at (5, 10), wind direction is 45 degrees, drift is 3 degrees.

```
Sample output data
357
```

Data description
The Course Over Ground for the given segment.

```
Your input data
23 32 26 14 140 4
```

```
Your answer:

Slope (m) =	ΔY/ΔX =	-6/1 = -6
θ =	arctan(	ΔY/ΔX ) + 360° (wrt X axis) = 279.46°
wrt Y Axis upward = 171°

Since wind is 140° which is < 171°, it is blowing from left (observer looking forward)

So drift = 171° + 4° = 175°

```

-----
### Task 6 - New York:

There’s no better way of traveling than cruising on the sea. Such an enchanting escape helps relax and rejuvenate. Help Party Parrot go on an unforgettable adventure and plan her Atlantic cruise from Portugal to New York where she is participating in NEscala.;

```
Sample input data
1 5
1 2 1
1 3 2
1 4 3
3 5 5
4 3 4
4 5 1
```

Data description
The cheapest route from city 1 to city 5. The cost of traveling from city 1 to city 2 is 1. From 1 to 3 it’s 2, from 1 to 4 it’s 3 etc.

```
Sample output data
4
```

Data description
The cost of the cheapest route from city 1 to city 5, i.e. 1 -> 4 -> 5


```
Your input data
6 4
32 33 9
32 34 8
32 35 11
2 4 5
2 31 20
34 35 18
31 33 9
31 32 19
1 2 6
1 6 6
1 31 3
35 4 20
33 34 9
5 4 20
6 31 6
6 5 20
```

```
Your answer
6 -> 5 -> 4 = 20 + 20 = 40
```

----
### Task 7 - San Francisco

At last Party Parrot reached the place where the largest Scala conference in the world – Scale By the Bay – happens. In between listening about monads and why Scala is the best language to play with, she decided to catch some ocean breeze and visit all 4 bridges that are the landmarks of the city. In this task your goal is to help the Parrot decide whether she would be able to take a walk in a city with a given network of bridges so that she visits each bridge exactly once and that she finishes the walk in the district she started it. You are given a list of districts and – for each district – another list of districts to which the initial one is connected by a bridge, e.g. “A: C, D“ means that there’s a bridge from A to C and from A to D. The bridges are bi-directional, i.e. if there’s a bridge from A to C, the Parrot can also walk over it from C to A. Note that there can be more than one bridge between two given districts, so an input like “A: B, B, C” is also valid. ;

```
Sample input data
A: B, B, C, C, D
B: D
C: D
```

Data description
Two bridges from A to B Two from A to C One from: A to D, B to D, C to D

```
Sample output data
false
```

Data description
Because it’s not possible to walk through each of the bridges exactly once.

```
Your input data
E: G, G, D, C
F: G, A
G: F, E, F
C: D, F, A
A: G
D: C

G: F, B
D: C
B: G
A: D, C, D, C
C: G
E: C, F, C

```

```
Your answer

Visual representation:
          
    B === G --------|
          |         |
A -- D -- C -- E -- F
|____|    |    |
|_________|    |
|_________|    |
          |____|


false
```

----

### Task 8 - Tokyo

While visiting Tokyo, Party Parrot couldn’t let go of a chance and beside joining Scala Matsuri she rushed to AnimeJapan. She tried Scala cat cosplay and visited amazing booths, one of them was a maze and to her surprise, one was a chessboard! The Parrot found herself on a chessboard of an arbitrary size. She’s only allowed to move like the chess knight does, i.e. two fields vertically and one horizontally, or two fields horizontally and one vertically (https://en.wikipedia.org/wiki/Knight_(chess)#Movement). Your goal is to help the Parrot know whether she would be able to visit each field on a chessboard with the given dimensions exactly once, so that she completes her tour on the starting position. The solution should be a boolean string (“true”/”false”), depending on whether the Parrot would be able to visit each field exactly once.

```
Sample input data
3 4
```

Data description
A chessboard with 3 rows and 4 columns.

```
Sample output data
false
```

Data description
Because it’s impossible to visit each field exactly once.

```
Your input data
62 83
or
47 16
```

```
Your answer
true
```


-----

### Task 9 - Mars

We’ve been all around the world with Party Parrot, that was a blast! Your last quest in this journey is to search for life on Mars. The red planet must have some traces of functional programming beings, right? Let’s continue to spread the love for monads and functors to the whole universe beyond Earth. Help Party Parrot configure the parameters of the space rocket so that she can safely land on Mars. For her final journey the Parrot uses a spaceship which weighs 120 tons and is about to land on the surface of Mars. The spaceship approaches Mars with a vertical speed v0 (in m/s), but to land safely the speed needs to be no more than v (in m/s) when the spaceship touches the surface. The speed needs to be reduced to the safe value no later than after t seconds. Assuming the gravitational acceleration near the surface of Mars to be 3.7 m/s^2, the goal of your program is to calculate the minimum force F (in Newtons) to which the engines need to be set in order for the spaceship to land safely. Assume that the engines are directed perpendicular to the surface. The solution should be a single floating point number with accuracy of exactly two decimal places.;


```
Sample input data
50.3 0.7 42.1
```

Data description
Initial speed v0 [m/s] safe landing speed v [m/s] maximum slowing down time t [s]

```
Sample output data
585377.67
```

Data description
The minimum engine force F [N] required to slow down from v0 to v in time t.

```
Your input data
51.50 0.38 32.45
or 
87.80 0.77 64.64
``` 

```
Your answer

v = u + at
v = 51.5 + 3.7 * 32.35 = 171.565

To get the v final of 0.38 we need to decelerate at (171.565 - 0.38) / 32.45 = 5.275346

So Force F = 120000 * 5.275346 = 633041.60

Final Answer:
633041.60
```

### Party Parrot is HAPPY

![PartyParrotThumbsUp](/assets/general/thumbsupparrot.gif){:class="img-responsive"}


