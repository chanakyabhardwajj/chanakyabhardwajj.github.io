---
layout: post
title: ES6 - Reverse Iterable for an Array
meta: Reverse Iterable for an Array without actually reversing the Array
---
For the past few days, I have been working a lot with the new ES6 [Iteration Protocols](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Iteration_protocols). And I thought of sharing a simple concept that I learnt.

Let's start simple.  
If we want to iterate over an array, we can use the *for .. of* loop like this :  
{% highlight javascript %}
let arr = [1,2,3,4,5];
for (let i of arr) {
    console.log(i); //outputs 1,2,3,4,5
}
{% endhighlight %}

But what if we want to iterate over the array **backwards**?  
The usual way would be to use a regular *for* or *while* loop, like :  
{% highlight javascript %}
let arr = [1,2,3,4,5];
for (let i = arr.length-1; i >=0 ; i--) {
    console.log(arr[i]); //outputs 5,4,3,2,1
}
{% endhighlight %}

But this way we lose the convenience of using a *for .. of* loop.  

In order to use a *for .. of* loop to iterate over an array **backwards** we would have to reverse the array itself, like :

{% highlight javascript %}
let arr = [1,2,3,4,5];
for (let i of arr.reverse()) { //not cool!
    console.log(i); //outputs 5,4,3,2,1
}
{% endhighlight %}

But as is obvious, the problem with this approach is that the array *arr* has to be reversed which is not always desired.  


This is where the ES6 Iteration Protocols come into play. We will define our own *custom iterator* that will iterate over the array *arr* **backwards**. And we will make it an *iterable* as well.  
Here we go :  
{% highlight javascript %}
let arr = [1,2,3,4,5];
let index = arr.length;

//Custom Iterator
let reversedIterator = {
    next : function () {
        index--;
        return {
            done : index < 0,
            value : arr[index]
        }
    }
}

//Make it an Iterable
reversedIterator[Symbol.iterator] = function() {
  return this;
}

//Whoa!
for (let i of reversedIterator) {
    console.log(i); //outputs 5,4,3,2,1
}
{% endhighlight %}

If you're not familiar with the *[Symbol.iterator]*, make sure to checkout the [MDN documentation](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Iteration_protocols) for ES6 Iteration Protocols.


From here it is easy to see that this concept can be extended to any kind of iteration logic, over any kind of collection.

Cheers.
