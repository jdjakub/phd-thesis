\hypertarget{bl-substrate-ref}{%
\chapter{BootstrapLab Substrate Reference}\label{bl-substrate-ref}}

# State in BootstrapLab

State is a graph of *maps* (key-value dictionaries) pointing to each other, along with \ac{JS} *primitives:* numbers, booleans, strings, `null`, `undefined`, and ordinary \ac{JS} objects.

Maps contain *entries* or *fields* consisting of a *key* and a *value*. We also refer to maps and primitives together as *values*. Starting from a given map `m`, we write a *path* `m.foo.bar.baz` to denote the value arrived at by following the keys `foo`, `bar`, and `baz`. For this to be defined, `m.foo` and `m.foo.bar` must be maps.

The substrate inherits the reference semantics of \ac{JS}; if one wants to insulate a map from side effects originating from other references, one must perform a copy to some level of depth and operate on the copy.

There is a *root* for the state, from which everything is discoverable; all absolute paths begin here, and every value that "exists" necessarily has some path from root, although it may have more than one owing to the graph structure. An "address" consists of a map and a key; analogously to paths, a piece of state may have more than one "address" if it is referenced from multiple maps.

Because values at the root level are accessible from only a single key, they are the "first port of call" for instructions and are known as *registers*. Some specific *substrate* registers are reserved for use by instructions. The main substrate registers are:

- `focus`: an "accumulator" register used by most instructions.
- `map`: holds the map to be updated or navigated by map instructions.
- `source`: holds the new value when updating a map entry.
- `next_instruction`: program counter; holds the address of the next instruction.
- `scene`: container for the scene tree affecting the graphics window (discussed shortly).

Some special substrate registers are:

- `vec`
- etc

All other keys at the root level are general-purpose *user registers* available for programs to use.

## Graphics State: The `scene` Tree

# Change in BootstrapLab

In-keeping with Alignment (Force\ \ref{alignment}), the smallest units of change ought to just "fall out" of the structure of the State:

- Change a map entry to a new value (our `store` instruction)
- Create a new map (our `load` instruction)
- Actions necessary to support the above (`index` and `deref`)
- Inheritance of platform changes, \ie{} mutate \ac{JS} state and call any API (`js`)

We will elaborate on these shortly, but first we will specify how instructions are represented as state, and how we will notate them in shorthand and diagrams. We will mostly stick to reference material here; for more in-depth design rationale, see Section\ \ref{the-minimal-random-access-instruction-set-and-its-perils}.

## Instruction Encoding In State, Text, and Diagrams
An instruction is a map with an `op` field serving as the opcode, along with any parameters as further entries. Some examples:

```
{ op: load, value: my_reg },
{ op: deref },
{ op: store, register: my_dest }
```

Because this notation is so verbose, and no instruction has more than one parameter (see Section\ \ref{the-minimal-random-access-instruction-set-and-its-perils}), we use an inline textual notation with unnamed parameters. We would write the above like so:

```
load my_reg ; deref ; store my_dest
```

In the next section, we will specify the semantics of the instructions. In line with the spirit of *Notational Freedom* (Section\ \ref{notational-freedom}), we will use box-and-arrow diagrams, since we judge this more suitable for our substrate than traditional formal semantics notations. However, we will take some cues from the latter; for example, we show the state before and after the instruction. We also use symbols to stand for abstract values. Specifically, $K$ denotes any string (or "key"), $M$ denotes a map, and $V$ denotes an arbitrary value. We additionally employ a grey spot to highlight the part of the state that was mutated.

## Change Map Entry and Supporting Instructions

The `store` instruction, with no parameters, expects a map $M$ in the `map` register, a key string $K$ in the `focus` register, and a value $V$ in the `source` register. After execution, the entry $K$ of $M$ will have the value $V$.

\begin{figure}[!h]
    \centering\includegraphics[width=10cm]{../../fig/semantics/store.png}
\end{figure}

The `index` instruction, like the `store` to which it is dual^[We mean this in an informal sense, but it points to some interesting analysis which we have not undertaken. Compare also `deref` and the register version of `store`, leaving `focus` curiously on its own.], takes a map $M$ in `map` and a key string $K$ in `focus`. After execution, `map` contains the value $M.K$.

\begin{figure}[!h]
\centering\includegraphics[width=10cm]{../../fig/semantics/index.png}
\end{figure}

There are alternate semantics^[This "overloading" of an instruction is straightforward in a map substrate, as compared to a flat binary one (Section\ \ref{let-us-avoid-the-low-level-binary-world}), although an argument could be made for it to be a separate instruction called `store-reg`.] when `store` is executed with a parameter `register` of string value $K$. In this case, given a value $V$ in `focus`, the root-level entry $K$ will have value $V$.

\begin{figure}[!h]
\centering\includegraphics[width=10cm]{../../fig/semantics/store-reg.png}
\end{figure}

With a string $K$ in `focus` and register $K$ holding the value $V$, an execution of `deref` will place $V$ in `focus`.

\begin{figure}[!h]
\centering\includegraphics[width=10cm]{../../fig/semantics/deref.png}
\end{figure}

Finally, `load` takes a parameter `value` with a value $V$. After execution, the `focus` register contains $V$.

\begin{figure}[!h]
\centering\includegraphics[width=10cm]{../../fig/semantics/load.png}
\end{figure}

Actually, there is a subtlety if $V$ is a map: the `focus` register contains a *deep copy* of $V$. This is to preserve the intended use of the `value` parameter as a "literal". Otherwise, if the map was modified and the same instruction was run again (\eg{} during a loop) further instructions would see these changes. The purpose of having a `load` instruction with a map "literal" in the code is to *instantiate* a new map with that structure, so the literal in the code must remain unaffected.

## Create New Map
Because of the fact that `load` does a deep copy of its `value`, creating a new map is simple: `load` $M$, where $M$ is an empty map. After execution, `focus` contains a new empty map that can be written to, *without* affecting the empty map in the instruction itself, which will stay empty (unless, of course, the instruction is deliberately modified "as data" by separate code). If `load` did not perform a deep copy, it would be necessary to use a *different* `load` instruction each time one wanted to create a new map.^[This situation is the same Python's notorious "mutable default arguments" for functions\ \parencite{PythonNotorious}. Default arguments are evaluated the one time a function gets *defined*, instead of per call, so subsequent calls to the function will see any successive changes to the same default argument object.]

## Inheritance of \ac{JS}-level change
The `js` instruction takes a `func` parameter and calls it as a \ac{JS} function. This functions as an all-purpose "escape hatch" into the \ac{JS} platform, analogous to `asm` blocks in C code.

```
{ op: js, func: () => {
    alert("Hello, World!");
}}
```

## Proof of Turing-Completeness
because i say so

## Remarks
Fails the self-interpret test

See also blog post