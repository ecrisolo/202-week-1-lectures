# Last half of Design Recipe, Starting with Mutation and Mutable Values

Sigh. There's just too much material in the design recipe to fit in one lecture.
Okay, Where were we?

## Review (5 minutes)

What did we do last time? What parts of the Design recipe do we know about?

## Design Recipe (cont'd.)

### Step Three: Test Cases (15 minutes)

Test Cases! Delicious! I *love* test cases. They're like sticky buns. MMMMMM!

Okay, story time.

You're exploring a dusty, dystopian, post-apocalyptic world. The landscape
is littered with a tangled mass of old wires and broken gears. Towering
pillars of rusty metal arch above you. Everything is still. On a dusty shelf
sits a corroded nozzle, and a button next to it is labeled "fresh hot coffee."

Do you press the button?

Okay, scenario two:

You're exploring a dystopian, post-apocalyptic world. You stoop to enter
a low building. Inside, it's warm, and the reassuring hum of machinery
reaches your ears. There is a sink, with recently-used cups in it. On
a shelf, you see nozzles and tubes. You see what looks like coffee, being
brewed. There is a cup on a shelf below the coffee maker, and a button
labeled "fresh hot coffee."

Do you press the button?

Okay, so maybe you don't like coffee.  Do you see the difference between
these two scenarios? The first one is lifeless and alarming. The second
one, while perhaps not *comforting*, is at least somewhat reassuring.
You can see the machinery working. There's a reason to believe that it's
doing something.

This, in a nutshell, is what test cases are for. Test cases are the living,
breathing, reassuring mechanical hum of your code. When I see that there
are test cases, and that the test cases pass, I gain some slight belief
that the code actually works, and does something useful. Code without test
cases is frightening and dangerous.

You can tell I'm selling this hard, and I have to, because Python's test
case frameworks are really quite bad. They seem to have been informed
by JUnit, and like JUnit, they make writing tests painful. There's no good
reason why test cases should be so bad, and I'll write my own framework
just as soon as I have a spare minute. Sigh.

First, let me differentiate between "unit" tests and other kinds of tests.
The test cases we'll be writing in this class are almost entirely unit
tests. Unit tests are tests of a single function, or "unit".

Okay, let's write a test of our income_level function. A test case
involves inputs, and an expected result. What might we supply as arguments,
and what should the result be?

(discuss.)

One point I'd like to make here: do you notice how this is the first time
when we actually discussed how the function is going to work? One of the
principal benefits of writing test cases is that they force you to think
about what the function should do *before* you start writing it. This is
the right time to think about it. This is called "design." It may well be
when you sit down to write test cases that you realize that the idea you
had in your head is not going to work, or that you need to change your
data definitions.

Remember: changing your data definitions or signature at this point is
easy; you haven't written the body of the function yet. The whole goal
of the design process is to ensure that you don't waste effort by writing
an enormous amount of code that you later have to throw away.

So, here's the test case I came up with:

```python
income_level(Household("SLO",[342],346),15000,80000)
# should be
"low"
```

In order for Python to run this test automatically, we're going to have
to smash all kinds of nastiness in there.

First off, at the top of the file, we need

```python
import unittest
```

Then, down at the bottom of the file, we need this

```python
if (__name__ == '__main__'):
  unittest.main()
```

This tells python to run our tests if this is the main file.

Finally, we need to declare our tests in a class that inherits
from unittest.TestCase (whatever that means...), using methods
whose name begins with `test`:

```python
class TestCase(unittest.TestCase):
    def testa(self):
        self.assertEqual(3,3)
```

Each test case should have its own test method. This makes me
sad, so I actually generate them automatically using Racket...
but let's not talk about that, now.

So, here's how we'd add a test for income_level to this class:

```python
class TestCase(unittest.TestCase):
    def testa(self):
        self.assertEqual(3,3)

    def test_income_level(self):
        self.assertEqual(income_level(Household("SLO",[342],346),15000,80000),"low")
)
```

Let's add tests for the other two levels:

```python
class TestCase(unittest.TestCase):
    def testa(self):
        self.assertEqual(3,3)

    def test_income_level(self):
        self.assertEqual(income_level(Household("SLO",[342],346),15000,80000),"low")
        self.assertEqual(income_level(Household("SLO",[342],46743),15000,80000),"medium")
        self.assertEqual(income_level(Household("SLO",[342],289344),15000,80000),"high")
```

You'll notice that I put them all in the same method. It would be a bit
nicer to put them in separate methods so that

* the name would identify the specific test that failed, and
* all of the tests would run even if an earlier one failed.

Despite this, I think it's just too painful for you; I'm fine with putting all of
the tests for a method in a single test method.

Are we done? In a method like this, it's pretty reasonable to ask about the boundaries:
what if a household has an income of *exactly* $80,000? Let's say that we "round up":

```python
        self.assertEqual(income_level(Household("SLO",[342],15000),15000,80000),"medium")
        self.assertEqual(income_level(Household("SLO",[342],80000),15000,80000),"high")
```

Wow! That's a lot of tests. In particular, all of the repeated
`Household` stuff looks irritating. In fact, that's probably an
indication that really, this function should be stripped down to one
that just accepts the income number and returns the range. Let's leave
it as it is, though; there's a tension between programs that are simple
enough to easily understand in a lecture setting and programs that are
complex enough to benefit from the design recipe.

### Step Four: Template (0 minutes)

The next step is the template. For some programs, the template step will be very
helpful--most notably, in programs that operate on lists and trees, or that use
mixed data. We're going to omit discussion of the template step, for now.

### Step Five: Write the body of the function (5 minutes)

Okay, it's time to write the body. I think you have the tools for this
already. Can you write it with a partner? (time to write the function)

Here it is:

```python
def income_level(household,low_mid,mid_high):
    if (household.income < low_mid):
        return "low"
    elif (household.income < mid_high):
        return "medium"
    else
        return "high"
```

Now we run the tests. Did they pass? Yes! I hope so. If they didn't, you
know what to do...go back and fix them. You may be going back into step
five, but you may also be going back further; to step three, step two, or
even step one. We hope you don't have to go back to step one.

## Moving on: Mutable Values (25 minutes)

Okay, I need to tackle what is, for me, an extremely tricky issue.
I'm going to present you with two questions, and you're going to see
that you might answer the same question in two different ways.
Let's see how that goes.

First: here's a quarter. Do you see it? Yes. Okay, now I'm putting the
quarter in my pocket.

Where's the quarter? (In my pocket.) Yep, it's in my pocket.

Does one of you have a quarter in your pocket? (Yes.) Okay, is the quarter
in your pocket? Whaddaya mean, "which quarter?" You mean there's more than
one quarter? Are they different, my quarter and your quarter? Yes? How can
you tell? (By marking the quarter.)

Okay, fine. Be that way.

Next question.

Here's a number. (write 6 on the board.) I'm going to put this number inside
of a point. Here I go: (write (7,6), erase the 6.). Is this thing a point? Sure it is, it's
the point with X coordinate 7, and Y coordinate 6.

Okay, here's the question. Where's the six? Is it in the point? Did I
put the six in the point? Where does the 6 go when I erase it
completely? Here, let me write another point: (6,4). This point has a six in it,
too! Which one is the original six? Are there two sixes now? Where did the
other six come from? Can I use the same test that I used on the quarters? put
a mark on one of them? Maybe add one, and see if they're still the same.

These questions are silly, aren't they?

But why is the quarter different from the point?

Why doesn't it make sense to "change" the six into a seven?

Here's why: we perceive math in a different way than we perceive
physical objects. "six" is an *idea*. If I write a six and then
erase it and write a seven, I didn't change the six into a seven,
I just wrote down one idea and then wrote down a different one.
The notion of the number is different from its concrete representation.

We think of objects, though, as being persistent. As being mutable. If
I take this quarter and make a mark on it, it now makes sense to say,
"this quarter has a mark on it." If I bend it or cut it in half, we can
say, "this quarter is bent."

In fact, the mathematical way of thinking--the idea that we're
manipulating ideas, rather than physical objects--is the result of a
heck of a lot of very hard work by our culture. It's not a natural thing
at all. So why do we do it? Because it turns out that without this
big idea, the whole notion of algebraic manipulation goes belly-up.
Suppose I say that "y = x + 1". Well, this may be true now, but what
if `x` changes? Then the equation becomes false!  Algebraic manipulation
crucially relies on the notion of immutability, and allows us to dispense
with the notion of time.

What does this have to do with programming?

Well, it turns out that when we program, we often mix together these
two modes of thinking, and that each one has virtues and costs.

Generally speaking, what I've been calling the "mathematical" way
of thinking is associated with "purely functional" programming, though
you might just think of it as "mathematical" programming. The other
mode, the physical mode, where things can be altered, this is what
we usually call "imperative" programming.

Why would you pick one or the other?

Well, it turns out that it's a lot easier to write correct programs
when writing in a functional style. This is because it's impossible
to do certain bad things. Put differently, in a functional style,
certain things just aren't possible.

Let's take this back to programming.

```python
a = Point(6,8)
b = Point(6,8)
```

Are these two things the same point? (Yes.) Really? How do I figure out
whether two points are the same? (They have the same x and y coordinate.)

What about the quarter test? What if I change one of them, and see whether
the other one changes? Here, let's try it.

```python
b.x = 19
```

Did the other one change? Oh... no. It didn't. So if we do things like this,
then we're living in the quarter world. Things can be changed. Things that
are "equal" to each other maybe might not be equal later.

What if we try this again, like this?

```python
a = Point(6,8)
b = a
```

Now let's try changing the `x` field again. What happens? This time they
*both* change. So this setup is different from the other one.

There are two solutions to this problem; the functional one, and the
imperative one. Let's look at each of them in turn.

Functional: you're not allowed to mutate values like that.

Imperative: equality doesn't work the way we discussed.

Both of these solve the problem. The first one *restricts what you can do*.

This makes some programmers very angry, because they don't want anything
to restrict their power. This is a bit like people who remove rev-limiters
and then destroy their engines.

The second one does not limit what you can do. Instead, it just makes it
possible to cause many different fascinating problems.

Let's be very concrete. Suppose I write a function that's supposed to
accept a point and return the sum of the absolute values of the `x` and
`y` coordinates.

Here's a test case, to illustrate:

```python
def test_manhattan_distance(self):
    self.assertEqual(manhattan_distance(Point(6,-8)),14)
```

Okay, so let's say I'm grading a student submission, and they pass the test.
Does this mean that their function is right? (No, maybe the function *always* returns 14.)
Does this mean that the function is correct for *this particular input* ?

No!

Consider this definition:

```python
def manhattan_distance(pt):
  pt.x = abs(pt.x) + abs(pt.y)
  return pt.x
```

Is this function correct? Well, it does return the right value, but it's a
frightening implementation. This is like a diamond appraiser who grinds
your diamond into dust, weighs it, and then says, "well, it looks like
your diamond *used* to be worth $1000."

The real kicker is this: when you make a mistake using mutation, you probably
won't notice it for a while. Suppose you're out running errands, and you
get home, and you notice there's a big ding in the rear passenger door.
How did that get there? Who bumped into your car? Well, if you've been
driving all over doing errands, it could be one of twenty different places.

Debugging programs using mutation is a lot like this. Something goes wrong,
and you don't notice it until much later. Then, you have to try to do
detective work to figure out where the bad thing happened. Then suddenly
it's six AM, and the sun is coming up. ouch!

So... how do we prevent this?

The easy way is simply not to mutate values. If a function doesn't mutate
any values, then it's guaranteed not to have these problems. This is
what we call a purely functional program.

The hard way is... to test that the values that you passed into the function
didn't get mutated, and neither did any other value that the function has
access to. This is a little like asking a kid "list all of the things that
you *didn't* break, today"; it's not really feasible.

So, looking forward: we're going to be talking about a whole lot of data
structures, and functions on those data structures. A lot of them will involve
mutation of values. Some of them won't. We'll be keeping our eyes open, and
trying to be cognizant of when we're mutating values, and when we aren't.
What you'll probably find is that most of your really tricky debugging problems
will arise from mutation of values.

--

Copyright (C) 2017, John Clements (clements@racket-lang.org)
