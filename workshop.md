* [Introduction](#sec-1)
* [JavaScript](#sec-2)
  * [Equality](#sec-2-1)
    * [Problem](#sec-2-1-1)
    * [Solution](#sec-2-1-2)
  * [Arrow Functions](#sec-2-2)
  * ["Classes"](#sec-2-3)
  * [Promises](#sec-2-4)
  * [Generic tooling](#sec-2-5)
    * [ESLint](#sec-2-5-1)
    * [Webpack](#sec-2-5-2)
* [Web](#sec-3)
* [Git](#sec-4)

# Introduction<a id="sec-1"></a>

The aim of this workshop is to introduce key concepts and benefits of a programming language.

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
return 1 == "1";
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

In JavaScript, `==` is the _abstract equality operator_. When comparing values
with `==`, it converts both values to a common type. As an example, with
[5](#org793ab51), it converts the `true` to a number before comparing.

```js
return Number(true);
```

So since 1 is equal to 1, `1 == true` evaluates to true.

### Solution<a id="sec-2-1-2"></a>

`===` is the strict equality operator. If the values are of different types, they're unequal.

```js
console.log(1 === "1");
console.log(1 === [1]);
console.log(1 === true);
return undefined === null;
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
function multiply(x, y) {
  return x * y;
}
```

* [ ] Something about anonymous

Arrow functions were influenced by CoffeeScript, a functional programming language that transpiled into JavaScript.

They make simple functions a lot more concise.

```js
const multiply = (x, y) => x * y;
```

The difference is particularly apparent when they're passed as arguments e.g. in Promise chains.

```js
fetch("www.example.com/api/v3")
  .then(function(response) {
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  });

/* Versus */
fetch("www.example.com/api/v3")
  .then(response => response.json())
  .then(json => console.log(json));
```

But surely they weren't added just for a little syntactic sugar?

## "Classes"<a id="sec-2-3"></a>

Classes in Javascript was first introduced in ECMAScript® 2015. They are essentially **syntactical sugar**
over Javascript's inheritance with prototype chaining. A reason why you would consider using ES6 Classes
is almostly due to cleaner and simpler syntax. Will there be performance differences (In a positive note)?
Probably the ECMAScript engine rendering the Javascript codes will be able to optimize a little better but 
there will probably be little to no significant difference in the way the script performs. Another reason 
why you should Classes is due to the fact that it is much easier to set up inheritance using the new ES6
syntax. The "methods" provided by Classes look like those familiar to developers of class-based languages 
such as C#, Java, C++ etc. However, they are not the same. JavaScript remains prototype-based.


### Prototype
As mentioned in the general description of this section, ES6 Classes are syntactic sugar for the prototype chain system. To give an example:

```js
var Person = function(name, age)  {
  if(!(this instanceof Person)) {
    throw new Error("Use the 'new' keyword when constructing a Person object.");  
  }
  this.name = name;
  this.age = age;  
};

// Add properties in Person function's prototype
Person.prototype.details = function() {
  alert(`Name: ${this.name}\nAge: ${this.age}`);
};

var user = new  Person('Joshua', 21)
user.details()	
```

As you can see, the 'Person' variable is an instance of an object. It currently has a function property called details. When trying to access a property of an object,the property will not only pursue on the object but on the prototype of the object, the prototype of the prototype, and so on until the end of the prototype chain is reached or there is another matching property name in the prototypical chain. To elaborate more about this, go through the following code snippet:

```js
let Character = function () {
   this.name = '_blank_';
   this.age = 0;
}

let newPlayer = new Character(); // {name: '_blank_', age: 0}
//add properties in Character function's prototype

Character.prototype.age = 20;
Character.prototype.gender = 'Unknown';

console.log(newPlayer.name) // _blank_

console.log(newPlayer.age) // 0
// The prototype also has a 'age' property of value 20 but it's not visited. 
// This is called "property shadowing.

console.log(newPlayer.gender); // Unknown
// newPlayer object does not own the property 'gender'. Hence, check
// the prototype chain newPlayer.[[Prototype]] and the value is 'Unknown'.

console.log(newPlayer.class) // undefined
// newPlayer object does not own the property 'class'.  Hence, check
// the prototype chain newPlayer.[[Prototype]].[[Prototype]] and the value is 'null'
// no property found, return undefined.
```
Each object in Javascript has a private property which stores an association link to another object called its prototype. That prototype object has a prototype of its own, and so on until an object is reached with null as its prototype. Because null has no prototype, it serves as the final link in this prototype chain.

### Implementation of Classes
There are two ways to define a Class. Similar to function declarations and function expressions, the class syntax has two components:  class declarations and class expressions. In this workshop, we will be covering 
**class declarations**. However, you can refer to online resources to find out more about [class expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes).

It is easy to set up a class instance and establish inheritance hierarchies using the new ES6 syntax.The code snippet below creates a class called 'Person' and defining the associating the respective function properties to the object:
```js
// ES6
class Person {
  constructor(name, age) {
      this.name = name;
      this.age = age;
  }

  get details() {
    return this.greet();
  }

  greet() {
    return `Hello. My name is ${this.name} and I am ${this.age} years old.`;
  }

  static predict(person) {
    if(!this instanceof(Person)) {
      throw new Error("Use the 'new' keyword when constructing a Person object.");  
    } else if(person.age > 65) {
      console.log("You are a senior citizen.");
    } else {
      console.log("You are NOT a senior citizen.");
    }
  }
}

const p1 = new Person('Joshua', 20);

console.log(p1.details); // "Hello. My name is Joshua and I am 20 years old."
Person.predict(p1); // "You are NOT a senior citizen."
```
_The bodies of class declarations and class expressions are executed in strict mode i.e. constructor, static and prototype methods, getter and setter functions are executed in strict mode._

```js
// class Person() {
//  ...
// }
class Employee extends Person {
  constructor(name, age, position, salary) {
    super(name, age);
    this.position = position;
    this.salary = salary;
  }

  print() {
    console.log(`You are a ${this.position} and your income is $${this.salary}`)
  }

  greet() {
    const greetings = super.greet();
    console.log(`${greetings} I am a successful person!`);
  }
}

p2.print() // You are a Director and your income is 15000
Person.predict(p2); // "You are a senior citizen."
p2.greet() // Hello. My name is John and I am 70 years old. I am a successful person!
```

## Promises<a id="sec-2-4"></a>

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
