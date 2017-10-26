# Grasping Javascript: `this` Keyword

`this` can be pretty hard to understand when first learning Javascript. Hopefully this article can help in grasping `this` a little easier (pun intended).

## Looking for the Call-Site

It is best to understand where `this` is a reference to by looking at where a function is called. Emphasis on where the function is called and not where it is declared. We are looking for the call-site because it's the only thing that matters for `this` binding.

Let's look at an example of what call-sites are:
```js
function foo() {
	// call-stack here is `foo`
        // call-site is in the global scope

	console.log( 'first' );
	baz(); // call-site here is for `baz`
}

function baz() {
	// call-stack here is `foo` -> `baz`

	console.log( 'second' );
	bar(); // call-site here is for `bar`
}

function bar() {
	// call-stack so far is `foo` -> `baz` -> `bar`
	// the call-site for this function is in 'baz'

	console.log( 'third' );
}

foo(); // call-site for `foo`
```

Knowing the call-site allows us to better understand where `this` is binding to. 

## The 4 Rules

There are four kinds of rules that help what `this` binds to. Utilizing these four rules when insepcting call-sites will help in better understanding `this`. The four rules are default binding, implicit binding, explicit binding, and new binding. We'll go into depth on these rules.

### 1. Default Binding

The most common call-site is the default binding and to be referred to when none of the other rules apply.

Example:
```js
function foo() {
    console.log(this.word);
}

var word = 'hello';

foo(); // "hello"
```

The first thing to note is the global-scope. Here, variable `word` is in the global scope and, therefore, the global object property is `word = 'hello'`. The second thing to note is to look at the call-site, `foo()`. It's just an ordinary reference for the `function foo()` and `this.word` would resolve to that call, which points to the global object.

### 2. Implicit Binding

The second rule to remember is if the call site is in the context of an object.

Example:
```js
function sayName() {
	console.log(this.name);
}

var obj = {
	name: "Adrian",
	sayName: sayName
};

obj.sayName(); // "Adrian"
```

There are a few different things to notice in the example above. The first is how `sayName()` is referenced on the `obj` object and at the call-site `sayName()` is called as a property for `obj`. So in a sense the `sayName()` function is "owned" by `obj`. Therefore, from the implicit binding rule, the `this` keyword points and binds to the `obj` object. The reason why I quoted "owned" is because of some problems that can happen if implicit ruling is applied.

A frustration that comes with implicit binding is how an object can lose its `this` binding. The default rule is used when the implicit rule loses its binding, which then binds to the global object or is undefined.

Example:
```js
function sayName() {
	console.log(this.name);
}

var obj = {
	name: 'Adrian',
	sayName: sayName
};

var bar = obj.sayName;

var name = 'Gotcha';

bar(); // "Gotcha"
```

In the example, it may seem `bar()` should say "Adrian" since it looks like it would get the same value as `obj.sayName` but `bar()` is just another reference to `sayName()`. `bar` is just getting a reference to `obj.sayName` but not invoking it. What matters is how the call-site looks like. `bar()` is just an undecorated function call so default binding is in effect and `obj.sayName` when declaring `bar` just a reference.

### 3. Explicit Binding
Explicit binding can happen by invoking a function's `apply()` or `call()` method. Explicit binding allows you to not add the function as a property to the selected object. Instead, `call()` or `apply()` takes the selected object as its parameter and invokes the function on the specified object.

Example:
```js
function sayName() {
	console.log(this.name);
}

var obj = {
	name: 'Adrian'
};

sayName.call(obj); // "Adrian"
```

In this example, `this` in `sayName()` explicitly binds to obj. Explicit binding allows you to force `this` binding to the object in call/apply's parameter. Note that `call()` and `apply()` are similar but differ in their additional parameters.

Unfortunately, explicit binding alone doesn't rid us of the perils of a function "losing" its `this` binding. Functions can still "lose" its binding or even be paved over by frameworks.

Enter hard binding, a variation of explicit binding which helps us get around losing the `this` binding.

Example:
```js 
function sayName() {
	console.log(this.name);
}

var obj = {
	name: 'Adrian'
};

var sayObjName = function hardBind() {
	sayName().call(obj);
}

sayObjName(); // "Adrian"
setTimeout(sayObjName, 200); // "Adrian"
sayObjName.call(window); // "Adrian"
``` 

In the example we explicitly bind `sayName()` to `obj` but implemented hard binding. In this case, hard binding happens when we invoke `sayObjName()` and at the same time it invokes `hardBind()` and will now forcibly bind `sayName()` with `obj`. So no matter what we do with `sayObjName()` at a later time, `sayName()` applies to `obj`.

In fact, hard binding is so common there is already a built-in method called `Function.prototype.bind()`. The `bind()` method is used like before but with different syntax:

```js
function sayName() {
	console.log(this.name);
}

var obj = {
	name: "Adrian"
};

var sayObjName = sayName.bind(obj);

sayObjName(); // "Adrian"
```

Using the bind() method will now point `this` to the specified `obj` placed in its parameter.

### 4. `new` Binding

The last rule for `this` binding is the `new` binding. Although the keyword `new` is familiar to those who already know class oriented languages, it is not exactly the same. Javascript's "constructor" functions are not the same because they do not belong to any classes and are not instantiating a new class. Let's look at an example.

```js
function foo(a) {
	this.a = a;
}

var bar = new foo(2);
console.log(bar.a); // 2
```

When `new` is placed in front of a function a `new` object is created, that object is prototype linked, and `this` binding is pointing to that object. 

### Which Has Priority

Let's review the four rules and apply them to see which rule will take precedence over the other.

```js
function sayOwnColor() {
	console.log(this.color);
}

var red = {
	color: 'red',
	sayOwnColor: sayOwnColor
};

var blue = {
	color: 'blue',
	sayOwnColor: sayOwnColor
};

red.sayOwnColor(); // "red"
blue.sayOwnColor(); // "blue" 

red.sayOwnColor.call(blue); // "blue"
blue.sayOwnColor.call(red); // "red"
```

In the case above, explicit binding is precedent over implicit binding.

Now let's see `new` binding's precedence in what we have so far.

```js
function setName(name) {
	this.name = name;
}

const person1 = {
	setName: setName
};

const person2 = {};

person1.setName('bob');
console.log(person1.name); // "bob"

person1.setName.call(person2, 'bill');
console.log(person2.name); // "bill"

const dave = new person1.setName('dave');
console.log(dave.name); // "dave"
console.log(person1.name); // "bob"
```

In the example above `new` binding takes precedence over implicit binding.

Let's now see if `new` binding takes precedence over explicit binding.

```js
function setName(name) {
	this.name = name;
}

var person = {};

const name = setName.bind(person);
name('dave');
console.log(person.name); // "dave"

const bob = new name('bob');
console.log(person.name); // "dave"
console.log(bob.name); // "bob" 
```

We notice `new` binding didn't change `person.name`'s value but the `new` binding created a new object who's name property is "bob". Although we bound `person` to `name` with `new`, `bob.name` is "bob". 

In summary, there are four questions to ask when figuring out the value of `this`.

1. Is the function called with `new` binding? If yes, `this` value is the newly created object.

```js
var bar = new foo();
```

2. Is the function called with `call` or `apply` (explicit binding) or hard bound with `bind`? If yes, `this`
value is the explicitly called object.

```js
var bar = foo.call(obj);
```

3. Is the function called in the context of an object or is "owned" by one? `this` value is the context 
object.

```js
var bar = obj.foo();
```

4. If all else fails then default binding is the answer and `this` is the global object.

I hope reading this article helps in understanding `this` a little more. If you found it helpful don't forget to leave a few claps. 