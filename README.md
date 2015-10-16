# Vessel

Vessel is a new way of thinking.  It disbands traditional programming language paradigms in favor of exploring something new.  Take it for a spin, we think you'll like it.

## Thinking in Vessel

Vessel has a few fundamental underlying concepts that are neccessary to get to understand the language comprehensively. It is expressive. It only has a small subset of reserved characters - no keywords.

Vessel neatly closes the gap between low and high level programming.  It can be used for communicating with graphics cards as well as making beautiful interfaces.

Main.v
```
App['My App', TranslateService['vi']] start
```

App.v
```
App [
  name String
] {
  start {
     system print | translate string '$1%s started!' % name
  }
} services [
  translate TranslateService
]
```

Strings.vi.v
```
'$1%s started!': '$1%s bắt đầu!'
```

TranslateService.v
```
TranslateService {
  string String {
    Strings has language ? Strings.(language).(..) ?? ..
  }
} private [
  language String
]
```

## Writing in Vessel

Vessel is easy. Designed with writing sentances in mind, it flows unobstructed from your brain to the keyboard.  Writing clear and concisely is heavily promoted through the language's design, with future-proofing built right in from the beginning of your project.

### Reserved Characters

```
{ }  logical scope block
[ ]  object
( )  expression grouping
|    glue or separate expression
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

By default, expressions are greedy groups of up to a maximum of three parts: left, middle, and right. Or more plainly: context, operator, and argument.

All these are valid code with their associated meanings:
```
a           # just a
a b         # call b of a
a b c       # call b of a with c
a b c d     # call d of (call b of a with c)
a b c d e   # call d of (call b of a with c) with e
```

The following is exactly the same as above:

```
(a)
(a b)
(a b c)
((a b c) d)
((a b c) d e)
```

You can use the glue and separate selector to help write cleaner expressions without all the parenthesis.  Again, this is equivalent to the above:

```
a
a|b
a b c
a b c | d
a b c | d | e
```

Note that `a b c | d e` would actually be `(a b c) (d e)`.

Gluing and separating is especially useful when you want to reverse the order of evaluation, such as if you want `(a b) c` "c of b of a":

```
a|b c  
a b | c
```

## Classes

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
This is a class with a single `string` property called `name`.  Have you noticed the brackets have changed?  Don't freak out yet -  let me explain. Vessel separates data from logic, public from private, cats from dogs.  Nothing is intermixed.

```
Material [
  name String
] {
  name {
    name
  }
}
```
Let's walk through this one.  Brackets hold data. Braces hold logic. Simple.  In our case here, we define a function called `name` that simply returns the name.

You might be wondering exactly what this function does.  It's a simple getter.  Every block in Vessel is a list of expressions.  The very last expression evaluated in a block is equal to the result of that block.

We could make the property private and use this as a getter.  Let's do that and add a setter along the way.

```
Material [] [
  name String
] {
  name {
    name
  }
  name String {
    .name: ..
  }
}
```

Here we specify no public properties, hence the empty definition block.  The second definition block holds private properties.

Because we are assigning a variable, we have to be carful to access it from the parent scope. We do this by preceding the name with a period.

There's also something else interesting in here, that `..`.  In Vessel, expressions have access to their left and right counterparts.  You can think of the left counterpart (`.`) as a traditional reference to `this`, and the right counterpart (`..`) as a single argument. In this setter we're assigning the `name` property of `this` to the right counterpart, or the argument, which is defined as a `string`.

You can also name these data and logic groupings which can come in handy when doing reflection.

```
class | public {

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

This is multiple class inheritence.  Again, simple and clean.

## Logic and Scope

Scopes are defined with braces `{ }`. Vessel files are anonymous scopes.

### If / Then / Else

b if a
```
a ? b
```

b if a else c
```
a ? b ?? c
```

b if a else d if c
```
a ? b ?? c ? d
```

b if a else d if c else e
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
{
  alive ?? ! 
  
  system print 'alive'

  !!
}
```

Here we are using the loop command `!!` to return to the beginning of the block.  We check and break at the beginning because we don't want this block running at all if the condition is never true.  For the `do...until` we can simply put the conditional at the bottom:

```
{
  system print 'alive'

  alive ? !!
}
```


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

An instanciated scope.

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
