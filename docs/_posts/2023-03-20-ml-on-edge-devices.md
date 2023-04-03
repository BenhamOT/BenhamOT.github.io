---
layout: post
title:  ML at the edge!
date:   2023-03-20 20:48:03 +0000
categories: computer vision machine learning edge devices
tags: computer-vision machine-learning mlops
---

### Project overview

In this post (my first in fact) I'm going to talk through one of my favourite projects that I've worked on over the 
last few years. The project headline; __can we develop an android app that classifies screenshots on a mobile phone?__
It sounds fairly simple when you read it written down but there were some interesting challenges involved. 

Although a lot of excellent mobile development also went into this, my main focus was developing the computer vision model that would
classify the screenshots, embedding the model into the app and ensuring the inference code that sat within the app 
performed all the required image preprocessing. I can't go into specific details about the purpose of the app but I can 
say that it was to monitor what certain individuals where doing on their phone and detect if they were viewing 
specific things.

### Implementation

Although I'd consider myself to be a [PyTorch][pytorch] convert (a topic for another day), 
this project was developed using [TensorFlow][tensorflow]. Originally I'd attempted to use a pre-trained 
open source model that I found online and so at the start of the project 
the bulk of the work consisted of converting the model into a format such that it could be embedded into the app. This is one 
area where TensorFlow did come into its own. Thankfully there is a tool called [TensorFlow Lite][tflite] that
enabled me to convert the model into a compressed .tflite file.

[tflite]: https://www.tensorflow.org/lite
[pytorch]: https://pytorch.org/ 
[tensorflow]: https://www.tensorflow.org/ 

Converting the original model into the tflite format was actually quite a painful
process because the open source model was developed in the legacy TensorFlow 1.x API and there isn't the same level 
of support for the older tf versions. However, when using the newer tf 2.x API, it is fairly straightforward to use the
tflite conversion API or command line tool. 

Once I'd managed to get over the model conversion hurdle and had a nice compressed tflite file, we embedded the model file into the
app and started the UAT testing. Here we discovered the model was performing poorly compared to the benchmarks we'd established 
from a dataset of approximately several thousand test images.

My first lesson learned; in any ML project it's best to source training data that as close as possible resembles the
data that the model will see when deployed to production. This sounds quite obvious in hindsight but at the time I 
didn't think it would be required to use mobile phone screenshots as part of the training data to get reasonable results. 
This turned out to be a very naive assumption. The concept of a mismatch between your training data and the data that the 
model is exposed to in production is known in the mlops world as data drift. 

The next step was to try and take the existing model and continue the training, passing on labelled mobile screenshot data. 

The model made it into production but it wasn't performing to the clients satisfaction. The way the app worked, if any
screenshots were classified as positive they would be securely sent to the clients servers when an internet connection could be established. 
The problem was, the client was receiving too many false positives and their team responsible for assessing the positively classified 
images couldn't get through them all.

An overarching lesson I learned from this project is that when delivering anything involving ML, it's crucial that everyone
involved is on the same page about the performance of the model and what the corresponding business implications are. 

Finally I decided that I would try and train a new model, using an well defined architecture ... mobilenet ... 
Why did this turn out to be a good move? Machine Learning has continued to evole repidly over the last decade (just look at where we are now 
with generative models (GPT-4, LLaMA, stable diffusion, etc) and even in the 3 years since the open source model was produced
there were many advances that had been made in terms of model architectures ....

architecture consideration - capacity vs performance trade off




Lessons learned
Add complexity when required
 
