---
layout: post
title:  ML at the edge!
date:   2023-03-20 20:48:03 +0000
categories: computer vision machine learning edge devices
tags: computer vision machine learning mlops
---

### Project overview

In this post (my first in fact) I'm going to talk through one of my favourite projects that I've worked on over the 
last few years.

The project headline - can we develop an android app that classifies screenshots on a mobile phone.

It sounds fairly simple when you read it written down but there were some interesting challenges involved. Although a lot
of excellent mobile development also went into this, my main focus was developing the computer vision model that would
classify the screenshots, embedding the model into the app and ensuring the inference code that sat within the app 
performed all the required image preprocessing. I can't go into specific details about the purpose of the app but I can 
say that it was to monitor what certain individuals where doing on their phone and detect if they were viewing 
specific things.

### Implementation

Although I'd consider myself to be a [PyTorch][https://pytorch.org/]  convert (a topic for another day), this project was developed using 
[TensorFlow][https://www.tensorflow.org/] . Originally I'd attempted to use a pre-trained open source model that I found online and therefore 
the bulk of the work consisted of converting the model into a format such that it could be embedded into the app. This is one 
area where TensorFlow did come into its own. Thankfully there is a tool called [TensorFlow Lite][https://www.tensorflow.org/lite]  that
enabled me to convert the model into a compressed .tflite file.
Converting the original model into the tflite format was actually quite a painful
process because the open source model was developed in the legacy TensorFlow 1.x API and there isn't the same level 
of support for the older tf versions. However, when using the newer tf 2.x API it is fairly straightforward to use the
tflite conversion API or command line tool. 

Once I'd managed to get over the model conversion hurdle and had a nice compressed tflite file, we embedded the model file into the
app and started the UAT testing. Here we discovered the model was performing poorly compared to the benchmarks we'd established 
from a dataset of approximately several thousand images.

My first lesson learned - in any ML project it's best to source training data that as close as possible resembles the
data that the model will see when deployed to production. This sounds quite intuitive in hindsight but at the time I 
didn't think it would be required to use mobile phone screenshots as part of the training data to get reasonable results. 
This turned out to be quite a naive assumption.

The next step was to try and take the existing model and continue the training, passing on labelled mobile screenshot data. 

Finally I decided that I would try and train a new model, using an well defined architecture ... mobilenet ... 

Why did this work well? Machine Learning has continued to evole repidly over the last decade (just look at where we are now 
with generative models (GPT-4, LLaMA, stable diffusion, etc) and even in the 3 years since the open source model was produced
there were many advances that had been made in terms of model architectures ....

Lesson only add complexity when it's required. It's been well documented by many 
Challenges

Lessons learned
