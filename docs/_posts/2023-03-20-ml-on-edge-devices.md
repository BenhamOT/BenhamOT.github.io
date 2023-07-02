---
layout: post
title:  ML at the edge!
date:   2023-03-20 20:48:03 +0000
categories: computer vision machine learning edge devices
tags: computer-vision machine-learning mlops
---

### Intro

In this post (my first in fact) I'm going to talk through one of the most interesting projects that I've worked on over the 
last few years. The project headline; __can we develop an android app that classifies screenshots on a mobile phone?__
It sounds fairly simple but there were some interesting challenges involved. 

Although a lot of excellent mobile development also went into this, my main focus was developing the computer vision model that would
classify the screenshots and embedding the model into the app. I won't go into specific details about the purpose of the app
(to save myself potentially getting into trouble) but I will say that it was to monitor what people where doing on specific phones.

### Implementation

Although I'd consider myself to be a [PyTorch][pytorch] convert (a topic for another day), 
this project was developed using [TensorFlow][tensorflow] (tf). Originally I'd attempted to use a pre-trained 
open source model that I found online and so at the start of the project 
the bulk of the work consisted of converting the model into a format such that it could be embedded into the app. This is one 
area where TensorFlow does come into its own; there is a feature called [TensorFlow Lite][tflite] that
enables you to convert a model into a compressed .tflite file that can then be embedded onto an edge devices. 

[tflite]: https://www.tensorflow.org/lite
[pytorch]: https://pytorch.org/ 
[tensorflow]: https://www.tensorflow.org/ 

Initially, converting the original model into the tflite format was a surprisingly painful
process because the open source model was developed in the legacy tf 1.x API and there isn't the same level 
of tf lite support for the older tf versions. However, when using the newer tf 2.x API, it is fairly straightforward to use the
tflite conversion API or command line tool.

Once I'd managed to get over the model conversion hurdle and had a nice compressed tflite file, we embedded the model file into the
app and started UAT testing. Here we discovered the model was performing poorly compared to the benchmarks we'd established 
from a dataset of several thousand test images.

My first lesson learned; in any ML project it's best to source training data that matches the
data that the model will see when deployed to production. This sounds obvious in hindsight but at the time I 
didn't realise it was necessary to use mobile phone screenshots as part of the training data to get reasonable results. 
This turned out to be a pretty naive assumption. The concept of a mismatch between your training data and the data that the 
model is exposed to in production is known in the mlops world as data drift. 

The next step was to try and take the existing model and continue the training, using labelled mobile screenshot data. 
The model made it into production but it wasn't performing to the clients satisfaction. The way the app worked, if any
screenshots were classified as positive they would be securely sent to the clients servers when an internet connection could be established. 
The problem was, they were receiving too many false positives and the team responsible for assessing the positively classified 
images wouldn't be able to get through them all as the total number of active devices increased. 

An overarching lesson I learned from this project is that when delivering anything involving ML, it's crucial that everyone 
is on the same page about the performance of the ML application and what the corresponding business implications are. In this case, 
ensuring that everyone understood that a false positive rate of 1% meant that out of every 100000 'negative' images, the model 
would still incorrectly classify ~ 1000 of them as positive. 

Based on the need to further reduce the false positive rate, I decided that I would try and train a new model. 
Thankfully, this turned out to be a good move. Machine Learning has continued to evolve rapidly over the last decade, 
just look at where we are now with generative models and LLMs, and in the 3 years since 
the original open source model was developed there had been many advances made in terms of model architectures and efficiency.

When deploying ML onto an edge device there is a trade off between model performance (how good the model is at it's job) and device performance. 
The general trend is that larger models with more parameters are more likely to be better at whatever 
they're trained to do, given sufficient training data. However, it's difficult to embed large models over a certain size,
in terms of parameters, into edge devices because of hardware limitations (compute, battery life etc). This is likely to 
be an interesting conundrum faced by people trying to embed LLMs and generative models into edge devices. Fortunately, at the time 
of development there were a range of model architectures that had been purposely designed for mobile devices. At the time, 
the overall winner was mobilenetV3 from the [mobilenet][mobilenet] family. Although there are better options nowadays, at the time
this offered the best trade off between model and device performance.

[mobilenet]:https://keras.io/api/applications/mobilenet/

I trained the MobileNetV3 model on AWS Sagemaker, using Sagemaker studio. An example of some of the helper scripts that enabled me to do this can be found [here][sagemaker-repo]. When training models using Sagemaker, you've got 3 different options
to choose from: 

Use the built in algorithms

Use bespoke code that can be run in a pre-built docker container

Provide your own bespoke code and container image.

In this specific I went for the second approach, using my own tensorflow code in the Sagemaker provided tensorflow container. 
This provided the best trade off between flexibility and ease. Historcially I've found the built in algorithms to be a bit
awkward to configure.

[sagemaker-repo]:https://github.com/BenhamOT/aws-sagemaker-custom-training-example

benefits of using Sagemaker - bayesian optimiation of the hyparameters, distributed training which the trainings runs up
significantly

The final thing I want to touch on is why the model was embedded into the app and not hosted in the cloud for example. 
Why embed into the device? Offline, online, cost, performance 

 
