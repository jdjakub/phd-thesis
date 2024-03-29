\hypertarget{bl-substrate-ref}{%
\chapter{BootstrapLab Substrate Reference}\label{bl-substrate-ref}}

This chapter serves as a more complete description of the BootstrapLab substrate than the one given in Section\ \ref{design-a-substrate}. Following Section\ \ref{two-fundamentals-state-and-change}, we divide it into a discussion of *State* and *Change*.

# State in BootstrapLab
State is a graph of *maps* (key-value dictionaries) pointing to each other, along with \ac{JS} *primitives:* numbers, booleans, strings, `null`, `undefined`, and ordinary \ac{JS} objects.

Maps contain *entries* or *fields* consisting of a *key* and a *value*. We also refer to maps and primitives together as *values*. Starting from a given map `m`, we write a *path* `m.foo.bar.baz` to denote the value arrived at by following the keys `foo`, `bar`, and `baz`. For this to be defined, `m.foo` and `m.foo.bar` must be maps. A *list* is just a map with numerical keys, but as in \ac{JS}, it may have additional non-numerical keys if convenient.^[In other words, the "list" concept is contained within the "map" concept, but where the system expects a list (\eg{} when fetching instructions) it will ignore any extra user-defined keys.]

The substrate inherits the reference semantics of \ac{JS}; if one wants to insulate a map from side effects originating from other references, one must perform a copy to some level of depth and operate on the copy. Other \ac{JS} semantics, such as prototype chains, were considered but postponed for the sake of practicality; see Section\ \ref{the-cutting-room-floor} for an outline of what was left out of the substrate.

There is a *root* for the state, from which everything is discoverable; all absolute paths begin here, and every value that "exists" necessarily has some path from root, although it may have more than one owing to the graph structure. The root is not addressable from user code, but in \ac{JS} it is a global called `ctx`. An "address" consists of a map and a key; analogously to paths, a piece of state may have more than one "address" if it is referenced from multiple maps.

## Registers
Because values at the root level are accessible from only a single key, they are the "first port of call" for instructions and are known as *registers*. Some specific *substrate* registers are reserved for use by instructions. The main substrate registers are:

\pagebreak

- `focus`: an "accumulator" register used by most instructions.
- `map`: holds the map to be updated or navigated by map instructions.
- `source`: holds the new value when updating a map entry.
- `next_instruction`: program counter; holds the address of the next instruction and the instruction itself.
- `scene`: container for the scene tree affecting the graphics window (discussed shortly).
- `addend`, `factor`, `basis`: operands for mathematical instructions.

All other keys at the root level are general-purpose *user registers* available for programs to use.

## Graphics State: The `scene` Tree

\begin{figure}[!h]
\centering\includegraphics[width=8cm]{../../fig/rect.png}
\caption[Rectangle properties.]{A rectangle inferred from the presence of \texttt{color}, \texttt{width}, \texttt{height}, and \texttt{center}. As can be seen, \texttt{color} expects a hex string.}
\label{fig:rect-spec}
\end{figure}

While state in general can be graph-structured, it is tree-structured^[See Section\ \ref{graphs-vs.-trees} for how this was accomplished.] under the `scene` register. The `scene` itself is a list of *graphics maps*. The substrate recognises a map as a graphics map if it contains at least one *graphics property:* any of the keys `width`, `height`, `color`^[We comply with the fact that, for better or worse, American spelling conventions are the *de facto* standard for internal identifiers and code more generally.], `opacity`, `position`, `center`, `top_left`, `zoom`, `text`, or `children`. The presence of these keys causes the substrate to connect their values to shapes in the graphics window and maintain synchronisation. Any graphics map can have a list of `children` in the scene hierarchy.

At present, only crude graphics are possible via two shapes. A rectangle has a `center`, `color`, `width`, and `height` (Figure\ \ref{fig:rect-spec}), while a text label has a `top_left`, `text`, and `opacity` (Figure\ \ref{fig:text-spec}).

\begin{figure}
\centering\includegraphics[width=8cm]{../../fig/text.png}
\caption[Text label properties.]{A text label inferred from the presence of \texttt{text} and \texttt{top\_left}. The \texttt{opacity} property, not shown, takes a number between 0 and 1.}
\label{fig:text-spec}
\end{figure}

More interesting is the protocol of vector properties like `center` and `top_left`. Firstly, the naming of these properties themselves follows a design principle which could be called "Naïve Honesty".^[By analogy to Boxer's "Naïve Realism"\ \parencite{Boxer}.]

\begin{heuristic}[Naïve Honesty]
If a more abstract term has only one intended meaning in a particular API, use the honest concrete concept instead.
\label{naive-honesty}
\end{heuristic}

For example, the term "position", widely used in graphics APIs, is not very self-documenting when it comes to shapes. In practice, it always means "the centre of the shape" or "the top left-hand corner" or some such meaningful function of the shape itself. Therefore, why not just be explicit about this, and save the user the effort of figuring out what it actually means? The co-ordinates are specified according to the same principle: instead of the ever-ambiguous `x`, `y`, and `z`, we have `right`, `up`, and `forward` (that is, into the screen from your position). In an ideal substrate, one could specify co-ordinates via `left`, `down`, `backward`, or any combination, and the system would automatically flip the signs as necessary for its platform graphics API calls. However, this functionality does not yet exist in BootstrapLab.

\begin{figure}
\centering\includegraphics[width=4cm]{../../fig/camera.png}
\caption[Camera properties.]{The camera `zoom` and `position` are bidirectionally synchronised between the state and the graphics window.}
\label{fig:camera-spec}
\end{figure}

Third, a vector property may have a `basis` key naming a registered co-ordinate frame; built-in examples are `world` (the root THREE.js basis) and `screen` (pixel co-ordinates from the top left). This key can be seen in the special *camera* graphics map (Figure\ \ref{fig:camera-spec}), linked to the zoomable/pannable view in the graphics window. If left unspecified, the basis is assumed to be that of the parent node in the tree. However, the user can set an explicit `basis` to express co-ordinates as most convenient to them, while the substrate will convert between frames under the hood.

This is another application of Naïve Honesty in regard to our frustrations about graphics programming: the co-ordinate basis of a vector is often left as *implicit* information for the programmer to carry around in his head, who must also have a notebook handy to write the relevant matrix equations to transform things properly. We believe this tedium is exactly what should be handled automatically by the substrate (on this theme, see \textcite{Gator}). As it stands, basis frames are registered by the key name of the graphics map into a flat list, and are thus vulnerable to name collisions and synchronisation issues. However, what we have is a promising start.

Naïve Honesty can be seen as a response to a design requirement called "No Guessing":

\begin{requirement}[No Guessing]
The user of an API should never have to uncover the meaning of a concept through trial-and-error experimentation. In graphics programming, the user should never have to create a throwaway object and transform it to resolve ambiguity about sign conventions, axis conventions, unit conventions, basis conventions, etc.
\label{no-guessing}
\end{requirement}

A reasonable reply to No Guessing might be to just write better documentation. However, this is still not a solution to implicit basis frames in user code, and Naïve Honesty attacks the root of the problem in the poor conventions themselves.

## Manually Updating State
In order to update a piece of state *and ensure* that all relevant UI also updates to reflect this, the `upd()` function is used in \ac{JS} code and the console. For example, to change the colour of the shape in Figure\ \ref{fig:rect-spec} to red, one would issue the following command in the console:

\begin{lstlisting}[language=JavaScript]
upd(ctx, 'scene', 'shapes', 'children',
         'yellow_shape', 'color', '0xff0000')
\end{lstlisting}

In order to obtain a \ac{JS} reference to a value, one gives a path to `map_get()`:

\begin{lstlisting}[language=JavaScript]
v = map_get(m, 'children', '1', 'color')
upd(ctx, 'my_value', v)
\end{lstlisting}

## Persisting State
Calling the function `export_state(map, filename)` in the console will walk the state graph from the given `map` and download it as a JSON file with the `filename`; if unspecified, it will default to `bl-state.json`. Calling `import_state(filename)` in the console or the code will yield a \ac{JS} `Promise` that will resolve to the same map, which can then be slotted in via `upd()`. For example, the following is how we load the Masp code in Section\ \ref{real-example-colour-preview} during initialisation:

\begin{lstlisting}[language=JavaScript]
import_state('misc/render.json').then(x => {
  upd(ctx, 'render_map_entry', x);
});
\end{lstlisting}

# Change in BootstrapLab

In keeping with Alignment (Force\ \ref{alignment}), the smallest units of change ought to just "fall out" of the structure of the State:

- Change a map entry to a new value (our `store` instruction)
- Create a new map (our `load` instruction)
- Actions necessary to support the above (`index` and `deref`)
- Inheritance of platform changes, \ie{} mutate \ac{JS} state and call any API (the `js` escape hatch)

The result is our instruction set, which we call \ac{BL-ASM}. We will elaborate on these shortly, but first we will specify how instructions are represented as state, and how we will notate them in shorthand and diagrams. We will mostly stick to reference material here; for more in-depth design rationale, see Section\ \ref{the-minimal-random-access-instruction-set-and-its-perils}.

## Instruction Encoding in State, Text, and Diagrams
An instruction is a map with an `op` field serving as the opcode, along with any parameters as further entries. Some examples:

```
{ op: load, value: my_reg },
{ op: deref },
{ op: store, register: my_dest }
```

Because this notation is so verbose, and no instruction has more than one parameter (see Section\ \ref{the-minimal-random-access-instruction-set-and-its-perils}), we use an inline textual notation with unnamed parameters. We would write the above like so:

\begin{lstlisting}[language=blasm]
load my_reg ; deref ; store my_dest
\end{lstlisting}

In circumstances where there are many instructions and we need to be even more concise, we only use the first letters of the names. The above example would reduce to:

\begin{lstlisting}[language=blasmmini]
l my_reg ; d ; s my_dest
\end{lstlisting}

In the next section, we will specify the semantics of the instructions. In line with the spirit of *Notational Freedom* (Section\ \ref{notational-freedom}), we will use box-and-arrow diagrams, since we judge this more suitable for our substrate than traditional formal semantics notations. However, we will take some cues from the latter; for example, we show the state before and after the instruction. We also use symbols to stand for abstract values. Specifically, $K$ denotes any string (or "key"), $M$ denotes a map, and $V$ denotes an arbitrary value. We additionally employ a grey spot to highlight the part of the state that was mutated. Figure\ \ref{fig:semantics} shows the general form.

\begin{figure}
\centering\includegraphics[width=10cm]{../../fig/semantics/semantics-diagram.png}
\caption[Instruction semantics notation.]{On the left, we have registers with their values, one of which references a map $M_1$ with some entries. On the right, we have the state after execution of the instruction; the register \texttt{reg3} has been changed (highlighted by the grey spot) to reference a new map $M_2$. Because instructions were designed to be "minimal" changes, the grey spot represents the only change and all other state remains untouched.}
\label{fig:semantics}
\end{figure}

## Change Map Entry and Supporting Instructions
We will now proceed to explain the semantics of `store`, `index`, `deref`, and `load`.

### Parameterless Store: Change Map Entry
The `store` instruction, with no parameters, expects a map $M$ in the `map` register, a key string $K$ in the `focus` register, and a value $V$ in the `source` register. After execution, the entry $K$ of $M$ will have the value $V$.

\begin{figure}[!h]
    \centering\includegraphics[width=10cm]{../../fig/semantics/store.png}
\end{figure}

\pagebreak

### Index: Follow Key in Map
The `index` instruction, like the `store` to which it is dual^[We mean this in an informal sense, but it points to some interesting analysis which we have not undertaken. Compare also `deref` and the register version of `store`, leaving `load` curiously on its own.], takes a map $M$ in `map` and a key string $K$ in `focus`. After execution, `map` contains the value $M.K$, unless this is `undefined`. In that case, it will try the special key `_` as a failsafe and `map` will contain $M.$`_` instead, which could still be `undefined`.

\begin{figure}[!h]
\centering\includegraphics[width=10cm]{../../fig/semantics/index.png}
\end{figure}

### Store To Register: Change Root Entry
There are alternate semantics^[This "overloading" of an instruction is straightforward in a map substrate, as compared to a flat binary one (Section\ \ref{let-us-avoid-the-low-level-binary-world}), although an argument could be made for it to be a separate instruction called `store-reg`.] when `store` is executed with a parameter `register` of string value $K$. In this case, given a value $V$ in `focus`, the root-level entry $K$ will have value $V$.

\begin{figure}[!h]
\centering\includegraphics[width=10cm]{../../fig/semantics/store-reg.png}
\end{figure}

### Deref: Follow Key in Root
With a string $K$ in `focus` and register $K$ holding the value $V$, an execution of `deref` will place $V$ in `focus`.

\begin{figure}[!h]
\centering\includegraphics[width=10cm]{../../fig/semantics/deref.png}
\end{figure}

### Load: Instantiate a Literal
Finally, `load` takes a parameter `value` with a value $V$. After execution, the `focus` register contains $V$.

\begin{figure}[!h]
\centering\includegraphics[width=10cm]{../../fig/semantics/load.png}
\end{figure}

Actually, there is a subtlety if $V$ is a map: the `focus` register then contains a *copy* of $V$. This is to preserve the intended use of the `value` parameter as a "literal"^[We use "literal" by analogy to "string literal", "number literal", etc. In other words, an entity presented in its entirety in the source code, rather than loaded from an external source at run time or built up from separate pieces.], especially when the map is empty (see Section\ \ref{create-new-map} shortly).

\begin{figure}[!h]
\centering\includegraphics[width=10cm]{../../fig/semantics/load-copy.png}
\end{figure}

At the time of writing, the copy performed is a deep copy, but this is almost certainly wrong, as would be always using a shallow copy. The purpose of having a `load` instruction with a map literal in the code is to *instantiate* a new map with that structure, so the literal in the code must remain unaffected. The basic intuition (Figure\ \ref{fig:copying}) is that we want things that *present as* part of the literal in the code to be fully (deep) copied; in other words, an inner map that is fully "nested" (and not referred to elsewhere) should be duplicated. Meanwhile, any references to data structures that already exist elsewhere (which thus should not present as "fully nested" in the map literal) should be preserved. However, the substrate does not yet distinguish between these two cases.^[It could be argued that the generator of the code (whether user or compiler) knows best, hence `load` should just take an optional flag for whichever case is not the default.]

\begin{figure}
\centering\includegraphics[width=\linewidth]{../../fig/semantics/copying.png}
\caption[Sharing vs. copying of \texttt{load} literals.]{On the left, we have a \texttt{load} instruction for a "literal" map, meant to be used as-is. Its effect is to fill the \texttt{focus} register with \emph{some sort of} copy of the \texttt{value}. We would present ``child'' data (\ie{} with no other parents/referrers) as graphically nested within its parent, and these maps should get duplicated. However, data that is clearly meant to be shared, as evidenced by other existing references, ought to remain that way.}
\label{fig:copying}
\end{figure}

## Create New Map
Because of the fact that `load` makes a copy of its `value`, creating a new map is simple: `load` $M$, where $M$ is an empty map. After execution, `focus` contains a new empty map that can be written to, *without* affecting the empty map in the instruction itself, which will stay empty (unless, of course, the instruction is deliberately modified "as data" by separate code). If `load` did not perform a copy, it would be necessary to use a *different* `load` instruction each time one wanted to create a new map.^[This situation is the same Python's notorious "mutable default arguments" for functions\ \parencite{PythonNotorious}. Default arguments are evaluated the one time a function gets *defined*, instead of per call, so subsequent calls to the function will see any successive changes to the same default argument object.] However, arguments could be made to the effect that `load` should just have special semantics for the empty map, or that there should be a `load-new` instruction, and so on.

\hypertarget{inheritance-of-js-level-change}{%
\subsection{\texorpdfstring{Inheritance of \acs{JS}-level
Change}{Inheritance of JS-level Change}}\label{inheritance-of-js-level-change}}
The `js` instruction takes a `func` parameter and calls it as a \ac{JS} function. This functions as an all-purpose "escape hatch" into the \ac{JS} platform, analogous to `asm` blocks in C code.

\begin{minipage}{\linewidth}
\begin{lstlisting}[language=JavaScript]
{ op: js, func: () => {
    alert("Hello, World!");
}}
\end{lstlisting}
\end{minipage}

Some features of the platform were common enough to be worth implementing as their own instructions, as an optimisation:

- `add` adds the `addend` register to `focus`.
- `mul` multiplies `focus` by the `factor` register.
- `sign` replaces `focus` with its mathematical sign ($+1$, $0$, or $-1$).
- `typeof` replaces `focus` with its result under the \ac{JS} `typeof` operator.
- `basis` rewrites the vector in `focus` to be the same vector, but in the basis named by the `basis` register.

However, these remain experimental rather than practical and we did not get around to implementing an operand stack.

We could argue in favour of such instructions in terms of Alignment (Force\ \ref{alignment}). Supposing our basic state-prodding instructions were Turing-complete, we could certainly implement arithmetic, etc. using Church numerals encoded as maps, but this would be a waste of the vastly more efficient capabilities provided by the platform. Similarly, in a real-world instruction set (\eg{} x86), any special-purpose silicon in the *hardware* platform ought to be accessible from some instruction; we view arithmetic and logic instructions as *access points* to the heavily optimised ALU, as opposed to "intrinsic" requirements of the instruction set.

Tentatively, we conjecture that instructions are either *intrinsic*, \ie{} necessary for Turing-completeness, or *gateways* to platform optimisations. Any instructions for accessing \eg{} video memory could be considered intrinsic if we subsume video memory as just a special part of the overall state. Furthermore, any special instructions that affect the outside world (\eg{} refresh the screen, or send network packets) could be re-interpreted as writing to special parts of state that have side effects; see also Self's behaviour-as-state\ \parencite{Self} or Plan 9's "control files"\ \parencite{Plan9}.

## The Fetch-Execute Cycle
The `next_instruction` register holds a map with keys `ref` and `value`. The `value` entry holds the next instruction itself, while `ref` contains entries `map` and `key` as the address of the instruction, where `key` is an integer. During the fetch-execute cycle, `key` is incremented. In this way, a list of instructions can serve as a "basic block". Furthermore, if incrementing the `key` "runs off the end" of a list, the substrate will follow any `continue_to` entry in the list and continue execution at key `1` of the associated map.

The fetch-execute cycle is activated via the \ac{JS} console function called `run_and_render(n)`, taking a number `n` of instructions to execute (warning: this takes place on the main \ac{JS} thread, so if there are many instructions, the UI will stall). If `n` is omitted, it defaults to 1, effectively acting as a single-step command. Finally, any instruction can have a `break` parameter; if set to a value \ac{JS} considers "truthy", the fetch-execute cycle will halt after executing the instruction.

## Input Handling
Currently, input handling is entirely a substrate capability, as opposed to an in-system one. While we have successfully experimented with running in-system code as "device drivers" in response to input (Section\ \ref{graphics-and-interaction}), this is not how things work at present. As a leftover from these experiments, there is a register called `pointer` containing a boolean `is_dragging` and vector-valued `pressed_at` and `released_at`. The \ac{JS} `mousedown`/`mouseup` event handlers ensure `is_dragging` reflects whether the left mouse button is currently down over the graphics window, and log co-ordinates in `pressed_at` and `released_at` as appropriate. However, there is not currently any functionality in-system to "listen" for changes on these entries. Furthermore, because `run_and_render()` blocks the \ac{JS} thread until completion, not even a polling-based approach would work (since the events would queue up until after the thread unblocks).

These handlers, along with `mousemove` and `onwheel`, update the camera `position` and `zoom` properties to allow the user to zoom and pan around the graphics scene. The click handlers and `keydown` handler are full of code for selecting and editing entries in the Tree Editor (Section\ \ref{supplanting-the-temporary-state-viewer}). When eventually ported to in-system code (whether Masp or \ac{BL-ASM}), these event handlers will more resemble the "device driver" pattern that we mentioned.