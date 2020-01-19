---
layout: post
title:      "JavaScript Scope"
date:       2020-01-19 11:51:26 +0000
permalink:  javascript_scope
---


Understanding scope in programming is key to appreciating how your variables interact with the rest of your code. In some languages, this can be quite straightforward, but JavaScript’s anonymous functions and event handling features, along with a couple of little quirks, mean that handling scope in your applications can become frustrating.

Scope is the accessibility of variables, functions, and objects in some particular part of your code during runtime. In other words, scope determines the visibility of variables and other resources in areas of your code.



Scopes I covered in this blog are:
1. Global scope
2. Local scope
3. Function Scope
4. Block Scope
5. Lexical Scope
6. Scope chain



**Global Scope
**

Variables defined outside any function, block, or module scope have global scope. Variables in global scope can be accessed from everywhere in the application.

```
let carName = "Volvo";

// code here can use carName

function myFunction() {

  // code here can also use carName

}
```

**Local scope
**

Local variables have Function scope: They can only be accessed from within the function.


```
// code here can NOT use carName

function myFunction() {
  let carName = "Volvo";

  // code here CAN use carName

}
```

**Function Scope**

Function scope means that parameters and variables defined in a function are visible everywhere within the function, but are not visible outside of the function.

```
function doSomething(){
  console.log(x);
  var x = 1;
}
doSomething(); //undefined


function doSomething(){
  console.log(x);
  let x = 1;
}
doSomething();
//Uncaught ReferenceError: x is not defined

}
```

**Block Scope
**

In contrast, the var declaration has no block scope.

Variables declared with let and const can have block scope. They can only be accessed in the block in which they are defined.

```
if(true){
    var myVar = 1        // var
    }

myVar
// 1


if(true){
    let myVar = 1       // let
    }

myVar
// VM637:1 Uncaught ReferenceError: myVar is not defined
//     at <anonymous>:1:1

}
```


**Lexical Scope
**

Lexical scope is the ability of the inner function to access the outer scope in which it is defined.

The log function is a closure. It refers the x variable from its parent function autorun(), not the one from the run() function.

The closure function has access to the scope in which it was created, not the scope in which it was executed.

The local function scope of autorun() is the lexical scope of the log() function.

```
(function autorun(){
    let x = 1;
    function log(){
      console.log(x);
    };
    
    function run(fn){
      let x = 100;
      fn();
    }
    
    run(log);//1
})();

// 1
```

**Scope Chain
**

Every scope has a link to the parent scope.

When a variable is used, JavaScript looks down the scope chain until it either finds the requested variable or until it reaches the global scope, which is the end of the scope chain.

```
let x0 = 0;
(function autorun1(){
 let x1 = 1;
  
 (function autorun2(){
   let x2 = 2;
  
   (function autorun3(){
     let x3 = 3;
      
     console.log(x0 + " " + x1 + " " + x2 + " " + x3);//0 1 2 3
    })();
  })();
})();
```

Variables defined in global scope are available everywhere in the application.

In a module, a variable declared outside any function is hidden and not available to other modules unless it is explicitly exported.

Function scope means that parameters and variables defined in a function are visible everywhere within the function.

Variables declared with let and const have block scope. var doesn’t have block scope.

In "Strict Mode", undeclared variables are not automatically global.

In my next blog I will cover "Strict Mode". Thanks for reading this blog dedicated to give a bit of an introductory insight to scope in JavaScript.



