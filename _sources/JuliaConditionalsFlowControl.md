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

# Conditionals and Flow Control

*Conditional statements* are tests to return whether a given comparison between two variables is `true` or `false`.  They are used in combination with `if`-statements to control the flow of a program.  For example, we may want to check if the inputs to a particular function or program is within some valid range, or non-negative. In the event that it is a valid input, we proceed with the calculations. In the event that it is an invalid input, we terminate the program and prompt the user to input a parameter that is valid.

+++ {"slideshow": {"slide_type": "slide"}}

## Conditional operators

The conditional operators in Julia consist of the following:

  * `==` for testing if two data types are **equal** to each other
  * `!=` for testing if two data types are **not equal** to each other
  * `>` for testing if one data type is **greater than** another
  * `<` for testing if one data type is **less than** another
  * `>=` for testing if one data type is **greater than or equal to** another
  * `<=` for testing if one data type is **less than or equal to** another
  
A few code examples with obvious outputs are shown below

```{code-cell}
---
slideshow:
  slide_type: fragment
---
println(5 == 5)
println(5 != 6)
println(10 > 9)
println(9 < 10)
println(10 >= 10)
println(9 <= 10)
```

+++ {"slideshow": {"slide_type": "fragment"}}

You can *negate* any operation above by placing a `!` in front of it, e.g.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
!(5 == 5)
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Testing subsets

+++ {"slideshow": {"slide_type": "-"}}

You can also test if one element is a subset of another, e.g.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
1 in [1, 2, 3]
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
1 in (1, 2, 3)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
dict = Dict("key1" => 1, "key2" => 2, "key3" => 3)

haskey(dict, "key1")
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
1 in values(dict)
```

+++ {"slideshow": {"slide_type": "fragment"}}

For strings we need to use the function `occursin()`

```{code-cell}
---
slideshow:
  slide_type: '-'
---
occursin("Hello", "Hello, World!")
```

+++ {"slideshow": {"slide_type": "subslide"}}

### `and` / `or` operations

We can combine multiple conditional statements using the `&&` and `||` operators.  For example,

```{code-cell}
---
slideshow:
  slide_type: '-'
---
(9 <= 9) && (9 < 10)
```

+++ {"slideshow": {"slide_type": "fragment"}}

These follow the following boolean logic

 * true `and` true = true
 * true `and` false = false
 * false `and` false = false
 * true `or` true = true
 * true `or` false = true
 * false `or` false = false
 
As many `&&` and `||` statements can be combined as desired.  It's a good idea to use parenthesis when combining multiple `and`/`or` operations to ensure clarity in the order of operations.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
(true && false) || true
```

+++ {"slideshow": {"slide_type": "slide"}}

## `if`, `elseif`, `else` statements

+++ {"slideshow": {"slide_type": "skip"}}

We control the flow of a program with `if`-statements.  `if`-statements use conditionals as a test and then execute code in the *body* of the `if`-statement when the test is `true`.  Below we define a function that takes a number `x` as an argument and prints `"x is positive"` if (and only if) the input value for `x` is greater than 0. 

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function test_if_x_is_positive(x)
    if x > 0
        println("x is positive")
    end
end
```

+++ {"slideshow": {"slide_type": "skip"}}

Running the function with a few inputs

```{code-cell}
---
slideshow:
  slide_type: fragment
---
test_if_x_is_positive(5)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
test_if_x_is_positive(-5)
```

+++ {"slideshow": {"slide_type": "skip"}}

Notice the second function call does not return anything at all, this is because the conditional statement evaluated to `False` and therefore the body of the `if`-statement was never executed.  We can add a `else` clause to the `elif`-statement (shorthand for *else if* that will execute given another conditional statement, perhaps checking to see if `x` is negative.

```{code-cell}
---
slideshow:
  slide_type: fragment
---
function test_if_x_is_positive_or_negative(x)
    if x > 0
        println("x is positive")
    elseif x < 0
        println("x is negative")
    end
end
```

+++ {"slideshow": {"slide_type": "skip"}}

Running the function with a few inputs

```{code-cell}
---
slideshow:
  slide_type: fragment
---
test_if_x_is_positive_or_negative(5)
test_if_x_is_positive_or_negative(-5)
```

+++ {"slideshow": {"slide_type": "skip"}}

achieves the expected result.  What about the case when `x` is exactly 0?

```{code-cell}
---
slideshow:
  slide_type: fragment
---
test_if_x_is_positive_or_negative(0)
```

+++ {"slideshow": {"slide_type": "skip"}}

This case does not return any print statements because neither condition is met.  We could write a thrid condition to test if `x` is identically zero, or we could use an `else` clause which can be thought of as the "otherwise" condition.  The `else`-statement will execute in the event that none of the `if` or `elseif` conditions have been met. An `else`-statement is not followed by a condition.

```{code-cell}
---
slideshow:
  slide_type: fragment
---
function test_if_x_is_positive_or_negative(x)
    if x > 0
        println("x is positive")
    elseif x < 0
        println("x is negative")
    else
        println("x is 0!")
    end
end
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
test_if_x_is_positive_or_negative(0)
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Nesting

+++ {"slideshow": {"slide_type": "skip"}}

You can also nest `if`-statements inside each other. There is no limit to how many times this can be done.  An example is below.  This function takes two arguments `x` and `truncate_value`.  If `x` is less than`truncate_value` it returns `truncate_value`, otherwise is returns `x`.  It also allows `truncate_value` to be defined as `nothing` in which case it returns `x`.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function truncate(x, truncate_value)
    if truncate_value != nothing
        
        if x < truncate_value
            return truncate_value
        else
            return x
        end
    else
        return x
    end
end
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
@show truncate(5, 4)
@show truncate(-4, 0)
@show truncate(5, nothing);
```

+++ {"slideshow": {"slide_type": "skip"}}

While you can nest `if`-statements to any level, the readability of the code begins to suffer the deeper you go.  At some point, it's generally better for readability and debugging mistakes in the code to break out some of the logic into smaller functions.  The example above is really too simple for this to be necessary, but an example is shown below to demonstrate the idea.

+++ {"slideshow": {"slide_type": "skip"}, "tags": ["popout"]}

The underscore `_` in the function name `_truncate` is simply a convention to indicate that this function is never intended to be called directly by a user, but rather just a function that will be called in the body of other functions.

```{code-cell}
---
slideshow:
  slide_type: skip
---
function _truncate(x, truncate_value)
    
    if x < truncate_value
        return truncate_value
    else
        return x 
    end
end
    
function truncate(x, truncate_value)
    
    if truncate_value != nothing
        
        return _truncate(x, truncate_value)
    
    else
        return x
    end
end
```

```{code-cell}
---
slideshow:
  slide_type: skip
---
truncate(-4, 0)
```

+++ {"slideshow": {"slide_type": "skip"}}

The first function `_truncate` performs the truncation operation, but would error if `truncate_value = nothing`.  The second function checks to ensure that `truncate_value != nothing` and if this is `true`, then it calls the first function.

+++ {"slideshow": {"slide_type": "subslide"}}

### Ternary operator

The ternary operator is a shorthand single line `if`/`else` statement that has the form

`test ? do if test is true : do if test is false`

```{code-cell}
---
slideshow:
  slide_type: fragment
---
ternary_truncate(x, truncate_value) = x < truncate_value ? truncate_value : x 
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
@show ternary_truncate(-4, 0)
@show ternary_truncate(5, 0);
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
