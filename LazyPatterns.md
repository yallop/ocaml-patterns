# Lazy patterns #

This page documents lazy patterns in releases 0.3 and 0.4.

## Introduction ##

Lazy patterns extend OCaml's patterns with the keyword `lazy`.  A
pattern

```
    lazy p
```

matches a value of type
[`Lazy.t`](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Lazy.html)
by forcing it (as if with `Lazy.force`) and matching the result
against `p`.  You can use `lazy` anywhere that you can use a regular
constructor such as `Some`: for instance, `lazy` can appear in `let`,
`match` or `try` patterns, deep inside another pattern, as prefix to a
deep pattern, in an "or" pattern, or in a [pattern guard](PatternGuards.md).

## Example: implementing `force` ##

The following function behaves just like the
[standard Lazy.force](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Lazy.html) function:

```
  let force (lazy v) = v
```

## Example: Length of a lazy list ##

Given a type of lazy lists in the
[odd style](http://citeseer.ist.psu.edu/182358.html)

```
  type 'a llist = Nil | Cons of 'a * 'a llist Lazy.t
```

we can write a function that computes the length

```
  let rec llength : 'a llist -> int = function
    | Nil -> 0
    | Cons (_, lazy tl) -> 1 + llength tl
```

The [distribution](http://code.google.com/p/ocaml-patterns/downloads/list) contains more examples, including functions over streams
in the [even style](http://citeseer.ist.psu.edu/182358.html), based on
the package in
[Chris Okasaki's dissertation](http://www.cs.cmu.edu/afs/cs.cmu.edu/project/fox/mosaic/papers/cokasaki-thesis.ps).

## How lazy are lazy patterns? ##

With regular constructors (such as `Some` or `::'), constructing a
value may involve computation (including side effects or failure to
terminate), but deconstructing a value never does.  With `lazy` the
situation is exactly reversed.  The expression

```
   lazy exp
```

always succeeds with no side effects, whereas matching against a
pattern

```
   lazy pat
```

triggers evaluation of an expression, which may perform side effects,
raise an exception, or fail to terminate.

These properties of lazy patterns make it desirable to define an order
of evaluation for patterns, so that we can answer the following
questions about any pattern involving `lazy':

> Which subpatterns are evaluated?

and

> In what order?

The order we choose (which is necessarily somewhat arbitrary) is
left-to-right, depth-first, failing as soon as possible.  For example,
the following evaluates to `true` without raising an exception:

```
   match lazy 2, lazy (failwith "boom") with
     | lazy 3, lazy _ -> false
     | lazy 2, _      -> true
     | _, _           -> false
```

because the right-hand lazy value is never forced, whereas the
following raises an exception because both values are forced in the
first pattern:

```
   match lazy 2, lazy (failwith "boom") with
     | lazy 2, lazy _ -> false
     | lazy 3, _      -> true
     | _, _           -> false
```

In the following example, the lazy value is never forced

```
   match `A (lazy (failwith "boom")) with
    | `B (lazy x) -> x
    | `A _        -> true
```

so the expression evaluates to `true`.