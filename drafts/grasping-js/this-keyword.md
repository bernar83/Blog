# Grasping Javascript: `this` Keyword

`this` can be pretty hard to understand when first learning Javascript. Hopefully this article can help in grasping `this` a little easier (pun intended).

## Looking for the Call-Site

It is best to understand where `this` is a reference to by looking at where a function is called. Emphasis on where the function is called and not where it is declared. We are looking for the call-site because it's the only thing that matters for `this` binding.

Let's look at an example:
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