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


![mvc](/assets/mvc.PNG)


Here is how a popup login box is handled:

So you've entered your username and password.

![vtoc](/assets/vtoc.PNG)

V passes the information to C. Under V, underlined with red is 


```html
<form ng-submit="processLogin()">
   -
   -
   -
</form>
```

When a submit action occurs, it will 
have the 

{% highlight ruby %}

$scope.processLogin = function processLogin(){
 -
 -
};

{% endhighlight %}

function handle that event (you can see that function under C, underlined with red as well). The information
being passed is underlined with green and yellow. Under V, these are declared with 

```html
ng-model="name2"
```
These are referenced under C 
with 

{% highlight ruby %}
$scope.name2
{% endhighlight %}


![ctom](/assets/ctom.PNG)

C passes the information to M with 

{% highlight ruby %}
socket.emit('login', ---, ---); 
{% endhighlight %}

M receives the information with 

{% highlight ruby %}
@socketio.on('login', ---)
{% endhighlight %}

'login' is the name I gave this socket event. This needs to match in both the M and C. The 

{% highlight ruby %}
def on_login(un, pw):
{% endhighlight%}

under M takes two arguments. The '$scope.name2', and the '$scope.password' are the two arguments passed. 


![mtoc](/assets/mtoc.PNG)


After M checks if the information matches in the database, it sends it's response back to C. If the info does not match it does

{% highlight ruby %}
emit('logfail', ---)
{% endhighlight %}

If it does match, 

{% highlight ruby %}
emit('logsuc', ---) 
{% endhighlight %}

is executed. C receives catches these with 'socket.on'. Notice the matching names in both M and C.


![ctov](/assets/ctov.PNG)

Here we are calling a js function. We are getting the element 'popupbox' and changing it's visibility.



Basically "emit" is used to call functions in both directions between M and C. If M calls 

{% highlight ruby %}
emit('foo', x, y, z)
{% endhighlight %}

then C would receive that with 

{% highlight ruby %}
socket.on('foo', function(a, b, c){
{% endhighlight %}

If C were to call

{% highlight ruby %}
socket.emit('wubalubadubdub');
{% endhighlight %}

then M would receive that with

{% highlight ruby %}
@socketio.on('wubalubadubdub')
def on_wuba():
{% endhighlight %}
