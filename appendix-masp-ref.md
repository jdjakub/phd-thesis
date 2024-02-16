\hypertarget{masp-ref}{%
\chapter{Masp Reference}\label{masp-ref}}

The objective of Masp is to get all the benefits of a Lisp-like expression language in a way that is Aligned (Definition\ \ref{alignment}) with our map-based substrate (see Section\ \ref{bl-substrate-ref}). We want to preserve the dynamic, functional, and metaprogramming facilities of Lisp, while leaving behind the reliance on positional parameters.

Lisp is built around the "linked list" data structure as implemented by "cons" cells. Expressions to be evaluated take the form of lists. However, lists can also represent "literal" lists that are not meant to be evaluated.

Similarly, Masp will include both "literal" maps and maps representing an expression to evaluate. As in the BootstrapLab substrate, lists exist as maps with numerical keys.

## Maps As Functions
We find it useful to view maps abstractly as "extensional functions": mathematical functions taking inputs to outputs, whose mappings are explicitly specified instead of via some computation. Therefore it is important that maps must be usable as functions in Masp; we must be able to "apply" a map to a value. This gives us a special case of pattern matching for free (\ie{} strict equality matching). It may be worth considering how augmenting the definition of maps-as-functions could support more advanced pattern matching (\eg{} inequalities, more complex conditions but this is beyond the scope of Masp as-is. We retain the failsafe match named `_` from the substrate's `index` instruction (Section\ \ref{change-map-entry-and-supporting-instructions}).

For example, the following expression:

```
to: 
```

