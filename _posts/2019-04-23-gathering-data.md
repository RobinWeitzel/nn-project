---
layout: post
title:  "Gathering Data"
date:   2019-04-23 9:40:00 +0200
tags: project data
---
Now that we have decided on how we will encode the data we need to start looking for data sets.

### Games without explanations
Thankfully, the invention of the internet and subsequent rise of online chess tournaments has made it very easy to find game recordings.
One prominent example is the [Free Internet Chess Server (FCIS)](https://www.ficsgames.org/download.html).
They provide almost 340 million games, 21 million of which are standard chess games (the rest being mostly blitz games).
Furthermore, it is possible to only download games from good players (rating > 2000).
This makes for a perfect source.

Games, grouped by month or year, can be downloaded as a single file in the [pgn format](https://en.wikipedia.org/wiki/Portable_Game_Notation).
This format contains metadata about each game as well as a list of moves in the algebraic notation.
Have a look at [this](https://github.com/RobinWeitzel/nn-project/blob/master/data_exploration.ipynb) Jupyter notebook to see how I converted the games from pgn format into the two representations we chose in the last chapter.

Because the FCIS provides so many games I see no reason to look for other sources.
If I need more data, I can just download the games from another year.

### Games with explanations
Finding games where each move is explained is much trickier.
There are numerous great YouTube channels in which games are analyzed move for move (I can especially recommend [this](https://www.youtube.com/user/ChessNetwork) one). Unfortunately, it is very difficult to extract these explanations from a video.
Thus, I stated searching for game explanations in written form.

In theory, the pgn-format allows comments between each move.
Looking at different chess forums I was able to find some games but quickly grew worried that I would have to spend a lot of time piecing together a data set from hundreds of forum posts. Fortunately, a kind soul already did this and provided all the links in one [place](http://www.angelfire.com/games3/smartbridge/).
All in all this resulted in over 4000 annotated games.

Looking at the games in more detail quite a few of them are not formatted correctly.
After filtering those out we are left with 3683 games.
Opening up one of these games we see something like this:

~~~
1. e4 c5
{ Although Shirov has played a variety of defenses, I think he's been playing the Petroff's Defense and perhaps the Caro-Kann most recently. }
2. Nf3 Nc6 3. d4 cxd4 4. Nxd4 e5
{ Neo-Sveshnikov or Lowenthal leaves d5 (and perhaps d6) weak. More common is 4...Nf6, forcing White to defend Pe4 and threatening ...d5. }
5. Nb5 d6 6. N1c3
{ He threatens Nc3-d5 and Nc7+, so Black must nip that idea in the bud. }
 ( 6. Bc4 ) ( 6. c4 ) 6...a6 7. Na3 b5
{ Instead of White dominating square c4 it's Black who controls it, for the moment. Eventually piece play should dominate a position more than pawns. There is also the immediate threat of ...b4. }
8. Nd5
{ White could continue with Bc1-e3-b6 and Nc7+, so Black has to react quickly. }
8...Nce7 9. c4 Nxd5 10. exd5 $5
{ This makes the pawns a bit unbalanced with White advancing on the queen-side. Aside from Pb5 Black shouldn't have much difficulty completing his development. }
~~~

Some moves are trailed by comments in brackets but not all moves are.
Therefore, the next step is to only extract commented moves (since out neural network can only learn from these).
Check out [this](https://github.com/RobinWeitzel/nn-project/blob/master/data_exploration.ipynb#part2) Jupyter notebook to see how I did it.
We are left with 17615 commented moves which is hopefully enough since getting more games would take a lot of time I would rather spend on training the network.

Taking a look at the comments, we notice a few things:
* comments often contain the name of a player or his color
* some comments don't relate to the latest move
* many comments are complex explanation over multiple sentences
* comments frequently point to specific squares (which are mentioned by name)

All of this will make it more difficult to generate useful captions.
I will probably need to preprocess the comments (i.e. remove/replace names).
However, for a first try this should be good enough.
In the next chapter I will use the data set without annotations in combination with Autoencoders to teach the encodings to our network.