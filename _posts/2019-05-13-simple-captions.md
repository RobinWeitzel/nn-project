---
layout: post
title:  "Simple Captions"
date:   2019-05-13 13:11:00 +0200
tags: project encoding model caption
---
In this post I train a neural network to say what the last move was by showing it the board state before and after the move.
This task can easily be done by traditional, non-neural chess engines.
However, it showcases that our model can not just recite a chess board (see last post for more info about this) but also interpret it.
That's critical for our last step, generating explanations for moves.

### Data preparation
The input data to our model will be the board state before and after a move.
In addition, we will input the words that have been generated so far and predict the next word (marked in blue).

![input format](../img/post_6_vis_1.PNG)

Fortunately, we can combine the move lists (that we initially generated as a form of representation) with our matrix notation to generate data in the required format.
Because the move list uses the algebraic notation (i.e. Be4) but we want a sentence (i.e. Bishop to e4) I wrote a transformer:

~~~ python
def pieceToWord(notation, verbose=False):
    '''
    Returns a short sentence describing a piece and its position
    '''
    
    pieces = {
        "B": "Bishop",
        "R": "Rook",
        "K": "King",
        "Q": "Queen",
        "N": "Knight"
    }
    
    length = len(notation)
    
    if length == 0:
        return "Pawn"
    
    if length == 1:
        if notation.islower(): # Pawn
            if verbose:
                return "Pawn from file " + notation
            else:
                return "Pawn"
        else: # Higher piece
            return pieces[notation]
    
    piece = pieces[notation[0]]
    
    if not verbose:
        return piece
    
    if length == 2:
        if notation[1].isdigit(): # rank
            return piece + " from rank " + notation[1]
        else: # file
            return piece + " from file " + notation[1]
    
    if length == 3: # rank and file
        return piece + " from square " + notation[1:] 

def moveToSentence(move, verbose=False):
    '''
    Translates a move in the algebraic notation into a caption.
    '''
    
    pieces = {
        "B": "Bishop",
        "R": "Rook",
        "K": "King",
        "Q": "Queen",
        "N": "Knight"
    }
    
    action = " to " # Default action is move
    modifier = "" # Default no modifier
    
    if "+" in move: # Check
        if verbose:
            modifier = " with check"
        move = move[:-1]
    
    if "#" in move: # Checkmate
        if verbose:
            modifier = " with checkmate"
        move = move[:-1]
        
    if "=" in move: # Pawn promotion
        if verbose:
            modifier = " with promotion to " + pieces[move[-1:]].lower() + modifier
        move = move[:-2]
        
    if "e.p." in move: # En passant
        move = move.replace('e.p.', '')
        if verbose:
            return  " en passant" + modifier
    
    # Special cases
    if "O-O" == move: # Kingside caste
        return "Kingside castle" + modifier
    
    if move == "O-O-O": # Queenside castle
        return "Queenside castle" + modifier    
        
    # extract final position
    position = move[-2:]
    move = move[:-2]
        
    if "x" in move: # Capture
        action = " takes on "
        move = move[:-1]
        
    piece = pieceToWord(move, verbose)
    
    return piece + action + position + modifier + " <End>"
~~~

This yielded a dataset of around 5.5 million moves with captions.
Next, I further separated this dataset to so that each word is shown as the output once.

![data format 2](../img/post_6_vis_2.PNG)

We end up with 24 million annotated moves.

### Model
As mentioned above, our model has three inputs:
* the board state before the move
* the board state after the move
* all words of the sentence before the word we are trying to generate

To encode the board states we use the pretrained Encoder from the last chapter.
The sentence is embedded and fed through a GRU layer.
Two linear layers followed by a softmax predict the next word.

![model architecture](../img/post_6_vis_3.PNG)

After training for one epoch (which took a few hours) the model achieved a validation accuracy of 93%.
You may have noticed that the above mentioned transformer has two settings.
A simple one (rook takes on e4) and an advance one (rook takes on e4 with check).
The advanced captions require understanding of the rules of chess (i.e. what is check).
Generating theses captions the model still achieved an accuracy of 91%.
It thereby demonstrates the ability to induce the rules of chess without being explicitly told them.

These results are very encouraging.
The generated captions were understandable and made sense.
Furthermore, the network learned at least some of the rules which is key in order to analyze moves.
Without it, you can not explain why a move is good or bad.
However, I grew concerned that, even though we used the pretrained embedding from the Autoencoder, the network now ignored most of the pieces on board.
After all, to say "rook takes on e4 with check" you only need to know three things:

* a rook moved to e4
* e4 was already occupied by a piece
* the king is given check from e4

All other pieces are irrelevant.
Unfortunately, this limits the room for analysis.
Explanations should factor in the whole board, not just three pieces.
Thus I repeated the training freezing the encoding layer for the board state.


All the code from this post can be found [here](https://github.com/RobinWeitzel/nn-project/blob/master/simple_captions.ipynb).






