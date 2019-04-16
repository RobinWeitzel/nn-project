---
layout: post
title:  "First Post"
date:   2019-04-15 17:21:23 +0300
categories: project
---
The goal of my first post is threefold:
* outline the aim of this project
* list the steps I will take to complete the project
* theorize about the challenges I will face

### Aim of the project
Over the last few years, networks have gotten quite good at generating image captions [[1]](http://openaccess.thecvf.com/content_cvpr_2016/html/You_Image_Captioning_With_CVPR_2016_paper.html) [[2]](http://openaccess.thecvf.com/content_iccv_2017/html/Yao_Boosting_Image_Captioning_ICCV_2017_paper.html).  
At the same time, AlphaZero revolutionized chess engine designs beating the best traditional chess engined by a large margin [[3]](https://arxiv.org/abs/1712.01815).  
One thing missing is explainability. AlphaZero may make better moves than humans but no one knows why the moves were made.  

To solve this, I will try to train a neural network to explain the reasoning behind chess moves.
I will not build a chess engine, instead I will try to generate captions for chess moves (similar to how image captions a created).
As data set I will use human annotated chess games found on the internet.

### Steps needed to complete the project
First, I will look at different ways how to represent a chess board.  
How can I encode a move? Which representations are suitable for machine learning?

Next, I will compile a large data set from different chess websites.  
Because these will probably not have the same format I will also have to do a lot of preprocessing.

Once I have a data set I will train a model to generate the move descriptions.

Lastly, I will evaluate the results from the model.

### Possible Challenges
* Finding enough usable data sets
* Generating captions that make sense (both grammatically and chess related)  
