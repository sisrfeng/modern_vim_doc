Learn Vimscript the Hard Way

  * Functional Programming
      + Immutable Data Structures
      + Functions as Variables
      + Higher-Order Functions
      + Performance
      + Exercises


Functional Programming

We're going to take a short break now to talk about a style of programming you may have heard of: functional programming.

If you've programmed in languages like
Python,
Ruby or Javascript,
or especially Lisp,
Scheme,
Clojure or Haskell,

you're probably familiar with the idea of using functions as variables and
using data structures with immutable state.

If you've never done this before you can safely skip this chapter,
but I'd encourage you to try it anyway and
broaden your horizons!

Vimscript has all the pieces necessary to program in a kind-of-functional style,
but it's a bit clunky.
We can create a few helpers that will make it less painful though.
Go ahead and
create a functional.vim file somewhere so you don't have to keep typing everything over and  over.
This file will be our scratchpad for
this chapter.

Immutable Data Structures
Unfortunately Vim doesn't have any immutable collections like Clojure's vectors and  maps built-in,
but by creating some helper functions we can fake it to some degree.

Add the following function to your file:

        function! Sorted(l)
            let new_list = deepcopy(a:l)
            call sort(new_list)
            return new_list
        endfunction

Source and write the file, then run :echo Sorted([3, 2, 4, 1]) to try it out. Vim echoes [1, 2, 3, 4].

How is this different from simply calling the built-in sort() function?
The key is the first line: let new_list = deepcopy(a:l).
Vim's sort() sorts the list 💛in place💛,
so we first create a full copy of the list and sort that so the original list won't be changed.

This prevents side effects and
helps us write code that is easier to reason about and  test.
Let's add a few more helper functions in this same style:

    function! Reversed(l)
        let new_list = deepcopy(a:l)
        call reverse(new_list)
        return new_list
    endfunction

    function! Append(l, val)
        let new_list = deepcopy(a:l)
        call add(new_list, a:val)
        return new_list
    endfunction

    function! Assoc(l, i, val)
        let new_list = deepcopy(a:l)
        let new_list[a:i] = a:val
        return new_list
    endfunction

    function! Pop(l, i)
        let new_list = deepcopy(a:l)
        call remove(new_list, a:i)
        return new_list
    endfunction

Each of these functions is exactly the same except for the middle line and the arguments they take. Source and write the file and try them out on a few lists.

Reversed() takes a list and returns a new list with the elements reversed.

Append() returns a new list with the given value appended to the end of the old one.

Assoc() (short for "associate") returns a new list, with the element at the given index replaced by the new value.

Pop() returns a new list with the element at the given index removed.

# Functions as Variables

Vimscript supports using variables to store functions,
but the syntax is a bit obtuse.

Run the following commands:

    :let Myfunc = function("Append")
    :echo Myfunc([1, 2], 3)

Vim will display [1, 2, 3] as expected. Notice that the variable we used started with a capital letter.
If a Vimscript variable refers to a function it must start with a capital letter.

Functions can be stored in lists just like any other kind of variable. Run the following commands:

    :let funcs = [function("Append"), function("Pop")]
    :echo funcs[1](['a', 'b', 'c'], 1)

Vim displays ['a', 'c']. The funcs variable does not need to start with a capital letter because it's storing a list, not a function. The contents of the list don't matter at all.

# Higher-Order Functions

Let's create a few of the most commonly-used higher-order functions.
If you're not familiar with that term,
higher-order functions are functions that take other functions and do something with them.

We'll begin with the trusty map function. Add this to your file:

    function! Mapped(fn, l)
        let new_list = deepcopy(a:l)
        call map(new_list, string(a:fn) . '(v:val)')
        return new_list
    endfunction

Source and write the file, and try it out by running the following commands:

    :let mylist = [[1, 2], [3, 4]]
    :echo Mapped(function("Reversed"), mylist)

Vim displays [[2, 1], [4, 3]], which is the result of applying Reversed() to every element in the list.

How does Mapped() work? Once again we create a fresh list with deepcopy(), do something to it, and return the modified copy -- nothing new there. The tricky part is the middle.

Mapped() takes two arguments: a funcref (Vim's term for "variable holding a function") and a list. We use the built-in map() function to perform the actual work. Read :help map() now to
see how it works.

Now we'll create a few other common higher-order functions. Add the following to your file:

function! Filtered(fn, l)
    let new_list = deepcopy(a:l)
    call filter(new_list, string(a:fn) . '(v:val)')
    return new_list
endfunction

Try Filtered() out with the following commands:

:let mylist = [[1, 2], [], ['foo'], []]
:echo Filtered(function('len'), mylist)

Vim displays [[1, 2], ['foo']].

Filtered() takes a predicate function and a list. It returns a copy of the list that contains only the elements of the original where the result of calling the function on it is
"truthy". In this case we use the built-in len() function, so it filters out all the elements whose length is zero.

Finally we'll create the counterpart to Filtered():

function! Removed(fn, l)
    let new_list = deepcopy(a:l)
    call filter(new_list, '!' . string(a:fn) . '(v:val)')
    return new_list
endfunction

Try it out just like we did with Filtered():

:let mylist = [[1, 2], [], ['foo'], []]
:echo Removed(function('len'), mylist)

Vim displays [[], []]. Removed() is like Filtered() except it only keeps elements where the predicate function does not return something truthy.

The only difference in the code is the single '!' . we added to the call command, which negates the result of the predicate.

# Performance

You might be thinking that copying lists all over the place is wasteful, since Vim has to constantly create new copies and garbage collect old ones.

If so: you're right!
Vim's lists don't support the same kind of structural sharing as Clojure's vectors,
so all those copy operations can be expensive.
Sometimes this will matter.
If you're working with enormous lists,
things can slow down.

In real life,  though,
you might be surprised at how little you'll actually notice the difference.
Consider this: as I'm writing this chapter my Vim program is using about 80 megabytes of memory (and I have a lot of plugins installed). My laptop has 8 gigabytes of memory in it. Is the
overhead of having a few copies of a list around really going to make a noticeable difference? Of course that depends on the size of the list, but in most cases the answer will be "no".

To contrast, my Firefox instance with five tabs open is currently using 1.22 gigabytes of RAM.

You'll need to use your own judgement about when this style of programming creates unacceptably bad performance.

Exercises

Read :help sort().

Read :help reverse().

Read :help copy().

Read :help deepcopy().

Read :help map() if you haven't already.

Read :help function().

Modify Assoc(), Pop(), Mapped(), Filtered() and Removed() to support dictionaries. You'll probably need :help type() for this.

Implement Reduced().

Pour yourself a glass of your favorite drink. This chapter was intense!

á Previous Next â
Made by Steve Losh. License. Built with Bookmarkdown.



