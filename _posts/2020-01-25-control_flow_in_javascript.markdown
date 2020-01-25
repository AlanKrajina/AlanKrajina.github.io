---
layout: post
title:      "Control Flow in JavaScript"
date:       2020-01-25 12:18:45 +0000
permalink:  control_flow_in_javascript
---


Control flow is in computer science the order that the instructions or statements or functions are executed. In javascript, we read the code starting from the first line till the last line unless of course in the code there is some instructions or statements that changes that control flow.

#### 1. Controlling loops: `break` and `continue`

**Break**

- can be used to control loops and other statements while you are inside them.

The `break` statement can also be used to jump out of a loop.  

The `break` statement breaks the loop and continues executing the code after the loop (if any):

```
let i = 0;

while (i < 6) {
  if (i === 3) {
    break;
  }
  i = i + 1;
}

console.log(i);
// expected output: 3
```

or

```
for (let i = 0; i < 5; i++) {
  if (i === 3) {
    break;
  }
  console.log(i);
}
//excepted output : 0,1,2
```

The `break` statement exits a `switch` statement or a loop (for, for ... in, while, do ... while). When the `break` statement is used with a `switch` statement, it breaks out of the switch block. ... When the `break` statement is used in a `loop`, it breaks the loop and continues executing the code after the loop (if any).



**Continue**


`continue` only works inside while, do-while, for, for-of, for-await-of, and for-in. It immediately leaves the current loop iteration and continues with the next one – for example:

```
for ( let i = 0; i<10;i++){
if(i ===5){
continue;
}
console.log(i);
}
/*excepted output:
0
1
2
3
4
6
7
8
9*/
```

`console.log` will print all loop except the number 5, that is where the continue statement was set.

```
const lines = [
  'Normal line',
  '# Comment',
  'Another normal line',
];

for (const line of lines) {
  if (line.startsWith('#')) continue;
  console.log(line);
}

// Output:
// 'Normal line'
// 'Another normal line'
```

#### 2. `IF ELSE`

`if/else` statements are how programs process yes/no questions programmatically. If the first condition evaluates to true, then the program will run the first block of code. Otherwise, it will run the else block.

```
let weather = "rainy";
if(weather === "rainy") {
    console.log("Don't forget an umbrella today!");
}
else {
    console.log("It might be nice out today"!); 
}

// Don't forget an umbrella today!
```


`else if` statements are used to add more conditions to an if/else statement.


```
let weather = "sunny";
if(weather === "rainy") {
    console.log("Don't forget an umbrella!");
} else if (weather === "sunny"){
    console.log("Let's grab some sunscreen!");
} else {
    console.log("It might be nice out today"!); 
}

// Let's grab some sunscreen!

```

#### `switch`

- The switch expression is evaluated once.
- The value of the expression is compared with the values of each case.
- If there is a match, the associated block of code is executed.

The `getDay()` method returns the weekday as a number between 0 and 6.

If today is neither Saturday (6) nor Sunday (0), write a `default` message:

```
switch (new Date().getDay()) {
  case 6:
    text = "Today is Saturday";
    break;
  case 0:
    text = "Today is Sunday";
    break;
  default:
    text = "Looking forward to the Weekend";
}

// Today is Saturday

```

The `default` keyword specifies the code to run if there is no case match.

#### `while` loops

The following code uses a `while` loop. In each loop iteration, it removes the first element of arr via .shift() and logs it.

```
const arr = ['a', 'b', 'c'];
while (arr.length > 0) {
  const elem = arr.shift();          // remove first element
  console.log(elem);
}

// 'a'
// 'b'
// 'c'
```

If the condition always evaluates to true, then `while` is an infinite loop:

```
while (true) {
  if (Math.random() === 0) break;
}
```

The `do-while` loop works much like `while`, but it checks its condition after each loop iteration, not before.

```
let input;
do {
  input = prompt('Enter text:');
  console.log(input);
} while (input !== ':q');
```

`prompt()` is a global function that is available in web browsers. It prompts the user to input text and returns it.



