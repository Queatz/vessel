# Vessel

Vessel is a new way of thinking.  It disbands traditional programming language paradigms in favor of exploring something refreshingly new.  Take it for a spin, we think you'll like it.

## Thinking in Vessel

Vessel has a few fundamental underlying concepts that are neccessary to get to understand the language comprehensively. It is expressive. It only has a small subset of reserved characters - no keywords.

Vessel neatly closes the gap between low and high level programming.  It can be used for communicating with graphics cards as well as making beautiful interfaces.

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
?    true conditional
??   false conditional
!    break
!!   loop
'    string literal
#    comment
```

## Project Structure

Vessel files end in `.v`. You can access other vessel files in various ways by assigning to the file itself, optionally specifying a name.

#### config.v
```
config {
  app-name 'My Awesome App'
}
```

#### app.v
```
:config
:system.print
:..relative
snapp-http-server:'https://github.com/SnappLab/snapp-http-server'

print ('starting %s' % config.app-name)
# prints 'starting My Awesome App'
```

## Basic Template

#### app.v
```
:system

app {
  run {
    system print 'hello'
  }
}

app[] run
```
This is a basic app entry point template.  Since scope lookup is allowed to fall outside the file, this could have been written in one line:

```
system print 'hello'
```

`system` would not be found in the file and would be looked up using the following precedence:

1. File or folder called `system` in the same folder
2. File or folder called `system` in the system vessel include path
3. Package from the Vessel CDN called `system` (which would then be cached locally)


## Syntax

Vessel files are anonymous scopes. Usually you would define a single class within a `.v` file.

Vessel files are meant to be easy on the eyes with the possibility to minify.  Commas indicate separate statements.  Newlines also indicate separate statements, except when an "indetation block" is opened.

These are all the same
```
a, b, c

a
b, c

a, b
c

a
b
c
```

These are also the same:
```
a b c

a
  b
  c

a b
  c

a
  b
    c
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
material {

}
```

This is a class.  Simple and clean.

```
material [
  name string
]
```
This is a class with a single `string` property called `name`.  Have you noticed the bracets have changed?  Don't freak out yet -  let me explain. Vessel separates data from logic, public from private, cats from dogs.  Nothing is intermixed.

```
material [
  name string
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
material [] [
  name string
] {
  name {
    name
  }
  name string {
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
tree material {

}
```

This is class inheritance. Simple and clean.

```
pine tree material {

}
```

This is multiple class inheritence.  Again, simple and clean.

## Operators and Logic

Operators, or more traditionally, functions, are simply scope blocks.  They can live as methods of classes or passed around anonymously.

```
fish {
  name string
}

bucket {
  fishies list
  
  + fish { contents append .., . }
  
  + bucket {
    .. each { contents append .. }
    .
  }
}
```

```
rosie: fish['rosie']
goldie: fish['goldie']
pinky: fish['pinky']

beckys-fishies: bucket[contents: [rosie]]
mandys-fishies: bucket[[goldie]]

all-fishies: beckys-fishies + mandys-fishes
all-fishies + pinky

system print | all-fishies size
# 3
```

## Standard Library

### object

The base from which all things are.

### list

A list of objects

### map

A map of keys and objects

### string

A string

### number

A number.

 * integer
 * float
 * double
 * long

## Additional Notes

Extending the Vessel language is also easy.  It is written in C++.  Check [this page](wiki) for more information.

And thanks!  We hope you enjoyed the read. <3
