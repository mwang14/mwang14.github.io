---
layout: post
title: Using Checklists in Computer Science/Machine Learning (and RSS)
category: Work Efficiency
---


I recently started reading [The Checklist Manifesto](http://atulgawande.com/book/the-checklist-manifesto/) by Atul Gawande, and have found a lot of value in it. I have not finished it (as of now I'm 60% through according to my Kindle), but the book is already showing me places where checklists could be of great value in coding, machine learning, and maybe even my personal life. I thought I'd discuss a few of these and what my strategies would be based on what I've learned from the book.

## Coding

Checklists are not meant for things that you will get right 100% of the time without them, rather, they are meant for tasks where failing at one step will cause a good deal of pain. The main place where I think this could be of great use is git. Using git for personal projects should never run you into problems, but when using it with a team you can always screw something up that will cost you hours of your time (and therefore your team's time). I've forgotten to pull before branching, causing merge conflicts, messed up refactoring merge conflicts, messed up rebases, etc. Based on the book, I've come up with a couple of DO-CONFIRM checklists.

### Starting a new branch/feature

* Pull latest code from the base branch
* Check to make sure you have no outstanding code that needs to be stashed
* Create a new branch and check out to it

### Creating a pull request

* Run unit/integration tests
	* Make sure your code is covered in these tests
* Make sure your code is well documented
* Create pull request with description of new feature
* Add reviewer to PR

These are all very simple things to do, but messing them up can have great consequence. You may have forgotten to commit code before creating a new branch, causing a massive headache of getting code where it needs to be. You could forget to add a reviewer to your PR which would leave it hanging without review. These checklists are obviously not final (and I'm open to feedback!), but I think they could help relieve a lot of headache.

## Deep Learning

One area in deep learning where I can see checklists being very valuable is error analysis. Without lots of experience, it can be hard to remember the steps to fixing low recall and/or precision, overfitting, or underfitting. Checklists can be a great way to quickly confirm you are following the right steps. The cost of taking the wrong steps to solve the problem you are having, or even identify the problem, can be months worth of work. As of now, I haven't come up with proper checklists for this, but it is undoubtedly something I'llb e looking to do.

## Personal Life

It may be silly, but a morning routine checklist is something I definitely need right now. Here's how my mornings typically go as of now:

* Wake up to the alarm, turn it off and lay down with my eyes closed for 5-7 minutes.
* Get the energy to grab my phone and read random crap for 15 minutes
* Take a shower
* Read more random crap on my phone after my shower
* Get dressed, brush teeth, comb hair
* Make lunch, leave

There's a lot of time wasted here! For brevity I didn't even include a few things, but many times I "didn't have time to make lunch" or "forgot to brush my teeth". I want this to change. So, here's my checklist for mornings:

* Wake up to the alarm. Turn it off and immediately get up
* Shower (10 min)
* Brush teeth (2 min)
* Get dressed (3-5 min)
* Comb hair (2 min)
* Make lunch (5-10 min)
* Stretch and do physical therapy exercises (15 min)
* Pack bag, leave (3 min)

This is a total of 45 minutes if I folow it to a T. I'll be trying this checklist for the next week, and will update based on my results.

## Final note

This blog is now RSS compatible! Please do add it to your RSS reader. I'm thinking of writing a post about RSS itself (and other random thoughts I've had recently), so stay tuned!