---
layout: post
title:  "Autoencoder"
date:   2019-04-30 12:30:00 +0200
tags: project encoding model
---
So far, we have selected two data encodings and selected two data sets.
In this post, we will train an Autoencoder to test the encodings and data sets.

### What's an Autoencoder?
Autoencoders are unsupervised models via which an encoding can be taught to a neural network.
Often, you have only a small labeled data set. That's why you want your model to already "understand" the encoding when you start training.
Think of it like this: before teaching a model to recognize faces, why don't we first teach it what a face looks like in general?
That's the idea behind Autoencoders.

### How does it help us?
In our case, we want the model to understand a chess position before we try to generate captions.
Furthermore, we have a large, unlabeled data set; perfect for unsupervised training.

For the matrix notation, we can use a simple Encoder-Decoder.
Usually, this architecture is used with multiple CNNs which extract extract patterns from the image. 
However, we don't need this since the matrix notation already consists of patterns (i.e. we denote a pawn directly). There is no need to aggregate this further.
Instead, the matrix is simply flattened and fed through three linear layers with ReLU activation.
The Decoder reverses this process and the resulting matrix is compared with the input matrix.

~~~ python
from keras.models import Model
from keras.layers import Dense, Activation, Input, Flatten, Reshape

def Encoder():
    X = Input(shape=(8, 8, 12))
    f = Flatten()(X)
    d1 = Dense(512, activation='relu')(f)
    d2 = Dense(256, activation='relu')(d1)
    d3 = Dense(128, activation='relu')(d2)
    return Model(X, d3)


def Decoder():
    X = Input(shape=(128,))
    d1 = Dense(256, activation='relu')(X)
    d2 = Dense(512, activation='relu')(d1)
    d3 = Dense(8*8*12, activation='relu')(d2)
    r = Reshape((8, 8, 12))(d3)
    return Model(X, r)


# define input to the model:
X = Input(shape=(8, 8, 12))

# make the model:
autoencoder = Model(X, Decoder()(Encoder()(X)))

# compile the model:
autoencoder.compile(optimizer='adadelta', loss='binary_crossentropy')
~~~

This demonstrates how well our model can encode the matrix notation in a dense vector.
Once we get to the caption generation, we will use the trained Encoder as part of our model.




This post is under construction. And will be completed over the next few days (it takes some time to run the model).
