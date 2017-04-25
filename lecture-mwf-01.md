        # Welcome!

# Administrative stuff (20 mins)

* goal of the class: you should be able to choose and implement data
  structures to help you solve problems.
* labs
* projects
* class policies
* readings
* honesty policy
* handin


# Building a model of evaluation (30 mins)

Meta-cognition: how do you figure out how to write programs? You must
first be able to read them.

What does it mean to "read" a program? Broadly, it means translating
the program into thought-forms; something inside your head. Some
network of connections, starting with a "big idea", and proceeding
downward. Yikes!

I claim that in order to perform this translation,
you need a mechanical understanding of how a program evaluates; what
terms in the program mean. What kind of model do we use? Let's consider
some of the simplest programs

8 + 9

Do you know what this program evaluates to? Yes, you do.

## Functions

```python
def f(x):
  return 34 * x
```

Do you know what this program evaluates to? Do you know what it means?
Are these the same thing?

Okay, let's bring it back down to the ground. Here's another example:

```python
def f(x):
  return 34 * x

f(3) + f(4)
```

Do you know what this program evaluates to?

Explain to your neighbor how you figure out the meaning of this program.
Crystallize your understanding by writing down the different kinds
of program pieces, and how they're evaluated.

(hopefully, students see that there are several different kinds of "pieces"
in this program: additions, multiplications, function definitions, and
function calls. They probably won't see variable references as program
pieces, and I think I would let this lie, for now. Also, students may differ
in whether they model function evaluation using substitution or not. Frankly,
I'm curious to know how the students vote.)

## Kinds of data

This is a data structures class! Let's talk about different kinds of data.

What kinds of Python data do you know about?

(Write answers on board; numbers, strings, booleans, probably lists, maybe objects.
Ask students for examples of each one that they come up with.)

(Next, ask students for operations that they can apply to each kind of data.
In some cases, of course, certain operations require arguments of multiple
different kinds.)

Is there a difference between infix operators like + and * and functions like
pow(x,y) ?

(discussion)

So: no, not really. Well, syntactically maybe.

### Objects

Can you define your own kinds of data?

(Discussion)

Sure you can; you can use "Classes" to define new kinds of "Objects".

What's a class?

What's an object?

What's the difference?

Okay, so we define classes, and then we can create objects of those classes.

Let's try it. Suppose I have a Point class, with two fields, x and y.

What does this look like?

(They should be able to define this. They'll probably come up with

```python
class Point:
  def __init__(self, x, y):
    self.x = x
    self.y = y
```

Quick side question for those of you know Java but not Python: what's different?

(no explicit field declarations.) How do you feel about that? (It's scary.)

### Welcome to The Hot Place: stringification and equality

Okay, let's try it out.

(Type in class definition, create point such as Point(3,4))

Here's what it says:

```
<__main__.Point object at 0x1105d28d0>
```

Holy Moley? What the heck is that?!?

(Discuss.)

It turns out that by default, Python does a pretty crummy job of displaying
objects.

Can we do a better job? Sure we can! It's just ... painful.

Here's what it looks like:

```python
    def __repr__(self):
        return ("Point(%r, %r)" % (self.x, self.y))
```

Okay, let's try it out. What does it look like now?

```
Point(3,4)
```

Hey, awesome!

Well, sort of awesome. What's going on here? Our __repr__ method is explicitly
constructing this representation. That's painful, and error-prone. Sorry.

Okay, time for everyone's favorite topic: Equality.

What's the name of the python equality operator? That's right, it's '=='.

When should two points be considered equal?

(discuss)

Yep, when they have the same x and y values.

Sadly, that's not what Python gives us:

```
>>> (Point(3,4) == Point(3,4))
False
```

Darn!

Well, it turns out we can fix this one, too. Sigh.

```python
    def __eq__(self, other):
        return ((type(other) == Point) and (self.x == other.x) and (self.y == other.y))
```

Let's try again:

```
>>> (Point(3,4) == Point(3,4))
True
```

Yay! Or, sort of yay, anyway. We got the right answer, but it required some
work. 

Sadly, this is the format that we're going to be using for the rest of the class.

Fortunately, we wrote a little boilerplate-generator for you. It's online, at

https://brinckerhoff.org/csc202-class-template/

Try it out!

--

Copyright (C) 2017, John Clements (clements@racket-lang.org)
