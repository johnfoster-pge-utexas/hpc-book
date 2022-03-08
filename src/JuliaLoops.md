---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Julia 1.7.2
  language: julia
  name: julia-1.7
---

+++ {"slideshow": {"slide_type": "skip"}}

# For and While Loops

Looping structures, i.e. `for` and `while` loops, are another method of flow control in Julia programming.  They can be used to perform structured and/or repeated tasks that occur numerous times in a compact way.

+++ {"slideshow": {"slide_type": "slide"}}

## For loops

+++ {"slideshow": {"slide_type": "skip"}}

For loops are a looping structure that allows for iteration over a fixed number of items.  The items can simply be monotonically increasing integers, in which case the loop simply cycles a fixed number of times.  Additionally, the items can by items from a Julia `Array`, `Tuple`, or `Dict` or other iterable types

For demonstration purposes, let's start with a Julia `Array` of type `Any`.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
alist = [1, 2.0, "hello"]
```

+++ {"slideshow": {"slide_type": "skip"}}

Now we can write a `for` loop that iterates over each item in the list one at a time and prints the item to the screen.

+++ {"slideshow": {"slide_type": "skip"}, "tags": ["popout"]}

In this loop `item` becomes a *variable* internal to the loop that each entry in `alist` gets assigned to one-by-one as the program cycles through the body of the loop until the end of `alist` has been reached.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
for item in alist
    println(item)
end
```

+++ {"slideshow": {"slide_type": "fragment"}}

The loop above is exactly equivalent to this code

```{code-cell}
---
slideshow:
  slide_type: '-'
---
item = alist[1]
println(item)
item = alist[2]
println(item)
item = alist[3]
println(item)
```

+++ {"slideshow": {"slide_type": "skip"}}

The code above is said to be the *unrolling* of the loop.  Of course, if `alist` had many items in it, we would not want to type all the lines of code required to perform the operation without the loop.

+++ {"slideshow": {"slide_type": "skip"}}

Just like Julia functions and if statements, loops use and `end` keyword to indicate the end of *body* of the loop.  What is in the body gets executed every cycle through the loop, any valid Julia code can be placed in the body.

+++ {"slideshow": {"slide_type": "fragment"}, "tags": ["popout"]}

The [`StepRange`](https://docs.julialang.org/en/v1.7/base/collections/#Base.StepRange) type provides a quick way to generate a monotonically increasing set of integers for iteration.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
for i = 0:2:6
    println(i ^ 2)
end
```

+++ {"slideshow": {"slide_type": "fragment"}}

Tuples can be iterated over in a for loop just like lists.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
atuple = (1, 3, 5)

for item in atuple
    println(item)
end
```

+++ {"slideshow": {"slide_type": "fragment"}}

Dictionaries can also be iterated over, by default iterating over a `Dict` returns a tuple of (key, value) pairs to be unpacked and assigned each to variables local to the loop.  

+++ {"slideshow": {"slide_type": "skip"}, "tags": ["popout"]}

If only dictionary keywords where desired for iteration in the loop, the `keys()` function could be used.  Likewise `values()` can be used if only the values are desired.  The functions turn the respective keys and/or values into an `Array`.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
adict = Dict("name1" => "Romeo", "name2" => "Juliet")

for (keyword, value) in adict
    println("$(keyword) is $(value)")
end
```

+++ {"slideshow": {"slide_type": "skip"}}

Often times we would like to store the results of computations in a `Array` as the loop executes.  One way to do this is using the `push!()` function defined for `Array`s.  Here we start with an empty `Array` of type `Any` and append the square of the integer range of numbers from 0 to 10 each cycle through the loop.

```{code-cell}
---
slideshow:
  slide_type: fragment
---
alist = []

for item = 0:10
    push!(alist, item ^ 2)
    println(alist)
end
    
print(alist)
```

+++ {"slideshow": {"slide_type": "subslide"}}

## List comprehensions

+++ {"slideshow": {"slide_type": "skip"}}

There is another type of for loop structure that can be used in Julia when it is desired to store the output of computations from a loop in a `Array`.  These are called *comprehensions*.  An example of a list comprehension that produces the same result as the for loop above is

+++ {"slideshow": {"slide_type": "skip"}, "tags": ["popout"]}

In a list comprehension, the body of the loop goes before the `for` keyword.  

```{code-cell}
---
slideshow:
  slide_type: '-'
---
alist = Any[item ^ 2 for item = 0:10]
print(alist)
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Enumerate

+++ {"slideshow": {"slide_type": "skip"}}

Occasionally, in addition to iterating over the actual items in a `Array`/`Tuple`, the integer index corresponding to the location in the data structure is also desired.  This can be returned with the `enumerate()` function.  For example,

```{code-cell}
---
slideshow:
  slide_type: '-'
---
atuple = ("a", "tuple", "of", "strings")

for (count, item) in enumerate(atuple)
    println("$(item) -- has the index $(count)")
end
```

+++ {"slideshow": {"slide_type": "slide"}}

## While loops

+++ {"slideshow": {"slide_type": "skip"}}

While loops continuously execute the body of the loop so long as some conditional statement is evaluating to `true`.  The conditional statement appears to the right of the `while` keyword.  In the example below, we use a very simple conditional statement, but any valid conditional expression in Julia, including `&&` and `||` operators can be used.

+++ {"slideshow": {"slide_type": "skip"}, "tags": ["popout"]}

Here the conditional statement is `i < 5` which of course is `true` with the given initial value of `i=0`.  So the body of the loop executes, each time incrementing `i` by 1, until `i = 5` which causes the conditional statement to evaluate to `false` and the loop exits.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
i = 0

while (i < 5)
    
    println(i)
    
    i += 1
end
```

+++ {"slideshow": {"slide_type": "skip"}}

If the conditional statement in a while loop never evaluates to `false`, the loop will execute ad infinitum.  If new variables are being assigned in the body of the loop or the results of computations are stored, the infinite cycling can cause the computer's memory to fill and possibly lead to a crash of the program and/or computer.  It's often desirable to have a set number of maximum iterations that if reached will guarantee the loop will exit.  In this case, the functionality of a while loop can be replicated with the combination of a for loop and an if statement with a `break` keyword.  For example, the while loop structure above is replicated in the following code, but with the guard that the maximum number of iterations cannot exceed 10.

```{code-cell}
---
slideshow:
  slide_type: fragment
---
for i = 0:10
    if i > 4
        break
    end
    println(i)
end
```

```{code-cell}
---
hide_input: true
init_cell: true
slideshow:
  slide_type: skip
tags: [remove_cell]
---
macro javascript_str(s) display("text/javascript", s); end
javascript"""
function hideElements(elements, start) {
for(var i = 0, length = elements.length; i < length;i++) {
    if(i >= start) {
        elements[i].style.display = "none";
    }
}
}
var prompt_elements = document.getElementsByClassName("prompt");
hideElements(prompt_elements, 0)
"""
```
