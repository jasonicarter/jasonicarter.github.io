---
layout: post
title:  "The Journey: Practical Deep Learning for Coders - Part 1"
date: 2017-03-15
tags: [MOOC, Deep Learning, Machine Learning, fast.ai]
comments: true
share: true
---

## Warning:
This will be an ongoing (or until I get bored), haphazard, full of holes and riddled with tangents, documentation of my journey becoming a deep learning practitioner.

[Practical Deep Learning for Coders](http://course.fast.ai/) is a MOOC by [fast.ai](http://www.fast.ai) that believes 1 yr of coding experience and a very different way of teaching can put you on the path of a deep learning expert.

> fast.ai is dedicated to making the power of deep learning accessible to all

> […]one of the biggest differences that you’ll see as a result is that **we teach “top down” rather than “bottom up”**

## Lesson 0: The Setup
![lesson zero](/assets/posts/practical_deep_learning/lesson_0.jpg)

Why fast.ai? I first heard of fast.ai through a [colleague](https://twitter.com/inspiratory) also interested in data science. But I started the course because of their teaching philosophy and what it means to [provide a good education.](http://www.fast.ai/2016/10/08/teaching-philosophy/) It’s quite an intriguing approach and I do love being an earlier adapter of anything tech, so why not?

*For reference: I’m a software engineer, with a data analyst nano-degree from [Udacity](http://www.udacity.com).*

Lessons start, sort of. The first lesson is about getting your environment up and running. This includes creating an AWS instance, running a couple of bash scripts, testing Jupyter notebooks and following along a video.

All straight forward with only a few “gotchas”. Next up, **Lesson 1: Recognizing Cats and Dogs.**

## Lesson 1: Dogs vs Cats
![lesson one](/assets/posts/practical_deep_learning/lesson_1.jpg)

> It’s the class which is kind of like the opening of a novel. […] when you have to introduce all of the characters and their backstories — Jeremy

From the intro of [The Fred Hollows Foundation](http://www.hollows.org) to classifying skin lesions, from installing tmux to discussing [ImageNet](http://www.image-net.org) and the VGG model, all of the characters have been laid out.

1 hour and 38 mins later lesson 1 video ends but so much more material to read and a bit of homework to do. The MOOC is the online version of the weekly class, so lets see if 7 days from now my homework is done.

…it’s [Kaggle](https://kaggle.com) time!

Vgg16 model is “7 lines of code” and that’s all you need. And it is, kind of.

Don’t think too much about the code. I did. And got lost. Lesson 1 is top-down as I assume everything will be and I spent way too much time reading and thinking about every function call I made.

Ctrl+C,V the 7 lines, understand it from a high level and move on. You just trained (a pre-trained) model and validated the results. Congrats!

Now to generate predictions, some data manipulation and submit my entry to Kaggle. I kept my sanity only because I skipped to the lesson 2 wiki and notebook, and realized things will become much clearer if you don’t try too hard in lesson 1 aka try to figure out/understand every detail.

Lesson 1 does feel like you jumped into the deep end of the pool, but to put things into perspective, I had a Kaggle account for almost a year now and today was my first ever competition submission.

### STOP!
BTW, unless you have piles of gold hanging around remember this one thing: **aws-stop** or else
![bag of gold](/assets/posts/practical_deep_learning/money_gold.jpg)

That's it for now. I’m very interested to see what happens next. I’ve used CNNs to submit a Kaggle entry and now I get to learn how a CNN actually works. Next up, **Lesson 2: Convolutional Neural Networks.**
