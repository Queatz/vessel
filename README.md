# Vessel

Vessel is to programming languages as assembly is to hardware. It provides an abstraction on top of domain-specific languages such as C++, JavaScript, Python, and other high- and low-level languages, as well as scripting and templating languages such as HTML and CSS. It provides a single conceptual approach that compiles into domain-specific targets. Take it for a spin, we think you'll like it.

See [Why Vessel?](https://github.com/Queatz/vessel/wiki/Why-Vessel%3F), [Guess the Number](https://github.com/Queatz/vessel/wiki/Example:-Guess-the-Number), and [Greet Me](https://github.com/Queatz/vessel/wiki/Example:-Greet-Me) to skip right to some examples.

## Thinking in Vessel

**Vessel cares about: Language** 

Otherwise known as: *Explain Yourself*

A new set of eyes should always be be able to easily trace and understand what's happening in any line of code.  This is achieved with static typing, clear referencing, and - no magic allowed.

**Vessel cares about: Abstractions** 

Otherwise known as: *Don't Repeat Yourself*

It shouldn't ever be easier to write equivalent logic two times.  Abstract and reference it. Single source of truth, always, no exceptions, period.  A codebase should be able to scale up without amounting technical debt. Everything is decoupled and easy to hot swap.  This is made even cleaner with built-in dependency injection. See [Dependency Injection](https://github.com/Queatz/vessel/wiki/Dependency-Injection)

**Vessel cares about: You**

Programming languages should help their counterpart out by being clean, concise, and readable.  They should encourage long-term principles from the beginning and cause the programmer to write beautiful code without effort.


**App.v**
```
Animal {
  name String
  tummy: []

  eats Animal {
    tummy add ..
  }
}

cat: Animal['Kitty Cat']
dog: Animal['Gruffy']

cat eats dog
```

## Writing in Vessel

Vessel is easy. Designed with writing sentences in mind, it flows unobstructed from your brain to the keyboard.  Writing clear and concisely is heavily promoted through the language's design, with future-proofing built right in from the beginning of your project.

### Reserved Characters

```
{ }  logical scope block
[ ]  object
( )  expression grouping
,    statement separator
.    left accessor
..   right accessor
:    assign
::   const assign
?    true conditional
??   false conditional
!    break
!!   loop
'    string literal
#    comment
```

## Expressions

```
a           # just a
a b         # call b of a
a b c       # call b of a with c
a b c d     # call d of (call b of a with c)
a b c d e   # call d of (call b of a with c) with e
```

By default, expressions are greedy groups of up to a maximum of three parts: left, middle, and right. Or more plainly: context, operator, and argument.

All the above are valid code with their associated meanings.

```
(a)
(a b)
(a b c)
((a b c) d)
((a b c) d e)
```

This is exactly the same as above.

```
(a, b, c)
```

This evaluates to `c` since the last value in an expression is the result of that expression.

### Order of Operations

`#` > `( )` > `,` > `?` > `:`

## Classes and Functions

```json
Material {

}
```

This is a class.  Simple and clean.

```
Material [
  name String
]
```
This is a class with a single `String` property called `name`.  Have you noticed the brackets have changed?  Don't freak out yet -  let me explain. Vessel separates data from logic, public from private, cats from dogs.  Nothing is intermixed.

```
Material [
  name String
] {
  getName {
    name
  }
}
```
Let's walk through this one. Brackets hold data. Braces hold logic. Brackets are dynamic.  Braces are static. Simple.  In our case here, we define a function called `getName` that simply returns the name.

Every block in Vessel is a list of expressions.  The very last expression evaluated in a block is equal to the return of that block.

We could make the property private and use this function as the only way to read the name.  Let's do that and add a function to set the name as well.

```
Material [] [
  name String
] {
  getName {
    name
  }
  setName String {
    .name: ..
  }
}
```

Here we specify no public properties, hence the empty definition block.  The second definition block holds private properties.

Because we are assigning a variable in the `setName` function, we have to be careful to access it from the context scope (left accessor.) We do this by preceding the name with a period.

There's also something else interesting in here, that `..`.  In Vessel, expressions have access to their left and right counterparts.  You can think of the left counterpart (`.`) as a traditional reference to `this`, and the right counterpart (`..`) as a single argument. In this setter we're assigning the `name` property of `this` to the right counterpart, or the argument, which is defined as a `string`.

You can also name these data and logic groupings which can come in handy when doing reflection.

```
class {

} parsing {
  # various methods related to parsing this class
}
```

Let's have look at some more interesting things we can do with classes.

```
Tree Material {

}
```

This is class inheritance. Simple and clean.

```
Pine Tree Material {

}
```

This is multiple class inheritance.  Again, simple and clean.

### Arrays

```
Mat4 {
  values Double 16
}
```

A number after member definitions indicates that it is repeated x times.

```
Mat4[].values 8
```

Accessing individual elements in the array is as simple as calling the array with a number. Here we access the 8th value from a newly created `Mat4` object.

```
Mat4[].values 8: 22.0
```

Here it is assigned to 22.

## Objects

Classes can be instantiated into objects.  Let's play with some cats.

```
Cat {
  name String
  born Date

  meow {
    system print ('Meow! I, %s, was born on %s.' % [name, born])
  }
}
```

```
Cat[]
```

This creates a new object of type `Cat`.

```
Cat['Kitty']
```

This creates a new `Cat` with its `name` initiated to `'Kitty'`.  Ironic.

Note that when using a list initiator as above, the order of members as defined in the `Cat` class is mapped to the order supplied in the initiator.

```
Cat[born: Date from-string '10-10-2015']
```

For this poor nameless kitty cat we only want to supply a birthday, so we must use a mapped initiator to skip the name.

## Scope

Scopes are defined with braces `{ }`. Vessel files are anonymous scopes.  If a single scope is defined with an equal name to the file's name, it will be collapsed into the file's scope.

App.v
```
run {

}
```

is the same as

App.v
```
App {
  run {

  }
}
```

### Imports

Scopes can depend on other scopes.

```
:Service
```

This is a shorthand for:

```
Service: Service
```

It could also be referenced by its absolute path:

```
:my.app.services.Service
```

And this is a shorthand for:

```
Service: my.app.services.Service
```

### Scope Injection

There is no requirements where you can import scope.

```
App {
  run {
    :Service
  }
}

App{run.Service: MyCustomService} run
```

By importing `Service` within the `run` function, this allows us to plug in a new `Service` to be used when calling `run` for this `App` object.  See more in [Scope Injection](https://github.com/Queatz/vessel/wiki/Dependency-Injection#scope-injection-static).

See the [Scope Waterfall](https://github.com/Queatz/vessel/wiki/Scope-Waterfall) for more information on how imports are resolved.

## Logic

### If / Then / Else

A good way to think of conditionals in Vessel is with an implied "if" at the beginning of conditions, with `?` spoken as "then" and `??` spoken as "else."

if a then b
```
a ? b
```

if a then b else c
```
a ? b ?? c
```

if a then b else if c then d
```
a ? b ?? c ? d
```

if a then b else if c then d else e
```
a ? b ?? c ? d ?? e
```

A practical if block might look like:
```
success ? {

} ?? {

}
```

Another example, if you only need a false conditional block:
```
success ?? {
  system print 'error'
}
```

### Looping

Vessel favors loop and break commands over blocks.

```
[1, 2, 3] each {

}
```

Here, the elements are iterated in an object-oriented fashion.

```
x: 0, {
 
} while { x++, x < 100 }
```

A traditional for loop.

```
{
  system print 'alive'
} while { alive }
```

This is a while loop that evaluates `alive` before each loop.

```
{
  system print 'alive'

} while { alive }
```

This is a `do...until` loop that will always run at least once.


### Try / Catch

Error handling in Vessel is as simple as breaking with an assocated error object.

```
! 'error'
```

Thrown objects can be caught with an 'on error' conditional after expressions:

```
expression ! {

}
```

You can also catch only certain objects by specifying a type:

```
expression ! NotFoundException {

}
```

## Standard Library

### Scope

The base from which all things are.  All low-level language access happens through this class.

### Object

An instantiated scope.

### List

A list of objects

### Map

A map of keys and objects

### String

A string

### Number

A number.

 * Integer
 * Float
 * Double
 * Long

## Additional Notes

Extending the Vessel language is also easy.  It is written in C++.  Check [the wiki](https://github.com/Queatz/vessel/wiki) for more information.

And thanks!  We hope you enjoyed the read. <3
