- [Introduction](#sec-1)
- [JavaScript](#sec-2)
  - [Equality](#sec-2-1)
    - [Problem](#sec-2-1-1)
    - [Solution](#sec-2-1-2)
  - [Arrow Functions](#sec-2-2)
  - ["Classes"](#sec-2-3)
  - [Promises](#sec-2-4)
  - [Generic tooling](#sec-2-5)
    - [ESLint](#sec-2-5-1)
    - [Webpack](#sec-2-5-2)
- [Web](#sec-3)
- [Git](#sec-4)


# Introduction<a id="sec-1"></a>

# JavaScript<a id="sec-2"></a>

## Equality<a id="sec-2-1"></a>

### Problem<a id="sec-2-1-1"></a>

What do you think is gonna happen below?

```js
return 1 == 1;
```

What about this?

```js
return 1 === 1;
```

😲 So what's the difference ??!?!?

Well, let's try this&#x2026;

```js
return 1 == '1';
```

**Huh?**

```js
return 1 == [1];
```

**!!!**

```js
return 1 == true; // wat
```

    true

Ok, ok, we get the point. Why does this happen?

In JavaScript, `==` is the *abstract equality operator*. When comparing values
with `==`, it converts both values to a common type. As an example, with
[5](#org793ab51), it converts the `true` to a number before comparing.

```js
return Number(true)
```

So since 1 is equal to 1, `1 == true` evaluates to true.

### Solution<a id="sec-2-1-2"></a>

`===` is the strict equality operator. If the values are of different types, they're unequal.

```js
console.log(1 === '1');
console.log(1 === [1]);
console.log(1 === true);
return (undefined === null);
```

Well, most of the time anyway. You still get magical edge cases like this.

```js
return NaN === NaN;
```

TLDR; with JavaScript, **ALWAYS** use the strict equality operator (`===`) for
comparison unless you really know what you're doing with `==`.

## Arrow Functions<a id="sec-2-2"></a>

Consider the following function:

```js
function multiply (x, y) {
    return x * y;
}
```

-   [ ] Something about anonymous

Arrow functions were influenced by CoffeeScript, a functional programming language that transpiled into JavaScript.

They make simple functions a lot more concise.

```js
const multiply = (x, y) => x * y;
```

The difference is particularly apparent when they're passed as arguments e.g. in Promise chains.

```js
fetch('www.example.com/api/v3')
    .then(function (response) {
        return response.json();
    })
    .then(function (json) {
        console.log(json);
    })

/* Versus */
fetch('www.example.com/api/v3')
    .then(response => response.json())
    .then(json => console.log(json))
```

But surely they weren't added just for a little syntactic sugar?

## "Classes"<a id="sec-2-3"></a>

## Promises<a id="sec-2-4"></a>

Before talking about promises, let's look at the following pseudocode of a web client making 2 AJAX requests:

```md
1. Request data from server
2. Process the data returned from the server
3. Render the processed data in a dropdown list
4. Request another data from server
5. Process the data returned from the server
6. Render the processed data in a table
```

As you can see from the pseudocode above, the instructions given is executed in a sequential order (a synchronous operation). But what might not be obvious here is that when an AJAX request is made, it might take the server awhile to return a response(or none could be returned either). That would mean that your code will be stuck waiting for the server's response and none of the code below it will execute unless the server's response is returned. In the context of client side web development with JS, that would mean that your site might take a longer time to be ready before your users can start interacting with it.

Now let's look at another set of pseudocode doing the same thing (albeit differently):

```md
1. Request data from server and proceed to execute step 4. Only execute steps 2 and 3 when server returns a response.
2. Process the data returned from the server
3. Render the processed data in a dropdown list

4. Request another data from server, only execute steps 5 and 6 when server returns a response.
5. Process the data returned from the server
6. Render the processed data in a table
```

As you can see from the second pseudocode above, once the first AJAX request is made to the server, we proceed to execute the second AJAX request. Steps (2, 3) and (5, 6) will only execute when steps 1 and 6 recieves a response from the server respectively. This is an example of an asynchronous operation whereby code does not execute in a sequential order. In the context of client side web development with JS, that would mean that the pseudocode above appears to be executing the two AJAX request concurrently, allowing your web page to be ready for users to interact with much quickly compared to the first pseudocode example.

While the above examples do mention about how fast it takes for the website to get ready before users can start interacting with it, the point to draw here is the fashion in which code is executed. JS is single threaded, hence, a sequential operation involving a task that might not immediately return a result (e.g. I/O, networking operations, etc) will block the execution of the remaining code while waiting for some response like in pseudocode example 1. Pseudocode example 2 however, illustrates how JS effectively deals with tasks that takes awhile to return a result without blocking the execution of the other codes below by executing code in an asynchronous manner. In code speak, a way to model how JS deals with such asynchronous operations is through the use of promises.

Now let's enter the promise. A promise is simply a JS object that may return a single value in the future such as a resolved value or a reason why it fails to return a resolved value. As such, a promise has 3 possible states, fufilled, rejected, or pending.

To illustrate the states of a promise (and how to use them), let's look at the following example:

```js
// When we invoke the fetch API (a promise) to request data from a server endpoint,
// We can say that the current state of this promise is pending.
// At this state, #2 will execute first.

// #1
fetch('www.example.com/api/v3');    
    .then(function (response) {
        // When the request is complete, the promise is fufilled
        // And this callback function will be invoked with a single
        // argument representing the resolved value from the successful request
        console.log(response);
    })
    .catch(function (err) {
        // However if the request would to fail, the promise will be rejected
        // And this callback function will be invoked with a single
        // argument representing the reason why the Fetch API fails to complete your request successfully
        console.log(err);
    })

// #2
console.log('I will likely show first before the operation above completes');
```

Now that you know what a promise is and how to use them, let's look at how to write a promise

```js
// Import file system module from nodejs
const fs = require('fs');

// This function takes a filename as an argument and returns the file content
function readFile(filename) {
    // This promise wraps over the readFile function of the fs module
    return new Promise((resolve, reject) => {
        fs.readFile(filename, (err, file) => {
            if (err) {
                // Reject promise with error
                return reject(err);
            }
            // Resolve promise with file
            resolve(file);
        });
    });
}

// Invoke readFile function
readFile('file.txt')
    .then(function (result) {
        console.log(result); // file.text's content
    })
    .catch(function (err) {
        console.log(err); // log error
    })
```

This concludes the section on promises.

## Generic tooling<a id="sec-2-5"></a>

### ESLint<a id="sec-2-5-1"></a>

Fundamentally, ESLint is a utility that enforces a collection of rules (a style
guide) that your JS code needs to adhere to. By enforcing these set of rules,
ESLint is able to catch errors that you might have failed to notice when writing
JS (e.g. a dangling comma, syntax errors, accidental globals).

ESLint further enforces code formatting conventions, prompting you to write code
in a uniform fashion that can be easily understood and maintained by your team.

Moreover, ESLint is plugin based, and hence, different kinds of style guides can
be configured for your project to catch JS errors and enforce formatting
conventions.

Here's a quick look of ESLint in action in Visual Studio Code:

![alt text](images/eslint-in-action.png "ESLint catches an unexpected trailing comma")

To sum up, ESLint is a utility implemeted in JS projects to ensure that the code
you write meets the rules it enforces, so as to keep your code base consistent
and as error free as possbile.

### Webpack<a id="sec-2-5-2"></a>

# Web<a id="sec-3"></a>

# Git<a id="sec-4"></a>
