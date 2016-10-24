---
layout: post
title: ES6 - How Async/Await Works
description: A look into how Async/Await is transpiled and executed.
---
[Async functions](https://github.com/tc39/ecmascript-asyncawait) (a stage-3 ECMAScript proposal at this time) enable us to write asynchronous code in a synchronous fashion. However *Async/Await* does not have native support, yet.
Therefore in order to use this feature, we have to resort to various transpilers (like Babel).

Let's begin with a simple example of using Async functions.
{% highlight javascript %}
//'f' is an asynchronous function that returns a Promise. 
//The promise is resolved after a delay of 500ms. Simple.
async function f(n) {
    return new Promise(res => {
        setTimeout(() => res(n+1), 500);
    });
}

//'logger' is also an asynchronous function.
//It awaits on 'f' multiple times.
async function logger() {
    let first = await f(0); //waiting on f
    let second = await f(first); //waiting on f
    let third = await f(second); //waiting on f
    console.log(first, second, third);
}

logger(); //prints "1 2 3"
{% endhighlight %}
This example shows the power of *Async/Await* syntax i.e. how to avoid callbacks & promise-chains to write asynchronous code.
You can run this example using [Babel REPL](https://babeljs.io/repl/) and see the result for yourself.

**But how does this work without any native support? How is this syntax transpiled and executed correctly?**

The answer to that question is [**Generator functions**](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/function*) and [**ES6 Promises**](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise). The *Async/Await* syntax is transpiled to a clever use of Generator functions & ES6 Promises, both of which are natively supported.

_The rest of the post assumes a basic understanding of both of these concepts._

Let's understand how that works, by looking at a very special function:

{% highlight javascript %}
function f(n) {
    return new Promise(res => {
        setTimeout(() => res(n+1), 500);
    });
}

function* logger_v1() {
    let first = yield f(0);
    let second = yield f(first);
    let third = yield f(second);
    console.log(first, second, third);
}

function runner(genFn) {
    let g = genFn();
    let nxt = g.next.bind(g);
    let thrw = g.throw.bind(g);
    let result;

    try {
        result = nxt();
    } catch(e) {
        result = thrw(e);
    }

    let done = result.done;
    while (!done) {
        result = Promise.resolve(result.value).then(res => nxt(res), rej => thrw(rej));
    }

    return result;
}

runner(logger_v1);
{% endhighlight %}
