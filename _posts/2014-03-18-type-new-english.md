---
layout: post
title: Type.new()
---

> NOTE: This is a translation of [my spanish post](http://blog.amatiasq.com/2014/03/type-new/), if you speak spanish I recommend you the original version

## Constructors in Javascript

I've talked before about the limitation of constructors in javascript and the complexity it is to extend them.

```javascript
function Person(name) {
  this.name = name;
}
Person.prototype.methodA = function() { ... };

function Employee(name, position) {
  Person.call(this, name);
  this.position = position;
}
Employee.prototype = Object.create(Person.prototype);
Employee.prototype.methodB = function() { ... };
```

This topic is responsible for headaches of the majority of people developing javascript, the complexity needed to create a simple "class". This lead us to the point where the next version of ECMAScript (for those of you than don't know it, it's the standard Javascript is based on) has included a simplest way to do the same: the `class` keyword.

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  methodA() { }
}

class Employee extends Person {
  constructor(name, position) {
    super(name);
    this.position = position;
  }
  methodB() { }
}
```

<a target="_blank" href="http://www.es6fiddle.net/hsq7hzw6/">Try me!</a>

Although I've seen a lot of people exited thinking than ECMAScript 6 classes will be real clases I must say this code does nothing more and nothing less than the first code. And it's really important to know it because although it looks like Java or C++ classes, in this case it's still being object using [prototypal inheritance](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain) and hide it will only help us to not know why the code isn't working as we expected.

In any case we see than type definition in javascript is a complicated topic and, in my opinion, the solution proposed by the ECMA team is not the more accurate one.

## Object oriented

I think the problem lies on the definition we give "Object Oriented Programming" in first place, given than main OOP languages use to create object using classes and although objects are the base of the sistem the structure is given by classes. What I call OOP with classes.

When other language also defined as OOP but focused in a different way, Javascript between them, in this case the language has not classes but everything are objects and the structure is created using protitypes and every object can be the prototype of another object, and that meas than if B prototypes A, every property on A should also exist on B. This is what I call OOP with prototypes.

I've faced with a lot of people than thinks than OOP is not possible without classes so if Javascript has not classes it cannot be called OOP language. As on every geeks discussion we end on [Wikipedia](https://en.wikipedia.org/wiki/Object-oriented_programming#Fundamental_features_and_concepts):

> For example, object-oriented programming that uses classes is sometimes called class-based programming, while prototype-based programming does not typically use classes.

To be clear, a object oriented language is the one than has object (clear boy) and fulfills a set of features (dynamic dispatch, encapsulation, subtype polymorphism, inheritance, open recursion...). While in Java this is implemented using classes Javascript uses prototypes.

## Javascript Beginings

Sure most of you know this better than I do but as far as I know, I don't think it's true but it helped me in this case. Javascript was first called LiveScript, legend has it that at that time Java growing up and for marketing reasons they decied to call the new language Javascript. And for the samen reason they decided to modify the language to look more like Java adding, with another features, the `new` operator to look like it had classes.

Something curius about constructors in Javascript, besides than they are simple functions, is than every function in javascript has the property `prototype` than has by default a object than only has a single property, the `constructor` property. That is the constructor itself.

```javascript
function Testing() { }
console.log(Testing.prototype.constructor === Testing);

var proto = Testing.prototype;
console.log(proto.constructor.prototype === proto);
```

<a target="_blank" href="http://jsfiddle.net/amatiasq/Pb2Ap/">Try me!</a>

## Constructors vs prototype objects

This made me think maybe the original intention of javascript objects was **not to have constructors than contain prototypes but to have prototypes than contains constructors**. That is: instead of...

```javascript
function MyType() {
  this.id = 1;
}
MyType.prototype.methodA = function() { ... }
```

Do this...

```javascript
var MyType = {
  constructor: function() {
    this.id = 1;
  },
  methodA: function() { ... },
};
```

Cool! Doesn't it look like a more simpler way to declare types? [Here](https://gist.github.com/amatiasq/5215294) we can see the same type written with constructors and with this paradigm so you can judge yourselves. And what happens when we try to invoke the constructor? do we have to use `.call()` or `.apply()` to change it's context?

```javascript
var instance = Object.create(MyType);
instance.constructor();
```

BOOM! The constructor already has `this` because it's invoked directly on the instance! Isn't it way simpler and logic from this point of view?

Also by accident we have wiped out the constructor function and what we have is a simple object, the more basic element on OOP. I mean, to declare a type we only have to create an object, and to prototype an object we only need one step.

```javascript
var SubType = Object.create(MyType);
```

We are not forced, unlike the first case, to create a new constructor to create a subtype, by prototypal inheritance we have the same constructor than `MyType`

```javascript
console.log(SubType.constructor === MyType.constructor); // true
```

<a target="_blank" href="http://jsfiddle.net/amatiasq/VRKYv/">Try me!</a>

And the best part, what happen if we want to create a type without constructor? No problem.

```javascript
var MyType = {};
console.log(MyType.constructor); // Object
```

<a target="_blank" href="http://jsfiddle.net/amatiasq/yGJLL/">Try me!</a>

## ECMAScript 6

This paradigm looks very similar to the way to create classes in ECMAScript 6.

```javascript
class MyType {
  constructor() {
    this.id = 1;
  }
  methodA() { ... }
}
```

Someone can say "yes, but with ECMAScript 6 classes we can extend, call parent method with `super` and we don't have to write `function` everywhere...". True, but these are not ECMAScript 6 classes' features, these are features of [all objects literals](https://github.com/lukehoban/es6features#enhanced-object-literals) in ECMAScript 6.

```javascript
// Clase ECMAScript 6
class Employee extends Person {
  constructor(name, postition) {
    super(name);
    this.position = postition;
  }
  methodA() { ... }
}

// Objeto en ECMAScript 6 estándar
var Employee = {
  __proto__: Person,

  constructor() {
    super(name)
    this.id = 1;
  },
  methodA() { ... }
};
```

The differences between a ECMAScript 6 class and a ECMAScript 6 object are minimal, but as a class makes us think `Employee` will behave like a Java class (and it won't), a object is just that, a simple object; and we all are capable to understand how a object behaves, right? (otherwise this is not the article you need to read :P).

But let's put ECMAScript 6 aside for now, we'll need time until we can use it for big projects.

## Instanciation

That was type definition, but how do we instanciate it? First we'll need to think what a instance is, if we haven't classes can we have instances? Acording to Wikipedia

> In computer science, an object is a location in memory having a value and referenced by an identifier. An object can be a variable, function, or data structure. In the object-oriented programming paradigm,"object," refers to a particular instance of a class where the object can be a combination of variables, functions, and data structures.

At least for me, the word doesn't change too much, the issue is we need to create object to be prototypes and we want to create "instances" of this prototypes. The way to prototype an object is using `Object.create()`.

```javascript
var instance = Object.create(MyType);
```

Wait a moment... this looks exactly the same we did to create a subtype, isn't it? Yes, it is.

Then what is the difference between a instance and a subtype? In general, nothing. **A instance IS a subtype** because it can be a prototype for other objects. But in the most cases "instances" have a need than subtypes don't: on a instance we invoke the constructor, on a subtype we don't. 

```javascript
var MyType = {
  constructor: function() {
    this.id = 1;
  }
};

// Create subtype
var SubType = Object.create(MyType);

// Create instance
var instance = Object.create(MyType);
instance.constructor();
```

This similitude between a instance and a subtype helps us understand how Javascript at the end is reallly, really simple: everything is an object; there is no difference between a type and a "instance" because the difference is conceptual.

This is really useful to understand Javascript's simplicity and core, but it's a little bit tedious to have to do two steps to instanciate, can we simplify it?

## `Type.new()` is the new `new`

The truth is that we could, we can create a function to do this process:

```javascript
function createInstance(Type) {
  var instance = Object.create(Type);
  instance.constructor();
  return instance;
}

var Type = {
  constructor: function() {
    this.id = 1;
  }
};

var instance1 = createInstance(Type);

var TypeWithoutConstructor = {};
var instance2 = createInstance(TypeWithoutConstructor);
```

<a target="_blank" href="http://jsfiddle.net/amatiasq/Jry2Z/">Try me!</a>

And what happens if instead of call it `createInstance` we call it `$new` for example?

```javascript
var instance = $new(MyType);
```

It starts to look similar, we only need to chante `$new` function to pass parameters to the constructor function.

```javascript
function $new(Type, params) {
  var instance = Object.create(Type);
  instance.constructor.apply(instance, params);
  return instance;
}

var Type = {
  constructor: function(name) {
    this.name = name;
  }
};

var instance = $new(Type, [ 'bob' ]);
```

<a target="_blank" href="http://jsfiddle.net/amatiasq/t8QCk/">Try me!</a>

Looks like it works, but just to finish it, why don't we set `$new` as a `Type` method? this way we could pass the arguments without the array and as ECMAScript 5 allow us to use kewords as properties we can call it simply `new`.

```javascript
var Type = {
    new: function() {
        var instance = Object.create(this);
        instance.constructor.apply(instance, arguments);
        return instance;
    },
    constructor: function(name) {
        this.name = name;
    }
};

var instance = Type.new('bob' );
```

<a target="_blank" href="http://jsfiddle.net/amatiasq/x3qM4/">Try me!</a>

And we have a way we can use with ECMAScript 5 to create types and instances in a simple way. But what is the difference between doing this and using `new Constructor()`? Besides the simplicity of this pattern to create and extend types, it has more advantages, mainly because it allow us to control exactly **how** each type creates it's subtypes. In some cases you'll find useful to replace the `.new()` method for another function in a specific type for memory optimization or extra functionallities but this is beyond the reach of this post.

## Conclusion

After spending years trying [hundred](https://gist.github.com/amatiasq/4038135) [of](https://gist.github.com/amatiasq/5215294) [ways](https://gist.github.com/amatiasq/5254098) [to](https://gist.github.com/amatiasq/5619166) [create](https://gist.github.com/amatiasq/6270563) [and](https://github.com/amatiasq/LifeJS/blob/master/lib/animal.js) [extend](https://github.com/amatiasq/-legacy-BRIAP/blob/master/src/core/base.js) ["classes"](https://github.com/amatiasq/-legacy-bio/blob/master/src/core/Base.js) [to](https://github.com/amatiasq/jsbase/blob/master/src/extend.js) [find](https://github.com/amatiasq/-legacy-Life/blob/master/lib/physic/Force.dart) [the](https://github.com/amatiasq/glib/blob/master/core/base.js) [simplest](https://github.com/amatiasq/lulas/blob/master/src/core/extend.js) fastest and more elegant, I keep [this one](https://gist.github.com/amatiasq/7892749) as my choice. The lesson Javascript gave me is than it doesn't worth it to fight it's nature, if we want to use Javascript successfuly the more reasonable is to use Javascript and not to use it against it's nature.

Time will tell but I think this system can even compete face to face with ECMAScript 6 "classes", but in any case the conversion between a type created using this pattern and the constructor is easy.

For example, to convert a type created with this pattern to a constructor to use it with `new`:

```javascript
var MyType = {
  myMethod: function() { ... },
};

function MyConstructor() {
  MyType.constructor.apply(this, arguments);
}
MyConstructor.prototype = MyType;
```

<a target="_blank" href="http://jsfiddle.net/amatiasq/tGD7G/">Try me!</a>
    
And for convert a constructor into this pattern:

```javascript
function MyConstructor() {
  this.value = 1;
}
MyConstructor.prototype.myMethod = function() { ... };

var MyType = MyConstructor.prototype;
// and if you wish...
MyType.new = $new;
```
    
<a target="_blank" href="http://jsfiddle.net/amatiasq/5R4z2/">Try me!</a>


## Initializer

Finally a bonus, after all this journey I've realized than the constructor, than in Javascript looks so important, isn't. If we stop to see the constructor we see it's a simple function.

```javascript
function MyType() { ... }
```

It has nothing special, even we can invoke it as a function and it doesn't construct anything. Then who constructs? `new` operator is the one than creates a new object and then invokes the method called `constructor`, than doesn't have any difference with any other method the object may have.

But as I see it, the function constructor does more initialization than construction, it should be responsible for initialize the object's properties, not to construct. Seeing it this way it's obvius than the name "constructor" it's not the more appropiate one, in my case I prefer to call it "initializer" or just "init", as Backbone does on it's objects.

That's why on my projects when I use this pattern, I prefer my function `$new` to invoke the method `init` instead of the method `constructor`.

```javascript
function $new() {
  var obj = Object.create(this);
  obj.init.apply(obj, arguments);
  return obj;
}

var MyType = {
  new: $new,
  init: function() {
    this.value = 1;
  }
};

var instance = MyType.new();
```

<a target="_blank" href="http://jsfiddle.net/amatiasq/7h7Te/">Try me!</a>
