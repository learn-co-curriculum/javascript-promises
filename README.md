JavaScript Promises
---

## Objectives
  + Understand how promises allow us to make specified JavaScript code run synchronously
  + Understand how to resolve a promise in JavaScript.
## Introduction

In the last section we saw that for functions or execution code that takes a while to run, JavaScript happily proceeds onto the next line.  It's why the code below will log "Hola" before alerting "Hello World!".  

```js
  setTimeout(function(){
      alert('Hello world!')
  }, 1000)

  console.log('Hola')
```  

Now there are some times that, we may need JavaScript to wait before proceeding on.  For example, imagine our `setTimeout` function generates some data that we would like in our `console.log` statement.  For example consider the following:

```js
  let message = 'initial'

  setTimeout(function(){
    message = 'updated'
  }, 1000)

  console.log(message)
  // initial
```    

As you can see, we want our `setTimeout` to reassign our variable `message` to equal the string 'updated' before a message is logged.  As we know, the logging of `message` occurs before variable is reassigned.  Let's see if there is a way to have our `console.log` procedure wait until after the the `setTimeout` function has completed.            

## Welcome to Promises

```js
  new Promise(function() { ... } );
```

The code above uses a built in JavaScript constructor function called a promise.  When a new promise is initialized it takes one argument, a function, which is called "the executor".  No joke.  That executor function is executed immediately with the construction of the promise.  Let's just see that part in action.

```js
  new Promise(function() { console.log('message') } );
  // message
  // {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
```

If you run that code in your console, you'll see that the executor function is run immediately - logging the message.  Now let's copy our code about into our executor function.  

```js

new Promise(function() {
  let message = 'initial'

  setTimeout(function(){
    message = 'updated'
  }, 1000)

  console.log(message)
})

// initial
// {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
```  

If you run this new code, our promise may appear pretty useless.  That is, our `console.log` statement still running before the `setTimeout` function completes...bummer.  Ok, so here is where the magic comes in.  

When we create a new promise, we return that promise object we saw above.  

```js
{[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
```

That object has a method on it called `then`, and we can pass that method a function that we can call whenever we want.  So for example, we can change our code to the following.  (The code below is getting there but missing some things, before it will work.)


```js
new Promise(function() {
  let message = 'initial'

  setTimeout(function(){
    message = 'updated'
  }, 1000)
}).then(function(messageArg){
  console.log(messageArg)
})

// Promise {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
```  

Ok, so the way the `then` method works, is that it receives a callback function -- called the "resolve function" -- which only is run at precisely the correct time.  When is that time, well it's after the `message` variable is reassigned.  The final piece is that our executor function, takes an argument, which references the resolve function.  Let's see what this looks like with our final code below.   

```js
const promise = new Promise(function(resolve) {
  let message = 'initial'

  setTimeout(function(){
    message = 'updated'
    resolve(message)
  }, 1000)
}).then(function(messageArg){
  console.log(messageArg)
})

// Promise {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
// updated
promise
  // Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: undefined}
```  

Ok, so the above code initializes a new promise object and is passed an executor function. The executor function takes an argument, which references the resolve function declared as an argument to `.then`.  We execute that resolve function in the line after `message` is reassigned to the string "updated".  Because the resolve function is executed from inside of the call to `setTimeout`, it is not executed until the proper time.  

One other thing to point out is that we assigned the promise to a variable called `promise`.  We did this to show something.  As you can see when we first initialize the promise, it has an attribute called `[[PromiseStatus]]`.  That attribute equals "pending" until the resolve function is called.  Once the resolve function is called, which occurs after 1000 milliseconds per the `setTimeout` function, we see that the `[[PromiseStatus]]` has changed to "updated".

## Promises in Functions

You may be wondering why we did not simply call the `console.log` from directly inside of the `setTimeout` function.  Practically, we could have.  However, we would like to maintain separation of concerns and promises allows us to do that.  We are separating the asynchronous portion of our codebase from the rest of our code.  For example, oftentimes you will see promises wrapped in a function call.  It looks like the following:

```js
function updateMessage(){
  return new Promise(function(resolve) {
    let message = 'initial'

    setTimeout(function(){
      message = 'updated'
      resolve(message)
    }, 1000)
  })
}


updateMessage().then(function(messageArg){
  console.log(messageArg)
})

updateMessage().then(function(messageArg){
  alert(messageArg)
})
```

As you can see, promises allows us to separate out our asynchronous action, and then by wrapping (and returning) our promise in a function, we can then do different things once that promise completes.  

## Summary

Promises allow us to execute asynchronous code, and then delay the execution of other code.  Promises are used by initializing a new promise object, which receives an executor function.  The executor is immediately called.  In the executor function is generally some asynchronous code.  The executor function takes an argument, which references the "resolve" function.  The resolve function is declared as an argument to the promise's `then` method.  This resolve function is generally then executed from inside of asynchronous code.  With this, promises allow a developer to execute code asynchronous code, and not have code that relies on the asynchronous code execute until specified.  A developer can easily specify what that code is by placing it inside of the function argument to the promise's `then` method.

## Resources

[MDN: promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
