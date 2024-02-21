\hypertarget{masp-ref}{%
\chapter{Masp Reference}\label{masp-ref}}

The objective of *Masp* is to get all the benefits of a Lisp-like expression language in a way that is Aligned (Definition\ \ref{alignment}) with our map-based substrate (see Section\ \ref{bl-substrate-ref}). We want to preserve the dynamic, functional, and metaprogramming facilities of Lisp, while leaving behind the reliance on positional parameters and making full use of map affordances.

Lisp is built around the "linked list" data structure as implemented by "cons" cells. Expressions to be evaluated take the form of lists. However, lists can also represent "data" lists that are not meant to be evaluated. Similarly, Masp includes both "data" maps and maps representing an expression to evaluate. Accordingly, we will divide our description of Masp into *State* and *Change*.

# State In Masp
State is inherited from the substrate (see Section\ \ref{state-in-bootstraplab}); in particular, lists continue to exist as maps with numerical keys. However, there is one important Masp structure that needs encoding *within* this substrate: the variable binding environments (or *envs* for short). An env is basically a tree of dictionaries, where children delegate failed lookups to their parents. In Lisp this is traditionally encoded as a tree of linked list cells, where lookups are accomplished by linear search. In Masp, the map substrate already provides the dictionary structure for free, with lookup as a primitive operation (no linear scans!) and all that must be done is to add extra structure for delegation.

Delegation could be as simple as adding a `parent` key to a map, but this "in-band" encoding would essentially reserve the word `parent` and preclude it from "user"-level Masp code. We could call it `__parent` or something equally strange and perhaps get away with it, but for elegance's sake we took the alternative route. An *env* is a map with two entries, called `parent` and `entries`. The latter holds the local entries (which can have whatever keys we want), while `parent` points to the parent env if it has one.

# Change in Masp
The fundamental distinction a Lisp/Masp interpreter needs to make is between expressions that *need* evaluating, and those that do not. If Lisp is asked to evaluate a list, it will assume the first element is a "function expression" and evaluate it to obtain a function.^[By Lisp, we abbreviate "the version(s) of Lisp that have the desirable features we wish to see in Masp"; any versions of Lisp that do not do what we mention are not germane to the discussion.] It will then *apply* this function to the rest of the list as arguments, evaluating them or leaving them alone as the function specifies.

In Masp, a function application is *explicitly* marked by the presence of an `apply` key. When Masp evaluates a map, it looks for this key and treats its value as a function expression. The rest of the keys in the map are its arguments. True, this *in-band* encoding means that no function may have a parameter called `apply`, but we made this decision for simplicity and readability of Masp code, given the primitive notations available to us at the time. Consider the following illustrative function application:

```
apply: drawLine, from: { x: 10, y: 20 }, to: { x: 50, y: 60 },
                color: { apply: quote, to: 0xff0000 }
```

The evaluator looks at the `apply` key, sees it is a \ac{JS} string, and looks up "drawLine" in the current env to get a closure. Suppose the closure lacks a `dont_eval_args` key; the evaluator goes through each remaining entry^[In a nondeterministic order---which is worth fixing in subsequent work. Perhaps Order Maps (Section\ \ref{the-cutting-room-floor}) have their place here.] and evaluates it. The `from` and `to` values are both data maps lacking an `apply` key, so they remain as they are. The `color` argument is a further application^[The special meaning of the `apply` key means that a "data" map in argument position to such a function cannot have such a key; it would be seen as an application and evaluated. This would not be a problem, however, for a function with `dont_eval_args` set, provided the function specifically left the given argument unevaluated.], which evaluates to the string `0xff0000` (simply putting the string itself would cause a variable lookup).

This illustrates that, as far as \ac{JS} primitives are concerned, nearly all are treated as "terminal" values that need no evaluation. The sole exception is a string, which denotes a variable to be looked up in the current env.

By convention, single-argument functions like `quote` name the argument `to` in order to provide the reading "*apply* function *to* argument". Some functions, such as `+`, `*`, `-`, etc. number their arguments instead of using their official names:^[Despite our tolerance of verbosity with the promise of future Notational Freedom, we could not stomach having `addend`, `multiplicand`, `subtrahend`, etc. in Masp arithmetic.] `apply: +, 1: 10, 2: 20`. 

As in Lisp, an individual function parameter can be "extensional" (evaluated before use) or "intensional" (used in its un-evaluated form). For example, the `if` function used as `(if cond then else)` has `cond` in extensional position; the behaviour of `if` depends entirely on whether `cond` evaluates to `true` or `false` and not on the make-up of the `cond` expression itself. Meanwhile, the `then` and `else` code are used *intensionally* because one of them should not be evaluated (otherwise, the "wrong" branch would still get to cause its side effects). For simplicity, functions in Masp are assumed all-extensional by default; this can be disabled by setting the `dont_eval_args` entry on the function closure, in which case it is up to the function itself to perform any evaluation.

A Masp *closure* is a map with entries for the function `body`, local `env`, and optionally a `dont_eval_args` flag. If the `body` is a \ac{JS} function, the closure represents a primitive function run in the platform. Otherwise, the `body` is treated as a Masp expression to evaluate in the context of the local arguments.

## Maps As Functions
We find it useful to view maps abstractly as "extensional^[Extensional in the sense of "listing out the entries"; a different meaning to the "extensional" of "evaluates all arguments"! Both of these philosophical concepts are relevant to Masp and it is unfortunate that they share the same word.] functions": mathematical functions taking inputs to outputs, whose mappings are explicitly specified instead of via some computation. Therefore it is important that maps must be usable as functions in Masp; we must be able to "apply" a map to a value. This gives us a special case of pattern matching for free (\ie{} strict equality matching). It may be worth considering how augmenting the definition of maps-as-functions could support more advanced pattern matching (\eg{} inequalities, more complex conditions) but this is beyond the scope of Masp as-is. We retain the failsafe match named `_` from the substrate's `index` instruction (Section\ \ref{index-follow-key-in-map}).

For example, the pattern match in the Factorial definition (Figures\ \ref{fig:masp1-3} and\ \ref{fig:masp4-5}) could be extended like so (using `$...$` to denote syntactic sugar):

```
to: n, apply:
  0: 1
  1: 1
  2: 2
  3: 6
  _: $ n * fac(n-1) $
```

This situation can be read imperatively as "to `n`, apply the map", or indicatively as "pushing `n` through the map".


## Masp Tree-Based Evaluation
As shown in Section\ \ref{implementing-masp-for-bootstraplab}, Masp evaluates expressions incrementally and additively in a subtree of the state. This subtree lives under the `masp` top-level register. Inside it lie `initial_env`, the global variable-binding env of last resort; `program`, the program being evaluated; and `ctx`, the current evaluation context somewhere within `program`.

The \ac{JS} interpreter function `masp_step()` performs a small evaluation step, \eg{} resolving a variable binding (this is the "incremental" aspect of evaluation). As a \ac{JS} function, it is "re-entrant" in the sense that all of the information it needs lives in the Masp tree, not the \ac{JS} stack. Thus, every step, it must re-learn where it is in terms of evaluating the current expression and leave appropriate markers in the tree to direct its next run.

A *local evaluation context,* or *context* for short, contains a local `env`, an `expr`, and eventually a `value` (this is the "additive" aspect of evaluation; one can still see the expression that the value came from). If `masp_step` sees that there is no `value`, its goal is to make one. Otherwise, its goal is to point the `ctx` back up to the context for the parent expression, or pass the value up there. At all times, the original `program` tree is unaffected; after full evaluation, every expression will have been duplicated and wrapped in a context, forming a tree of contexts collectively containing a copy of the `program` (Figure\ \ref{fig:masp-tree}).

\begin{figure}
\centering\includegraphics[width=8cm]{masp/masp.png}
\label{fig:masp-tree}
\caption[The Masp tree.]{The \texttt{masp} register contains an \texttt{initial\_env} with basic primitives, a \texttt{program}, and the current evaluation context \texttt{ctx}. When fully evaluated, the program has its tree structure expanded into a tree of local contexts, each with an \texttt{expr} and a \texttt{value} (\texttt{env}s have been omitted for brevity). Solid rectangles enclose ordinary maps; dashed rectangles draw attention to local contexts.}
\end{figure}


We will walk through the process from the perspective of `masp_step`. If the `expr` is a string, we add a `value` obtained from looking up the `expr` in the local `env`. If `expr` is a map with no `apply` entry, \ie{} a "literal" map, we set the `value` to a special closure in which `literal` is set to the `expr` and the current env is included. Otherwise, if the `apply` node has not yet been evaluated, we wrap it in a new context and enter (\ie{} we make it the new `ctx`). Once there is a value for the function closure, evaluation proceeds to the arguments unless `dont_eval_args` is set on the closure. A tracking variable in the context, `arg_i`, helps us discover the next unevaluated argument, as determined by the order of map keys from \ac{JS}' `Object.keys()`.

After processing the arguments, we inspect the `body` of the function closure. If it is a \ac{JS} function, we call this "primitive" with the context and argument values. The protocol for Masp primitives is that returning `true` (on the \ac{JS} stack) means that it has returned a value (in Masp); otherwise, it is evaluating a subexpression and needs to be run again afterwards. For example, here is the \ac{JS} code for the `decr` (decrement) primitive:

```
'decr': { body: (c, args) =>
    { upd(c, 'value', args.to-1); return true; }}
```

It sets the value in the context and returns `true`, signalling that it has also returned in Masp. A more complicated *intensional* primitive is `define` (its usage reads "define *name:* $\ldots$ *as:* $\ldots$"):

```
'define': { body: (c, args) => {
    if (typeof args.as === 'object') {
      const val = map_get(args.as, 'value');
      if (val !== undefined) {
        upd(masp, 'initial_env', 'entries', args.name, val);
        upd(c, 'value', null); return true;
      }
    }
    masp_enter('as');
  }, dont_eval_args: true }
```

Because `dont_eval_args` is set, `define` will receive the `name` argument as an unevaluated string. It needs to evaluate the `as` argument, and if it has already done so, it binds it to the `name` in the global `initial_env`, sets the Masp return value as `null` (\ie{} no return value, entirely side-effects) and returns `true` to return in Masp. Otherwise, it creates and enters a new context for `as`, and implicitly returns `undefined` in \ac{JS}, which is falsy, telling the evaluator that `define` has not finished evaluating.

Back to inspecting the `body`. If a `body` is absent but we have a `literal` map instead, we push the single argument (named `to`) through the map and treat the result as the `body` (going via the `_` key if this fails). This facilitates pattern-matching for the purpose of obtaining *further code to execute* beyond obtaining just a value straight away.

At this point, we have a `body` made of Masp code to evaluate in an appropriate context. We create a new context, duplicate the body code so we can destructively replace expressions with contexts down the line, and set that as the `value` of the `apply` context. We fill the local `env` with the processed arguments, and enter this new context (\ie{} set it as the `ctx`).

Finally, if we have been through all the preceding conditions in `masp_step` but did not "do" anything, and the current context already has a value, we must have finished evaluating this subexpression and re-enter the parent context to continue there.

# Masp Design Remarks
*Procedural Reflection in Programming Languages*\ \parencite{ProcRefl} was of great inspiration while we designed Masp, and remains of serious interest. The work builds up to the design of a fully, elegantly *reflective* version of Lisp called 3-LISP. In order to do this, it explains the unique features and subtleties of ordinary "1-LISP" while critiquing inelegant aspects of its design, and applies philosophical rigour to address these issues. The intermediate 2-LISP is possessed of strong "Category Alignment" regarding the different types of data structures available (\eg{} splitting 1-LISP's overloaded "list" into the applicative "pair" and the data "rail"), and methodically refines 1-LISP's `eval`/`apply` into a philosophically rigorous `normalise`/`reduce`. For reasons of time and resource availability, we designed Masp mostly as an analogue of 1-LISP, inheriting its flaws. However, we would welcome efforts to "port"\ \citeauthor*{ProcRefl}'s methodology regarding 2-LISP and 3-LISP to map-based substrates.

Another approach worth developing is found in \parencite{OECM}. *Open, Extensible Composition Models* inserts a level of indirection into the Lisp evaluator, such that user-defined *evaluators* and *applicators* can be supplied to support "novel composition mechanisms". These are also interesting ideas which, regrettably, we did not have the opportunity to try out. We welcome further work to port the ideas to maps and see if they are compatible with the 2-LISP or 3-LISP architectures.
