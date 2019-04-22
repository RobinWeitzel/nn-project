---
layout: post
title:  "Encoding a Move - Part 2"
date:   2019-04-20 12:53:00 +0200
mathjax: true
tags: project encoding
---
Nowadays most chess engines play much better than any human player. 
Engines like [Stockfish](https://stockfishchess.org/) and [AlphaZero](https://deepmind.com/blog/alphazero-shedding-new-light-grand-games-chess-shogi-and-go/) have been in development for years and part of that was figuring out a good way to represent a board.
A fairly comprehensive list of representations can be found [here](https://www.chessprogramming.org/Board_Representation).

Critically, not all of these are suitable for our project. In fact, most of the are not.
As part of each move, chess engines have to make numerous checks like if a piece is giving check.
Being able to calculate these things in a computationally efficient way is key since competitive chess is played on a timer.
Thus, most modern board representations are designed to allow for such quick checks (i.e. needing only one bitwise operation to determine if diagonal is open [[1]](https://www.chessprogramming.org/0x88)).

For us, such things are irrelevant. Instead, we are looking for the representation that is best understood by the computer.
in theory, a neural network can understand any notation but the more complex the representation, the more data and time will be needed just to teach this representation to the network. Since we have only very limited amounts of data this is not an option.

### The board as a matrix
In the previous chapter I said the chess board is made up of 8x8 chequered squares.
This sounds like a matrix, why not represent the board as one?
Something like this where the number stands for a certain piece and $ i_11 $ maps to the top left of the board:
$$ \begin{bmatrix} -5 & -2 & -3 & -7 & -9 & -3 & -2 & -5 \\ -1 & -1 & -1 & -1 & -1 & -1 & -1 & -1 \\ 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ 1 & 1 & 1 & 1 & 1 & 1 & 1 & 1 \\ 5 & 2 & 3 & 7 & 9 & 3 & 2 & 5 \end{bmatrix} $$
Even better, we can convert the categorical values for each piece into one-hot-encoded vectors.
Depending on whether we:
* see the black and white pieces as different vectors (i.e. $ white pawn = [1 0 0 ...] $, $ black pawn = [0 1 0 ...] $)
* or just take the negative value of each white piece vector for the black piece (i.e. $ [1 0 0 ] $ and $ [-1 0 0] $)
we get a matrix of 8x8x12 (8x8x6).

This representation looks very similar to how we represent images.
Instead of having three color channels, we have 12.
These parallels look very promising considering how success full neural networks are at generating captions for images.
![image as matrix](../img/Color-image-representation-and-RGB-matrix.png "[Source](https://www.researchgate.net/publication/282798184_The_project_is_based_on_emerging_field_Image_Processing_In_this_project_A_Graphical_User_Interface_has_been_designed_using_the_software_Labwindows_which_can_process_both_type_of_Real_time_Image_Proces)")


