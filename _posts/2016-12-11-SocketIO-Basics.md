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

V passes the information to C. Under V, underlined with red is 'ng-submit="processLogin()"'. When a submit action occurs, it will 
have the 'processLogin()' function handle that event (you can see that function under C, underlined with red as well). The information
being handled is underlined with green and yellow. Under V, these are declared with 'ng-model="name2"'. These are referenced under C 
with '$scope.name2'.

![vtoc](/assets/vtoc.PNG)


C passes the information to M with 'socket.emit('login', ---, ---);'. M receives the information with '@socketio.on('login', ---)'
'login' is the name I gave this socket event. This needs to match in both the M and C. The 'def on_login(un, pw):' under M takes two 
arguments. The '$scope.name2', and the '$scope.password' are the two arguments passed. 

![ctom](/assets/ctom.PNG)


After M checks if the information matches in the database, it sends it's response back to C. If the info does not match it does
'emit('logfail', ---)'. If it does match, 'emit('logsuc', ---)' is executed. C receives catches these with 'socket.on'. Notice the matching names in both M and C.

![mtoc](/assets/mtoc.PNG)
