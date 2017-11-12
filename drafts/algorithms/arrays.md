# Walkin' Thru: Arrays

Here's the first iteration of my walkthroughs through various algorithms I will try and solve. 

First algorithm problem might seem pretty easy. I even thought it was too when I solved it a little while back. Turns out I kind of got stuck for a bit there going over it again so it doesn't hurt to go over the easy stuff. Either that or it could be that it's late right now as I'm typing this.

The first problem deals with arrays.

An array is a type of data structure that allows programmers to store data of the same type in a contiguous block of memory. In today's walkthrough I will show how to reverse the order of elements in an array. 

## The Problem
Given an array, A, with N elements, print the elements as a single line in reverse order.

## The Input Format
The first line represents the integer N, the number of elements in the array. The second line contains N integers describing the array, A.

## Sample Input
4

1 4 3 2

## The Output Format 
N integers in array A in reverse order printed as a single line.

## Sample Output
2 3 4 1

## Given Code
```js
process.stdin.resume();
process.stdin.setEncoding('ascii');

var input_stdin = "";
var input_stdin_array = "";
var input_currentline = 0;

process.stdin.on('data', function (data) {
    input_stdin += data;
});

process.stdin.on('end', function () {
    input_stdin_array = input_stdin.split("\n");
    main();    
});

function readLine() {
    return input_stdin_array[input_currentline++];
}

/////////////// ignore above this line ////////////////////

function main() {
    var n = parseInt(readLine());
    arr = readLine().split(' ');
    arr = arr.map(Number);

}
```

## Walkthrough
Let's first look at what is given in the `main` function. The first line in the function declares a variable 
`n` which reads the first line of input and parses any integers. In this case `n` would be 4. In the second 
line of `main`, `arr` reads the second line of input then splits the string into an array of strings. 
Afterwards each element in `arr` is coerced into a number so the elements in `arr` are `[2, 3, 4, 1]`. 

My first step would be to reverse the elements in the array. In order to reverse elements in an array we can use the `reverse` function. 

#### Example
```js
let arr = [1,2,3,4];
let reverseArr = arr.reverse();
console.log(reverseArr); // [4,3,2,1]
```

Afterwards our next step would somehow join the elements of the array into a string. For this, we can use Javascript's `join` function. Doing so would allow us to fulfill the expected output for the problem. Let's look at an example.

#### Example
```js
let arr = [1,2,3,4];
let joinArr = arr.join(' ');
console.log(joinArr); // 1 2 3 4 
```

In the above code, the space in the `join`'s parameter is used as a separator for each element. If instead of `' '` we could use `'-'` and then each of our elements would be separated with `'-'`. 

Like so:

1-2-3-4

Now that we have our pieces together let's look at the final code for the problem.

## The Answer:
```js
function main() {
    var n = parseInt(readLine());
    arr = readLine().split(' ');
    arr = arr.map(Number);
    
    var arrReverse = arr.reverse().join(' ');
    console.log(arrReverse);
}
```

The code chains together both functions for less verbosity. 

There we have it. We have implemented reversing the contents in an array.

