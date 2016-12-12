---
layout: post
title: "SocketIO Basic Idea"
author: "Nate Nuval"
---

One of the assignments in my Applications of Databases class involved building a chat app using Flask-SocketIO and AngularJS.
This post isn't a tutorial; Dr. Zacharski has an excellent video tutorial <a href="https://youtu.be/5cQFzc_Zo8M">here</a> 
where he goes through step by step starting from scratch. Think of this as a supplemental guide to help you understand 
the basic idea. The trickiest thing for me to understand was how "emit" works and how does the Model View Controller (MVC) 
paradigm apply.

I like to think of MVC as three people who have to work together. Two of them don't like each other (M and V), so one of them (C) is 
stuck playing the middle man in the group. 

![mvc](/assets/mvc.PNG)

Q: Can tasks be accoplished if 2 out of 3 people don't like each other?

A: In this case Yes. 


Here is how a popup login box is handled:

So you've entered your username and password.

V passes the information to C. 

![vtoc](/assets/vtoc.PNG)
