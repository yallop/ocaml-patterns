**Update: Jake Donham has updated this package to work with OCaml 3.12: [http://github.com/jaked/patterns](http://github.com/jaked/patterns)**

A framework for writing extensions to OCaml pattern matching.  Examples included with the distribution:

> ## [Pattern-matching for lazy values](http://code.google.com/p/ocaml-patterns/wiki/LazyPatterns) ##

> ## Conjunctive patterns ##

> Conjunctive patterns (as found in F#) generalise "as"-patterns.
> In standard OCaml the syntax `patt as var' may be used to bind a
> value simultaneously to both a pattern and a variable; with
> conjunctive patterns the syntax `patt & patt' may be used to bind
> a value simultaneously to two patterns.  For example,

```
          let ((`A a, b) & (c, `B d)) = (`A 3, `B 4) in (a,b,c,d)
```

> evaluates to

```
         (3, `B 4, `A 3, 4)
```

> ## Object patterns ##

> Object patterns bind the results of calling an object's methods
> to other patterns during a pattern match.  This makes it more
> convenient to use objects as structurally-typed records.  The
> notation mirrors that in Jacques Garrigue's pa\_oo extension.  For
> example,

```
          let {| x = x; y = _ |} =
             object method x = 3 method y = 4 method z = 5 end
          in
             x + 1
```

> evaluates to

```
          4
```

> ## Negative patterns ##

> Matching with negative patterns succeeds if the value does not
> match the pattern given.  For example,

```
         let nonzero = function
            | ~0 -> true
            | _  -> false
         in (nonzero 4, nonzero 0)
```

> evaluates to

```
         (true, false)
```

> ## N+K patterns ##

> The infamous n+k patterns (as found in Haskell) offer a
> "Peano-number" view of integers.  Matching an integer value v
> against `patt+k' (where k is an integer literal) succeeds,
> binding patt to v-k, if v>=k.  For example

```
         let pred = function
            | x+1 -> x
            | 0   -> 0
         in (pred 10, pred 0)
```

> evaluates to

```
         (9, 0) 
```

Release 0.3 included

  * [pattern guards](http://code.google.com/p/ocaml-patterns/wiki/PatternGuards)
> > which are gone for now, but should reappear in a future release.

Links:

  * [Download source](http://code.google.com/p/ocaml-patterns/downloads/list)
  * [Documentation](http://code.google.com/p/ocaml-patterns/wiki)
  * [Announcement (v 0.1)](http://groups.google.com/group/fa.caml/msg/b0ec5324180bfeba)
  * [Announcement (v 0.2)](http://groups.google.com/group/fa.caml/msg/016e76d7a51559c8)
  * [Announcement (v 0.3)](http://groups.google.com/group/fa.caml/msg/f02a4ba3b0b9e80c)
  * [Announcement (v 0.4)](http://groups.google.com/group/fa.caml/msg/ffeb1daa8814050c)