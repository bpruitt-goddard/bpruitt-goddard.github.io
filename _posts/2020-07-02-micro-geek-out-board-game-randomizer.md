---
layout: post
title: Micro Geek Out - Board Game Randomizer
category: Productivity
tags: [geeking-out, side-projects]
---

There was a board game recently [(re)released](https://www.boardgamegeek.com/boardgame/256999/project-elite) that provided a great opportunity for [geeking out]({% post_url 2018-12-08-being-a-geek-with-limited-time %}). Reading like an interview problem, it has a clear set of inputs and outputs that can all be solved in a few lines of code.

# Problem
When setting up the game, a set of 8 Event cards must be selected. These cards are chosen from a pool of 25, with some being picked randomly from a list and others always being used. Below is the chart indicating which cards are chosen for each of the three difficulties.

| Card #    | Easy | Medium | Hard |
|-----------|------|--------|------|
|  1 to 16  | -    | 3      | 4    |
| 17 to 21  | 5    | 2      | -    |
| 22 and 23 | 2    | 2      | 2    |
| 24        | 1    | 1      | 1    |
| 25        | -    | -      | 1    |

<br />
For example, in `Medium` difficulty, 3 cards must be selected from the first 16. _Note_ that based on the logic, `Easy` will always include the same cards.

To make the setup easier and keep the cards sorted in the game, a simple script can be written to select the 8 cards based upon the above logic. They can then be retrieved from the sorted deck of cards. Thus, the problem is: 
> Given a difficulty, select 8 cards to use from the above chart.

# Solution
The main problem in this logic is determining how to randomly choose a _set_ of cards from a random range. Specifically, it needs to ensure that each number retrieved is unique.

The first attempt was to keep track of the list of possibilities and pop/remove one randomly for each needed card. This gets messy as it involves tracking the list of numbers for each iteration of the loop.

Thinking outside the box slightly, the solution can be simplified by _pre-scrambling_ the list. Take the list of possible numbers and randomize it. Then pull as many off the list as needed. This has the benefit of removing all logic from the "loop", and greatly simplifying the overall approach. Using a shuffle algorithm found on [Stack Overflow](https://stackoverflow.com/a/6274381/459018), the logic can be reduced to:

```javascript
function drawXFromList(numToDraw, numRange) {
        return shuffle(numRange)
            .slice(0, numToDraw);

        // Fisher-Yates shuffle algorithm
        function shuffle(a) {
            for (let i = a.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [a[i], a[j]] = [a[j], a[i]];
            }
            return a;
        }
    }
```

With the above function in hand, the rest of the work was simple. The resulting page is embeded below:

<iframe id="dashBody" 
    src="/assets/html/project-elite-deck-generator.html"
    height="400px"
    style="background-color: white;" />
