---
layout: post
title: Neural Network Interpretability/Blog updates
---

Wow, what a semester. I finished my graduate school applications, took my first class at MIT through the Advanced Study Program(Introduction to Machine Learning 6.036) while working. In the Winter semester, I'll be taking [Deep Learning](https://www.google.com/search?q=scpd+deep+learnng&oq=scpd+deep+learnng&aqs=chrome..69i57j0.1766j1j9&sourceid=chrome&ie=UTF-8) from Stanford's center of professional development online. Taking classes in person was pretty difficult for me, so I'm excited to be able to take some online on my own schedule. Fortunately, that means that I'll (hopefully) have time to write blog posts. Here's a list of my tentative plan going forward.

### Network Intrusion Detection
Last year, I did some network intrusion detection with raw network packets generated from a cyber range in Australia - you can see the dataset [here](https://www.unsw.adfa.edu.au/unsw-canberra-cyber/cybersecurity/ADFA-NB15-Datasets/). I plan on doing a write-up of how I approached this as well as some results. This was my first foray in combining machine learning with cybersecurity, and I had some pretty interesting takeaways in the drawbacks of using machine learning for security in general. I plan on posting a write-up on my approach and takeaways from this.

### Interpretability in Machine Learning 
I'm currently applying to PhD programs - the area I'm currently most interested in studying is interpretability in machine learning models. I knew before that neural networks are kind of a black box, but after taking a class I have a better understanding of why they're so hard to interpret. Even so, I have a couple of vague ideas/intuitions/more specific questions I plan on exploring.
1. Given a dataset of inputs to a neural network, it would be interesting to see the resulting feature transformation after training a neural network for each input. While it has the potential to be very complex, probably a lot of the neurons(assuming they use ReLU activation) are dead so the final equation for each input may not be too complicated. My question mostly is how different the final equations end up - maybe we could combine them to come up with a somewhat wholistic equation for the entire network. It might turn out that all training samples of one class have one equation(or a similar equation) and training samples of another class have the same different equation.
2. I swear I had more ideas... but I can't think of any right now. Womp womp.

### Adversarial Machine Learning
I'm curious if adversarial attacks in machine learning are signaturable at all - if you have enough adversarial examples, could you detect them and then use an augmented model that's robust against adversarial attacks?

### Closing thoughts
I pretty much don't have any big closing thoughts for this. Happy new years.