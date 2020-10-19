---
title: Variable Declaration
date: 2020-10-07 19:14:12
tags:
categories: 
- Javascript
---



## Variable Declaration

### Let keyword

**Features**：

1. No variable hoisting —— Variables can only be used after declaration.
2. Temporal dead zone —— If `let` declares a variable v, then v cannot be used before let declares it in the scope of let. It can be said that before v is declared, it already exists (this variable name is already occupied and cannot be used (sorry for the nonsense in the brackets)), but it cannot be obtained. Only after it is declared can it be obtained and used.
3. No repeated declaration of variables —— Therefore, it is not allowed to redeclare variables inside the function.



### Block scope

There are only global scope and function scope in ES5. ES6 adds block-level scope, and block-level scope must be in braces. (But it doesn't seem right. ES5 also has the concept of block-level scope, but there are only two types of variables: global and function-level.)

In the ES5 specification, it is not allowed to declare functions in block-level scope. (But if you declare it, no error will be reported. The browser still supports it, but it does not conform to the specification.) In ES6, it is clearly allowed to declare functions in the block-level scope. The behavior of the function declaration statement is equivalent to let, and the function is not referable outside the block-level scope.

But! Look at the following code:

```javascript
// The ES6 environment of the browser
function f() { console.log('I am outside!'); }

(function () {
  if (false) {
    // Declare function f repeatedly
    function f() { console.log('I am inside!'); }
  }

  f();
}());
// Uncaught TypeError: f is not a function
```

It should output`I am outside!`, but for ES6 browsers, this code will report an error. Because ES6 browsers still follow the`var`method for the functions declared in the block-level scope。In other words, the function declaration will be promoted to the head of the global or function scope. That is, the function declared in the block-level scope is actually equivalent to the behavior of the var variable.

The above code will be converted to:

```javascript
function f() { console.log('I am outside!'); }

(function () {
  var f = undefined;
  if (false) {
    // Declare function f repeatedly
    function f() { console.log('I am inside!'); }
  }

  f();
}());
```

Due to environmental differences and other factors, it is best not to declare functions in the block-level scope. If you want to use it, it is best to write a function expression with let:

```javascript
{
  let f = function() {
    console.log('I am inside!')
  }
 }
```





### Const keyword

`Const` declares a read-only constant and its value cannot be changed once it is declared. Like let, it has no variable promotion, temporary dead zone, and no repeated declarations.

**Features**：

1. A value must be assigned when declaring a const variable, otherwise an error will be reported. (Because if you don't assign a value at the beginning, you can't modify it later, this variable has no meaning to provoke it.)
2. `Const` essentially guarantees that the value stored in the memory address pointed to by the variable remains unchanged. Therefore, for basic types, its value cannot be changed. For composite types, only a pointer to the real data is stored in the memory address pointed to by the variable. So only the pointer does not change, but the actual data can still be modified. For example, object or array, etc., can be operated on.



#### Freeze Object

If you really want to freeze an object, use the`Object.freeze()`method. This method can prevent the object from adding/deleting/modifying attributes. To completely freeze an object, the properties of each object type should also be frozen.

```javascript
Object.freeze(obj);
Object.keys(obj).forEach((key ,i) => {
  if (typeof obj[key] == 'object') {
    Object.freeze(obj);
  }
})
```





### ES6 method of declaring variables

There are 6 ways to declare variables in ES6：`var, function, let, const, import, class`.





### The top level object

The top-level object refers to the`window`object in the browser, and the `global` object in the node.

In ES5, global variables are equivalent to the properties of the top-level object. When a global variable is declared, it automatically becomes an attribute of the top-level object. Will cause some problems:

1. No errors will be reported for undeclared variables at compile time, and errors will only be reported at runtime. Because it is likely to be a property created by the top-level object, and the creation of the property is dynamic.
2. The attributes of the top-level object can be read and written arbitrarily, which is not conducive to modular programming.
3. The `windows` object has an entity meaning and represents a browser object. This is not appropriate, because we may attach some attributes to it that are not related to this entity.



ES6 attempts to unbind global variables and top-level objects. It stipulates that the variables declared by `var` and `function` (ES5's original variable declaration method) are still attributes of the top-level object, while global variables declared by `let, const, class` do not belong to the top-level object.



```javascript
var a = 1; // Use var to declare a global variable
windows.a; // 1

let b = 2; // Use let to declare a global variable
windows.b; // undefined
```



The top-level objects are not uniform in different implementations:

1. In the browser, `windows` is the top-level object, and `global` in node is the top-level object.
2. The `self` in the browser and web worker also points to the top-level object, there is no `self` in the node



#### globalThis object

ES2020 introduced `globalThis` as the top-level object, which exists in any environment. This ensures that the top-level object can be obtained by it in any environment.