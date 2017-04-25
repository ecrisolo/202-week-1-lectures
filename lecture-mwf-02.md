# Design Recipe, Testing

Okay, this is the lecture where we talk about being responsible
citizens. Specifically, I'm going to try to present to you a *process*
for developing software.

Here's the process: go crazy and do whatever you like!

No? That's not a process? Ah well, I guess we have to pick a real process.

## The Design Recipe

Here's the goal: get the program written *efficiently* so you have the
time you need to sleep, eat, play, and do all your other homework.

Sound good?

As usual, getting things done efficiently means being careful and
working methodically. It means planning ahead, and thinking before
you type.

There are six steps in our design recipe (which, by the way, is
taken one hundred percent from the excellent book ``How To Design
Programs'', which is a totally amazing book---disclaimer: I know
the book's authors extremely well).

### Step One: Data Definitions (30 minutes)

Choosing how to represent your data is the single most important
decision you'll make when you begin to design a program.

This is a class about data structures, so it shouldn't be surprising
here, but in fact this applies to all of the work that you do.

Nearly everything else that you do stems from the decisions that you
make here.

Often, these decisions are made for you; for most of this class, I'll
be telling you what data structures to use for each of the labs. In
later assignments, you may be making these decisions yourself.

The basic question that you're trying to answer is this: "What values
in my programming language will I use to represent the elements of
my problem?"

#### Simple Data

Some decisions are (relatively) easy: if you're trying to represent
the height of a person, you probably want to use a floating-point
number. If you're trying to represent a pet's name, you probably
want to use a string.

Tell me about some *basic* kinds of python values:

(strings, ints, floats, booleans; try to fend off lists and
objects. Have them supply examples, too.)

These simple decisions give rise to simple data definitions. A data
definition is a piece of code that starts with a comment of the form
`A <whatever> is...`. So, for instance, if you decide that a pet
name should be represented as a string, you might write

```
# a PetName is a string
```

or

```
# an Income is a number, representing dollars/year of family income
```

Note that in these two cases, we're just giving more meaningful names
to existing data definitions. You should write this kind of data
definition only when it makes your code clearer and more readable.
There's absolutely no reason that you have to have a data definition for
every problem you solve. If your program just takes a number, that's fine,
and there's no need for a data definition specific to that program.

#### Compound Data

Other times, you may want to represent values that contain other values,
using objects. So, for instance, if you're operating on census data, you
migeht want to represent a household as an object with a location, a list
of people, and an income.

In Python, we can describe a set of objects using a `class`, as
you already know:

```python
class Household:
  def __init__(self, location, people, income):
    self.location = location
    self.people = people
    self.income = income

  # BOILERPLATE HERE
```

Note that I'm not going to bother writing all of the horrible
boilerplate for the __eq__ and __repr__ methods; you'll need to do
that when you want to run the code, though.

In addition to this, we're going to want to specify the kinds of values
that can be used in each field of an object, and the purpose of the Class
as a whole. Let's use comments for those:

```python
# A Household represents a household in the census data
class Household:
  def __init__(self, location, people, income):
    self.location = location # a Location
    self.people = people     # a list of Persons
    self.income = income     # a number of dollars/year

  # BOILERPLATE HERE
```

Note that in the case of the location and the people, we're referring
to other data definitions that are presumably defined separately.

For the sake of class, let's assume that these are both very simple:

```python
# a Location is a string, representing a city name
# a Person is an int, representing a person's id in the system
```


#### Mixed Data

Sometimes, you want data that can be one of two different things.

For instance, it may be the case that a geographical record refers
either to a house or to a commercial building. Those might be very
different records. We might want to write a program that accepts either
one of these. For this purpose, we might want a data definition that
gives us a choice:

```python
# a Building is one of
# - a ResidentialBulding, or
# - a CommercialBuilding
```

Where `ResidentialBuilding` and `CommercialBuilding` are separate
data definitions.

This is probably a good time to talk *very briefly* about type systems
and how they might play into this.

In a language like Java (how many of you have seen Java?), there's a
checker called a "type system" that checks your program ahead of time
to try to identify parts of your program that could fail. For instance,
maybe you're calling "substring" on a number. That wouldn't work.

Without going into details, it turns out that the Java type system
can allow a programmer to specify that there's a type called
`Building`, which is the union of the `ResidentialBuilding` and
`CommercialBuilding` types. Actually, that's not *completely*
correct, but it's pretty close. Also, if you want to create a union
of, e.g., String and Number, Java falls over completely, but don't
tell that to the Java lovers; they'll be all over you :).

Anyhow: Python does *not* have a type system (it has a runtime notion
of `type`, but this is not really a type system), so we need to write
this in the form of a comment.

If it's a comment, why write it at all?

(discuss.)

Answer: because programs are design documents. If the design isn't
clear, the artifact is worthless. (Always code as if the guy who ends up
maintaining your code will be a violent psychopath who knows where you
live. John F. Woods)

There are also common situations where one of the two choices may
be `None`. For instance: suppose a house *might* have a swimming pool.
You might write a data definition like this:

```python
# A MaybePool is one of
# - a SwimmingPool, or
# - None
```

If you know Java, you may know this special value as `null`, and you
may be delighted to hear that you're not required to declare it
explicitly...until you get NullPointerExceptions in running code
and your client decides to dump your product, costing your company
millions of dollars. The "null is a member of every type" idea comes
from Tony Hoare, who calls it his "billion dollar mistake." Oops.

This is not to say that it's not a valuable idea; we often want to
state that a value might be null; it's just that this should be declared
explicitly, as we've done in this data definition.

When we talk about how data definitions lead to templates, you'll see
how declaring this explicitly can prevent you from encountering errors
down the road.

#### Self-referential data

It turns out that something very interesting happens when we start
writing data definitions that contain references to themselves. That,
however, is the topic of the next lecture, not this one. So let's hold
off on it.

### Step Two: Purpose Statement, Contract, Header (15 minutes)

Okay, let's imagine we're writing a program, now. It's going to do
something simple. Let's suppose that we're trying to classify households
according to whether they're low, medium, or high-income households.
These divisions might come from statistics that we gather about the
distribution of incomes in the area, but let's assume that this part of
the program is just going to accept the divisions as inputs, and compute
which category a given household belongs to.

What kind of data will this program require? Well, it will take households
as inputs, and also numbers, and it might return a string. We'll use
the data definition for Households that we came up with earlier. Okay,
that was step 1, data definition. Now we're ready for step 2:

First off, we need a signature. This is a one-line comment that
describes what kind of values the function accepts, and what kind of
value it returns. It should contain an arrow to separate the arguments
from the result. In our case, we might decide to accept the household
first, and then the two numbers (dividing low from medium and medium from
high), and return a string. We'd write that signature like this:

```python
# Household int int -> str
```

Note that you can see from this signature exactly how many arguments
the function accepts, and in what order. In a language with a type
system, the signature would be replaced by a type.

Next, we need a purpose statement. This is a one-line comment
that states what the function will do:

```python
# return the income category for a given household
```

Writing purpose statements is incredibly important. There are two reasons
for this.

First, and more obvious: purpose statements help your graders and your
co-workers understand what a function is supposed to do. You would be
absolutely amazed at how hard it is to understand what a function called
"helper_thingy" is supposed to do.

Second, and less obvious: if you can't write the purpose statement, you
shouldn't be writing the function. Often you'll start writing a
function, saying to yourself, "I need a function here that does something
mumble mumble here lemme just start writing it." If you can't say ahead
of time what the function is supposed to do, then you shoudn't be writing
it. For Heaven's sake don't make Purpose statements an afterthought.

There is one obvious exception to this: if you are engaged in a creative
endeavor--writing a piece of music, playing with art--none of the rules
apply. You're just exploring and tinkering. It's fine to have "exploring
and tinkering" mode. However, you will find that "exploring and tinkering
mode" is *not* an effective way to get programs written in this class, or
any time when you know exactly what it is tht you need to get done.

Finally, we need to write the first line of the function, and a dummy
for the body:

```python
def income_level(household, low_mid, mid_high):
    pass
```

What decisions are we making here? (discuss) Yep: the name of the function,
the names of the arguments. Okay, so here's the whole program so far:

```python
# represents a household in the census data
class Household:
  def __init__(self, location, people, income):
    self.location = location # a Location
    self.people = people     # a list of Persons
    self.income = income     # a number of dollars/year

  # BOILERPLATE HERE

# Household int int -> str
# return the income category for a given household
def income_level(household, low_mid, mid_high):
    pass
```


--

Copyright (C) 2017, John Clements (clements@racket-lang.org)
