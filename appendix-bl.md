\hypertarget{bl-trivia}{%
\chapter{BootstrapLab Trivia}\label{bl-trivia}}

# The Cutting Room Floor
Force \ref{escape-plaf} directed us to do without several advanced substrate features we were tempted to include. For example, it would be useful to attach state change listeners to keep parts of the state in sync with others. We could go even further and include constraint-based programming features.

On another note, our substrate is based on "maps" without a predefined ordering of the entries. However, there is always some order in which they will be displayed:

```
{ red: 100, green: 255, blue: 0 }
```

Thus it might be nice to be able to set this on a per-map basis. A convenient way to expose this in-system would be via another map, or "order map" which would be a list map of key names:

```
{ 1: 'red', 2: 'green', 3: 'blue' }
```

A practical use of this is for enabling iteration through a map's keys or entries. If we wish to be rigorous, the order map itself would have an order map, which would (by default) be the same for all order maps:

```
{ 1: 1, 2: 2, 3: 3 }
```

Of course, with such a conceptually infinite sequence of order maps, care must be taken to implement it in a finite, on-demand way. Perhaps some clever circular reference would work, as COLA does for its *vtable* relation\ \parencite{OROM}. This raises the question of how to obtain an order map in-system. If we make it an ordinary key on all maps, we must be careful to render it only on-demand and to exclude it from ordinary iteration through keys. Plus, would we want the visual clutter of always displaying it? It might be better to make it accessible through a special instruction `order-map`.

We then face a further *synchronisation* problem, where we must alter the display order whenever the order map is changed, and insert or remove entries from the order map to match its source.

Other thoughts along these lines included *parent* maps for delegating lookups (similar to \ac{JS}'s prototype system), *inverse* maps, and *meta* maps for possibly collecting all of these (drawing inspiration from Lua's metatables). Of these, we will only discuss inverse maps in more detail.

Inverse maps come from the view of a map as a mathematical function from key names to values. Often in advanced data structures (such as those for graphical diagrams) it is essential to know "who points to me" via some key. For example, the question "Is this node the `source` of anyone else?" is a natural one, but normally it is impossible to answer based on ordinary dictionary keys. In ordinary programming languages, this information needs to be kept track of separately; say, in a manually synchronised list called `sources` that lives on the node. It is frustrating that the "forward" question is trivially answered by just following a map entry, yet the "backward" question has to be hacked around like this.^[Norvig's "Relation" pattern\ \parencite{DynPat} for dynamic languages is relevant to this sort of concern.]

An inverse map would somehow collect all references to a map from other ones. A user-level "map" would be implemented by two dictionary structures, the forward and backward halves, which are automatically kept in sync by the substrate implementation. The previously mentioned issues of access, mutation and others also rear their ugly heads here, so we can be forgiven for discarding the idea for the sake of making progress. Still, a properly worked out implementation would provide a valuable service for a high-level substrate.

# Graphs vs. Trees
A classic debate in the world of explicit structure is whether to use restricted *tree* structures or to allow arbitrary *graphs*. A tree has the advantage that every node has a single parent, which is a useful canonical answer to the question "what context am I in?". On the other hand, many practical problems do not fit inside a tree structure; either because they are DAGs, and a node can have multiple parents, or because they involve cyclic relations. Because we did not know what sort of things we would require in BootstrapLab, we erred on the side of freedom and supported full graph relations. This bit back at us in two ways, both involving the graphics domain.

Firstly, cyclic structures need to be rendered with care; a \naive\ depth-first search will never terminate. For a long time, we did not have any cyclic structures and got away with a depth-first approach to \ac{DOM} generation in the temporary state view (Section\ \ref{temporary-infrastructure-in-bootstraplab}).

Secondly, while this was the case, the graphics sub-region of state needed to be a tree. Spatial containment and other visual nesting (e.g. for the tree editor) is a tree structure, as is the underlying parent-child relationship of THREE.js objects. Many aspects of rendering the tree editor required the ability to ask "what context am I in?" but this is unanswered by default in a graph substrate. Providing a "parent" key for each node would not do---this would be a cyclic reference. Instead, we kludged it: the first map to reference another map becomes its "parent", and this lasts until the reference is deleted. This parent property is available from \ac{JS}; as we port the tree editor to Masp, we will have to decide how to expose it in-system (probably through a special instruction).

Of course, we eventually did require cyclic structures---for the tree editor! Each graphics node in the editor has a `source` key providing a way for edits to propagate back to the source state node. All edit nodes live in the graphics tree, including the one corresponding to the root node of the state. In this case, the `source` points all the way upwards to this root node. This cycle broke our state view and there was much gnashing of teeth to hack around this. Eventually, we bit the bullet and improved the state view \ac{JS} to cope with cycles---having previously hoped we were done with this temporary infrastructure.

Let this be a warning that Alignment (Force\ \ref{alignment}) will come for you in the end. If your substrate allows cycles, your state view must tolerate them!


\joel{ NOPE
# Appendix: Tree Editor
One interesting issue is the "display update" problem. For the \ac{JS} tree view, we simply intercept all state updates and use `querySelector` to lookup the \ac{DOM} node with the map's internal ID. Internal IDs are not currently accessible in-system, though we could provide instructions for this purpose. How, then, can we ensure that the relevant "display state" for a map entry is updated when it changes?

Even if we had an answer for this, there is a bigger problem. The \ac{DOM} state is not part of the "state" of our substrate. When our state changes, this triggers a \ac{DOM} state change. But the system's graphics are based on a special region of ordinary system state. Therefore, a state change will trigger another state change (to the graphics state.) What if the "graphics state" is being visualised in the tree editor? It will need to change too! And so on.

State change should cause state change (finite extent) in scene tree, if that state is visualised there.

In other words, there exists some address `->` graphics node mapping (e.g. in map metadata) inside the substrate.

Analogous to Virtual Memory Management GDT, LDT descriptor tables.

Yet, this makes too much complexity e.g. the graphics tree format part of the substrate!!

Alternatively: consider the humble hex editor. It doesn't change memory, trigger a hardware interrupt, and then sync the display data to match. Causality goes not:

```
Edit --> Mem --> OnMemChange --> Display
```

But:

```
Edit ---> Mem
     \--> Display
```

Aha! But how do machine-level debugger GUIs work? When instructions, the OS, or whatever ... change memory, how does it notice and display the changes??

It must either re-poll everything after a run/step, or get a changelog from OS...?

Changelog could be a special part of state expected to change constantly, so we just poll it on demand. We assume it's stale by default when looking at its visual representation.
}
