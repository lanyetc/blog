---
title: Destructuring Assignment
date: 2020-10-10 19:45:55
tags:
categories: 
- Javascript
---





## Destructuring Assignment



ES6 allows to extract values from arrays or objects according to a certain pattern and assign them to variables. It is essentially a **pattern matching**, as long as the patterns on both sides of the equal sign are the same, the variable on the left will be assigned the corresponding value.

When the destructuring fails, the variable value is `undefined`. When the pattern on the right contains the pattern on the left but does not match exactly, it is called incomplete deconstruction.

```javascript
let [a,b] = [1,2]; // a = 1, b = 2
let [a,b] = [1]; // Deconstruction failed, a = 1, b = undefined
let [a,b] = [1,2,3]; // incomplete deconstruction, a = 1, b = 2
```



### Array deconstruction

等号右边是可遍历的解构（具有`Iterator`接口），比如`Array, Map, Set...`等等。当右边不具有`Iterator`接口时就会报错。

The structure on the right side of the equal sign is iterable (with `Iterator` interface), such as `Array, Map, Set...` and so on. An error will be reported when there is no `Iterator` interface on the right.

```javascript
// Set deconstruction
let [x, y, z] = new Set(['a', 'b', 'c']); // x = 'a', y = 'b', z = 'c'

// incomplete deconstruction
let [x, y] = [1, 2, 3]; // x = 1, y = 2
let [a, [b], d] = [1, [2, 3], 4]; // a = 1, b = 2, d = 4

// Error
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```



#### Default Value

Variables deconstructed and assigned can have default values. When the constructed variable value is strictly equal to ʻundefined`, the default value is used. The default value can also refer to other variables in the destructuring assignment, as long as the variable has been declared.

```javascript
// default value
let [a = 0, b = 0] = [1]; // a=1, b=0
let [a = 0, b = a] = []; // a=b=0
let [a = b, b = 0] = []; // ReferenceError: b is not defined
```





### Object deconstruction

The object can also be deconstructed and assigned. The variable takes the value of the attribute with the same name in the object, if not, it is `undefined`. Use destructuring assignment to assign a method of an object to a variable

```javascript
let {foo, bar} = {foo: 1, bar: 2} // foo=1, bar=2

let {log, cos, sin} = Math; // Assign the method of the same name in Math to the left variable
```



When the variable name is inconsistent with the attribute name, it should be written as：

```javascript
let { foo: baz } = { foo: 'aaa', bar: 'bbb' }; // baz = 'aaa'
```

In fact, the destructuring assignment of an object is a shorthand for the following form:

```javascript
let {foo: foo, bar: bar} =  {foo: 1, bar: 2} 
```

The internal mechanism of object deconstruction assignment is to find the attribute with the same name first, and then assign it to the corresponding variable. The former is the pattern, and the latter is the variable that needs to be assigned.



The deconstruction of the object can also specify a default value. When the object's property is strictly equal to undefined, the default value takes effect.

```javascript
var {x = 3} = {x: undefined};
x // 3

var {x = 3} = {x: null};
x // null
```



#### Object destruction of array

Because the array is a special object, the array can be deconstructed, and the keys of the array are equivalent to attributes.

```javascript
let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3
```



#### String destruction

字符串也可以解构赋值，此时字符串会被转换成一个类似数组的对象：

```javascript
let [a,b,c,d,e] = "hello"; // a='h', b='e',....
```



#### Number and Boolean destruction

When the right side of the equal sign is not an array or an object, it will be converted to an object first, and then deconstructed and assigned.

```javascript
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true

// Error
let {a} = null;
let {b} = undefined;
```

Since both numeric and boolean packaging objects have toString attributes, they can be assigned to s. If null and undefined cannot be converted into objects, an error will be reported.



### Destructuring assignment of function parameters

The parameters of the function can also use destructuring assignment, and can specify default values.

```javascript
[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]

function move({x = 0, y = 0} = {}) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```



### Parentheses in destructuring assignment

Cases where parentheses cannot be used:

1. Variable declaration statement
2. Function parameters. Function parameters are actually variable declarations, so parentheses cannot be used.
3. The pattern part of the assignment statement.

These will report errors:

```javascript
// 报错
let ([a]) = [1];
let {(a): bar} = {a:1};

function f((a)) {return a};

([a,b]) = [1,2];
```



The only situation where parentheses can be used:

The non-pattern part of the assignment statement can use parentheses.

```javascript
[(a), b] = [1, 2]; // a=1, b=2
{a: (b)} = {a: 1}; // b = 1
```





### Application of destructuring assignment

- **Swap variable values**

  ```javascript
  // swap the value of x and y
  let x = 1;
  let y = 2;
  
  [x, y] = [y, x];
  ```

- **Return multiple values from a function**

  ```javascript
  // return an array
  function example() {
    return [1, 2, 3];
  }
  let [a, b, c] = example(); // Get the corresponding values by order
  
  // return an object
  function example() {
    return {
      foo: 1,
      bar: 2
    };
  }
  let { foo, bar } = example(); // Get the corresponding value by the attribute name
  ```

- **Definition of function parameters**

  You can easily associate a set of parameter names with variables. If the parameter definition uses object destructuring, you can pass an object directly when calling the function, but you can use the corresponding variable inside the function.

  ```javascript
  function f({x, y, z}) {
    ... // function body
  }
  f({x: 1, y: 2, z: 3});
  ```

- **Extract JSON data**

  ```javascript
  let jsonData = {
    id: 42,
    status: "OK",
    data: [867, 5309]
  };
  
  let { id, status, data: number } = jsonData;
  
  console.log(id, status, number);
  // 42, "OK", [867, 5309]
  ```

- **Default value of function parameter**

  Avoid checking whether the parameters are undefined and assign default values inside the function.

  ```javascript
  jQuery.ajax = function (url, {
    async = true,
    beforeSend = function () {},
    cache = true,
    complete = function () {},
    crossDomain = false,
    global = true,
    // ... more config
  } = {}) {
    // ... do stuff
  };
  ```

- **Traverse the Map structure**

  If you want to get thekey and value when using `for...of...` statement to traverse the map structure, you can write:

  ```javascript
  const map = new Map();
  map.set('first', 'hello');
  map.set('second', 'world');
  
  // get key and value
  for (let [key, value] of map) {
    console.log(key + " is " + value);
  }
  
  // get key
  for (let [key] of map) {
    console.log(key);
  }
  
  // get value (value is the second one, front vacancy)
  for (let [,value] of map) {
    console.log(value);
  }
  ```

- **Load the specified method in the module**

  ```javascript
  const { SourceMapConsumer, SourceNode } = require("source-map");
  ```

