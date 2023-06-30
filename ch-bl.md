\hypertarget{bl}{%
\chapter{BootstrapLab: The Three Properties in the Web Browser}\label{bl}}

\newtheorem{force}{Force}
\newtheorem{requirement}{Requirement}

\joel{
Having tried the "notation-first" approach with diminishing returns in Chapter\ \ref{year1}, we now turn our efforts towards prioritising self-sustainability. Recall that we took the "\OROM{}" half of the COLA design as our starting point and made things happen with plain JavaScript. This left us at the mercy of text strings as a starting point for building the "code" half.}

We now arrive at the main contribution of this work: the construction of a programming system with our Three Properties, which we call *BootstrapLab.*^[\url{https://github.com/jdjakub/BootstrapLab/tree/master/orom/computation}] On its own, the existence of such a system acts as a constructive proof of our Thesis Statement (Section\ \ref{thesis-statement-and-contributions}). However, we are less interested in the fact that such a thing *is* possible than we are in *how* it was done and what we can learn from the process. We have discussed our Three Properties in previous chapters as best we could while remaining in the theoretical realm, but since they are meant to be properties of programming systems, it is essential that we give practical experience an opportunity to teach us something about them that we could not learn otherwise. We would expect not only to learn how to *achieve* such properties, but also to gain clarity on their nature.

Thus, in this chapter^[This chapter was adapted from our 2022 *Onward!* Essay entitled "Ascending the Ladder to Self-Sustainability"\ \parencite{Onward22}] we take the self-sustainable \ac{COLA} as our inspiration and seek to build up to its Lisp-like "behavioural" half by means of code with Explicit Structure. We critically analyse its development process and identify ideas that may apply more generally. This will lay the foundations for creating new self-sustainable programming systems. We will have to wait until Chapter\ \ref{tech-dims} to evaluate BootstrapLab in terms of our Three Properties, but we will summarise it in terms of Olsen's criteria for user interface systems\ \parencite{EvUISR} at the end of this chapter.

What is presented here is not necessarily a chronologically accurate account, but a *rational reconstruction* of the steps involved. For each step, we describe the general task at hand, illustrate this with concrete decisions made in the implementation of BootstrapLab and, where appropriate, sketch possible alternative decisions and their likely consequences. In other words, it is a depth-first exploration of the process with some alternative branches suggested along the way. It can be understood on two levels:

1. It tells the development story of a concrete system. BootstrapLab is a novel self-sustainable system, based on explicit structure, built on top of the web platform.
2. It presents a rational reconstruction of the logical steps needed to bootstrap a *general* self-sustainable programming system (by taking BootstrapLab to be representative of the important parts of such a task). It highlights design *forces* and *heuristics* for resolving them which can be used by designers of future self-sustainable systems.

# Methodology

We follow in the spirit of \ac{COLA}, but we aim to bootstrap a graphical and interactive self-sustainable system instead of a textual one based on batch-mode transformations. The system should not have barriers in the way of using custom notations. We also want to work with an *interactive* system, meaning that the user should be able to modify the state of the running system through manual gestures and not just programmatically. 

This approach can better exploit the graphical and interactive capabilities of modern computing, but it also sidesteps the tedious accidental complexities of parsing and serialising text. Similarly, making the system interactive will allow the user to better understand the consequences of individual small changes and will, in turn, support a virtuous cycle of self-improvement.

Our desire is to make an interactive, structured "port" of the \ac{COLA} approach. This is unexplored territory. It must be emphasised that finding the right "final design" upfront should not be necessary and would defeat the spirit of the enterprise. The point is to build an initial kernel which is then sufficient for evolving and improving itself.

Unlike \ac{COLA}, we do not write an initial object system in a language like C++. In order to support interactivity, structure, and graphics, we begin with a platform that already conveniently supports those features. This forms a suitable "blank slate" from which to gradually develop the system into a self-sustainable state. At each stage, we take stock of which changes can feasibly be achieved at the user level within the system, versus those that can only be achieved at the implementation level (recall Section\ \ref{user-vs.-implementation-levels}). We then ask ourselves: how can we imbue the user level with control over some of these aspects?

The following sections propose key steps for evolving self-sustain\-ability in this way, informed by our actual experience applying them in BootstrapLab. We will point out the *forces* that shape the design and the *heuristics* by which we resolve competing forces. As we proceed through the development journey, we will reflect on the heuristics in light of actual practice and compare our choices for BootstrapLab with the corresponding stages of two other systems: \ac{COLA} and the Altair 8800 of the 1970s.

# Concepts and Terminology

\tomas{It would be nice to include the diagram I sketched here (if we think it can be useful for explaining things...)}

Following the terminology in Section\ \ref{platforms-and-substrates}, we use the term *product system* (or simply "the system") to refer to the programming system that we evolve towards self-sustainability. We use the *producer system* (or simply "producer") to bootstrap the product system. The producer is divided into two layers: the *platform* consists of all the pre-existing capabilities of the producer, while the *substrate* is the basis for the product system that we have to build. We use the term *in-system* to refer to changes made within the product system, by using it as a programming system at its user level. We also model the product system as having a *state* that *changes over time* as we introduced in Section\ \ref{two-fundamentals-state-and-change}.

\joel{
This is necessarily the case for any interesting interactive programming system, regardless of its programming paradigm. Even in functional, declarative, reactive or logic paradigms, the evaluation or re-computation in response to interaction with a user produces a new (changed) state.

When discussing state, we refer to both the visible interface and the hidden internal state of the programming system. The state always consists of substructures such as byte arrays, object graphs or trees. Correspondingly, a change to the state can be decomposed into sub-changes that affect small parts of the state.^[It may be argued that a very high-level programming paradigm would make it impossible to affect only small parts of the state. This may, however, not be a suitable starting point for a self-sustainable system.] This gives rise to primitive *instructions* that describe state change at the finest level of granularity.
}

Recall the definition of *bootstrapping* we gave in Section\ \ref{precursors-of-self-sustainability}. Our task is to explore the question: how do we bootstrap *interactive graphical* self-sustainable systems? Note that by definition, what we do with a self-sustainable system is open-ended. This chapter is solely concerned with *getting there* from the ordinary world, which is why we will spend so much discussion on the design of the substrate. This determines how the product system can evolve, how soon can it become self-sustainable and to what extent.

# Journey Itinerary
The rest of the chapter documents the steps involved in designing a self-sustainable system. Be advised that the sequence is a *rational reconstruction.* The implementation of BootstrapLab followed a more meandering path, but the following steps gesture at the Platonically optimal pathway for bootstrapping a self-sustainable programming system:

1. *Choose a starting platform.* The platform is a *pre-existing* programming system that we use to create and run the product system. The platform cannot be re-programmed, let alone to become self-sustainable, but it allows us to build a self-sustainable product system. To choose a platform, we consider its distance from desired substrate features and personal preference.
2. *Design a substrate.* The substrate defines the basic infrastructure supporting the product system: how the state is *represented* and *changed*. The design of a substrate re-uses parts of the platform where possible and extends it where necessary. We must decide which platform capabilities to use to represent the state and how to expose graphics and interaction. We design a minimal *instruction set* describing changes to the state, which can be represented using the state. We then use the platform to implement an engine that executes these instructions.
3. *Implement temporary infrastructure.* Use the platform to implement tools for working within the substrate, most importantly a *state viewer* or editor. These tools constitute a "ladder" that we will pull up behind us once we have ascended to in-system implementations of these tools.
4. *Implement a high-level language.* The substrate's instruction set (ASM) is cumbersome, so ensure programs can be expressed in-system via high-level constructs. Decide how to represent expressions as structured state and whether to *interpret* or *compile* them into ASM. Ideally, develop such an engine in ASM gradually and interactively. Alternatively, implement it at the platform level and later *port* it to ASM or the high-level language itself.
5. *Pay off outstanding substrate debt.* Port all remaining temporary infrastructure into the system, taking advantage of the infrastructure itself and the high-level language. The result is a *self-sustainable* programming system.
6. *Provide for domain-specific notations.* Use the self-sustaining state editor to construct a more convenient interface for editing high-level expressions. Add *novel notations and interfaces* as needed. Use these not just for programming new end-user applications, but also to improve the product system itself.

What we have here looks like a Waterfall development plan, each step strictly following from the completion of the previous. This presentation is convenient as a summary, but in practice, the sequencing here need not be so rigid. Adjacent steps may overlap, or we may need to prototype and revise a previous step in light of the result.

The general outline also resembles the discredited "recapitulation theory" in biology, where in order for an embryo to develop into a full organism, it passes through the evolutionary history of its ancestors. In other words, for a *particular* cell to develop into an animal, it needs to fast-track its ancestors' evolution from a cell in the distant past. While this has since been rejected in biology, it is a good summary of what is going on in our project here.

The bootstrapping of a particular self-sustainable system fast-tracks the historical development of computing's abstractions. It begins at the low level and ascends through to higher-level languages, each time building the next stage in the current one. This journey could be seen as an attempt to reconstruct programming on top of a more structured, graphical substrate than the byte arrays we all had to use the first time around. With that in mind, let us now proceed to the first stage.

# Choose a Starting Platform

> The platform is a *pre-existing* programming system that we use to create and run the product system. The platform cannot be re-programmed, let alone to become self-sustainable, but it allows us to bootstrap a self-sustainable product system. To choose a platform, we consider its distance from desired substrate features and personal preference.

\tomas{I removed the "blank slate" phrase here, because the platform is not a blank slate at all (maybe - it is the "slate" itself? which is of course not blank because it is granite!)}

The first step is to choose the platform that we will use as the basis for the product system. This could be any existing high-level or low-level programming system. An important factor is simply personal familiarity or preference for a particular platform. This plays a role during bootstrapping, but is destined to become irrelevant once self-sustainability is achieved.

The other major consideration is the primitives provided by the platform. They influence how we can design the substrate on top of it in the next step. If we begin with a high-level platform with many convenient features (\eg{} graphics and audio capabilities), then we will have to regard them as black boxes. We may expose them as primitives in the product system, but we will not be able to *re-program* them in-system since we cannot re-program the platform.

Alternatively, such imported convenient high-level features could later be *re-implemented* in the product using more basic primitives. However, this would delay the point from which we can work fully in-system. This foreshadows a coming design tension in the substrate (Section\ \ref{the-major-design-conflict}).

In the Altair 8800, the *platform* comprised linear memory (state) and native CPU instructions (state change). The platform did not provide other tooling aside from switches to manually set memory values (Figure\ \ref{fig:altair}).

\begin{figure}
\centering
\includegraphics[width=8cm]{Altair-8800.jpg}
\caption[The Altair 8800 microcomputer]{The Altair 8800 microcomputer and its front panel of switches. \emph{Image credit: \parencite{Altair}.}}
\label{fig:altair}
\end{figure}

In \ac{COLA}, the platform is C\ \parencite{OROM} or C++\ \parencite{COLAs} and the Unix command-line environment; in other words, it is the Unix programming system (Section\ \ref{the-unix-paradigm}).

In BootstrapLab, we chose \ac{JS} and the Web platform. This provides us with built-in Web technologies and libraries (including graphics) and the browser developer tools. This platform provides a range of convenient tools to assist bootstrapping. Because of its large scope, we have to carefully choose primitives to expose to the product system.

*What can be changed at the user level?* At this point, there is no product system to speak of yet. This means that nothing can be changed in-system. The platform can, in principle, be modified, but by assumption this is so unfamiliar or uneconomical that the reader has opted to make a self-sustainable system instead.

# Design a Substrate
> The substrate defines the basic infrastructure supporting the product system: how the state is *represented* and *changed*. The design of a substrate re-uses parts of the platform where possible and extends it where necessary. We must decide which platform capabilities to use to represent the state and how to expose graphics and interaction. We design a minimal *instruction set* describing changes to the state, which can be represented using the state. We then use the platform to implement an engine that executes these instructions.

With the *platform* defined as the already-existing programming system that we start from, we define the *substrate* as the basic infrastructure, implemented via the platform, necessary for the product system. This substrate is the part of the system which we have control over (being programmed *by us*, unlike the platform itself) yet which we do not expect to expose from within the system. In other words, the substrate is the small non-self-sustainable core that supports the self-sustainable product on top of it.

In short, our division is as follows:

\joel{
* *Platform*: not created by us, not self-sustainable.
* *Substrate*: created by us atop the platform, not self-sustainable.
* *Product*: created by us atop the substrate, self-sustainable.
}

\vspace{1em}
\begin{tabular}{ccc}
\toprule
 & Created by us? & Self-sustainable? \\
\midrule
Platform & No & No \\
Substrate & Yes, atop Platform & No \\
Product & Yes, atop Substrate & Yes \\
\bottomrule
\end{tabular}
\vspace{1em}

The design of the substrate can be considered along two axes (Figure\ \ref{substr-tab}). The first dimension follows the distinction between data and code, or *state* and state *change* (Section\ \ref{two-fundamentals-state-and-change}). We must first decide how the *state* of the system will be represented. Often, this is a matter of choosing an appropriate subset of what the platform already provides. Then, we decide how primitive *changes* to that state can be described and define the instruction set.

The second dimension follows the division between the *computer* and *human* actors. The full state of the system will be an internal data structure, but a *part* of the state---comprising the state of the user interface---can be directly seen by the user. Similarly, *change* can be performed automatically or manually. There must be a way to run instructions automatically at a high speed, but the user interface must also provide controls for a human to make changes at their own pace.

\joel{
| Domain \\ Agent | Human (Manual) | Computer (Automatic) |
|:--------------:|:--------------:|:--------------------:|
|      State     | User Interface |    Data structures   |
|     Change     |   UI Controls  |     Instructions     |
}

\begin{table}
\centering\small
\caption{The conceptual divisions of the substrate.}
\begin{tabular}{c|cc}
\toprule
Domain \textbackslash{} Agent & Human (Manual) & Computer (Automatic) \\
\midrule
State & User Interface & Data structures \\
Change & UI Controls & Instructions \\
\bottomrule
\end{tabular}
\label{substr-tab}
\end{table}

While the foregoing model applies to programming systems generally, a special condition is required for those that are self-sustainable. We must represent instructions as pieces of state, as opposed to having "two types of things"---ordinary data, and code---which must be viewed and edited using completely different tools. This property, conventionally known as *homoiconicity*, means instructions can be generated and manipulated just like ordinary state, whether programmatically or manually. Only if this is possible can higher-level abstractions can be built up, in-system, from the low level.

\joel{Isn't homoiconicity basically just von Neumann instead of Harvard architecture? any difference?}
\begin{requirement}[Homoiconicity]
Instructions must be readable and writeable as ordinary state.
\end{requirement}

## \ac{COLA}'s Low-Level Byte Arrays
In \ac{COLA}, the substrate is quite minimal and the majority is inherited "for free" from the low-level runtime environment provided by Unix (Section\ \ref{the-low-level-binary-world}).

At the lowest level, state in \ac{COLA} consists of an array of bytes, addressed numerically. Some structure is imposed on this via C's standard memory allocation routines, refining the model of state to a graph of fixed-size memory blocks and the stack. Changes to this state are represented as machine instructions encoded as bytes. This is the basic state model of a C program; the sample code for \ac{COLA} embellishes this with little more than a way to associate objects to their vtables^[A *vtable* specifies object behaviour by supplying runnable code for a requested method name. It is separate from the object "instance" so that multiple objects can share the same behaviour.] and a cache for method lookups.

This bare-bones, low-level substrate does not require much development on top of the platform and so it is quicker to complete. The ontology of state is copied from the platform, and in this case the machine instructions can be inherited too.^[In general, the internal representation of code in the platform will be unavailable to us when programming with it, so we expect not to be able to inherit it. This low-level platform is a special case, where we do have access to code if we are willing to write instructions using their numerical codes.] Completing the substrate more quickly means we can start working in-system sooner, but there is a downside: it may be cumbersome to work with such minimal functionality. The unfortunate effect would be that we speed through a primitive substrate, only to suffer slow progress at the beginning of in-system development.

Building back up from machine-code level may be an impressive hacker achievement or useful for pedagogy\ \parencite{Mu}. But it is clearly not optimal, speed-wise, when we already have a higher-level platform to program with.

In the other direction, there is no limit to how fancy we could make the substrate in terms of high-level abstractions and convenient features. However, these would take much longer to implement and delay in-system development. Moreover, this risks doing a lot of work that can never benefit from in-system innovation feedback (recall Section\ \ref{the-key-benefit-innovation-feedback}); the substrate's implementation will not be modifiable from within the system it supports.

## The Major Design Conflict
We clearly have two opposing tendencies here, which we will formalise as follows:

\begin{force}[Avoid Boilerplate]
\label{avoid-bp}
Push complex features into the substrate to avoid wasting in-system development time on them.
\end{force}

\begin{force}[Escape The Platform]
\label{escape-plaf}
Push complex features in-system to avoid delaying in-system development and to have them benefit from innovation feedback.
\end{force}

We will refer to these throughout the journey. They conflict over where the implementation of convenient functionality should reside. In any specific design, they will resolve in some compromise. It is helpful to consider the extreme points of this.

Force\ \ref{escape-plaf} wants to get the substrate over with as quickly as possible, eager to escape the (real) limitations of the platform and get working in a system that *can* be arbitrarily changed. Force\ \ref{escape-plaf}, left unchecked, will guide us to adopt a substrate resembling a Turing machine: have a tape for the state; instructions for manually shifting left and right, reacting to the current symbol, and writing a new one; have a user interface in which to do these things manually. Such a substrate is so simple it could be coded in an hour or two. Yet our first duties in-system will be to implement extremely basic features, like data addressing and arithmetic, in an extremely tedious way. The endpoint of Force\ \ref{escape-plaf} is the Turing Tarpit.

On the other hand, if we follow Force\ \ref{avoid-bp} unchecked, we spend much time and effort working with the platform to produce, in effect, a *complete* novel programming system. Any feature we would find useful in-system, we would have already implemented outside it. Yet this means that all the important functionality could not be changed except by going back to the source code in the platform; we'd have created a boring old *non-*self-sustainable programming system. The endpoint of Force\ \ref{avoid-bp} is programming-as-usual. 

A symptom of the latter failure mode would be that we never felt comfortable leaving the platform behind and continuing development from within the system. Self-sustaining systems are meant to be *grown* from a small enough starting point; we shouldn't need to come up with a flawless design ahead of time. This will only be possible if we artfully balance Forces\ \ref{avoid-bp} and\ \ref{escape-plaf} so that the in-system programming experience becomes tolerable in a reasonable timeframe.

\paragraph{Reflections on the Machine-Level Approach.} We experienced something like the Force\ \ref{escape-plaf} absurdity for \ac{COLA} when following the sample C implementation of its object system. The code was easy enough to comprehend and compile, but what we were left with was a system living entirely in memory lacking even a command-line interface. In order to develop the system, it seemed necessary to run it in a machine-level debugger.

\tomas{I wonder though if this is partly over-simplicity on the platform level, not substrate?}

Even if we had stayed with it, we would still be stuck in the low-level binary world which is unfriendly for humans to work with, as we explained in Section\ \ref{the-low-level-binary-world}. Instead of names, we only have numbers for addressing things. The state is flat and we cannot insert or grow something without physically moving other content to make space. Any structure, such as trees or graphs, has to be *faked* as memory blocks pointing to each other.

This type of substrate is still better than a Turing machine, and was a historical necessity in the early days of computing. But nowadays, we have the opportunity to leave this behind, and instead build new systems on top of a "low level" that is nevertheless *minimally* human-friendly (Section\ \ref{the-minimally-human-friendly-world}).

\begin{heuristic}[Minimally human-friendly low-level]
\label{min-friendly-ll}
Ensure the substrate natively supports \emph{string names} and \emph{substructures.} This is a minimal response to Force\ \ref{avoid-bp} that still keeps the substrate simple enough and thus does not strongly conflict with Force\ \ref{escape-plaf}.
\end{heuristic}

## BootstrapLab's Simple, Structured State Model
For the design of BootstrapLab, we chose the Web platform and \ac{JS} for personal preference reasons. This imposed a number of design decisions on the substrate, due to a tendency for earlier choices to determine which later ones will feel "natural" or "fitting".

In our high-level platform language \ac{JS}, state is a graph of plain \ac{JS} objects acting as property dictionaries. Suppose we *still* chose a low-level binary substrate like that of \ac{COLA}. This would no doubt be possible: declare one giant \ac{JS} array called `state`, design numerical instruction encodings which overwrite numbers at certain indexes, etc. Yet this would feel like a perverse waste of something the platform was giving us for free. 

\ac{JS} already provides the basic human affordances of naming and substructure, so why would we throw them away and force ourselves to implement them in-system? The low-level \ac{COLA} substrate does plausibly follow from its base C platform. Our choice of \ac{JS} as the platform encourages us to preserve its own state model in the substrate we design.

Similarly, it would make no sense to represent instructions as numbers or strings. While in the binary world, machine instructions are byte sequences with bitfields for opcodes and operands, in a dictionary substrate inherited from \ac{JS}, it makes sense to have explicit fields for this data:

```
{
  operation: 'copy',
  from: [ alice, 'age' ],
  to: [ bob, 'age' ]
}
```

As this example shows, addresses in a dictionary-based state model consist of an object reference and a key name.

This "preservation" incentive pervades the journey from platform to product system. The substrate should leverage representations made possible by the platform, while the instruction representation should leverage the structuring of state provided by the substrate. This will also apply to further subdomains expressed in the state model, such as graphics and high-level programming expressions. We formalise this as the following:

\begin{force}[Alignment]
\label{alignment}
Everything should fit: instructions, high-level expressions, and graphics expressions should all fit the substrate, and the substrate should fit the platform.
\joel{Violation \eg{} flat array as state via JS, fails to preserve a feature. Ideally, this force propagates all features from A to B.}
\joel{Call this "preservation"?}
\end{force}

In the end, our substrate largely inherits the state model, only making simplifications. For example, \ac{JS} objects have prototypal inheritance, meaning that a simple "read" operation of a property requires potentially traversing a chain of objects. Our substrate here omits this, so reads are quite simple. Additionally, \ac{JS} includes a special `Array` object type. We omitted this, opting to represent lists as maps^[We refer to our substrate's basic dictionary structure as the *map* for brevity.] with numerical keys. This unification means that the state model only has one type of composite entity, a fact we will exploit later for the high-level language.

We also noticed that we would not get very far if all our progress in-system could be wiped clean by losing our browser tab. Our platform, sitting within the Unix paradigm (Section\ \ref{the-unix-paradigm}), does not provide *persistence* out of the box, so we had to implement a mechanism in the substrate. We walk the state graph from the root node and discover a spanning tree, specially marking cyclic or double-parent references. We then serialise this into a JSON file which we can load by undoing the process. This is reminiscent of the image-based persistence in Smalltalk, though it is frustratingly manual. Nevertheless, it was critical to patch this unfortunate aspect of the platform and this was enough to do so.

Even though \ac{JS} is a high-level language, we consider this substrate low-level *relative* to the platform below it. Force\ \ref{avoid-bp} gave us several ideas for convenient features of a smarter state model, but Force\ \ref{escape-plaf} urged us to press ahead without them and see if we needed them later. Appendix\ \ref{bl-trivia} contains these details.

### Designing the Instruction Set
While the "data" half of the substrate may be easy to inherit from the platform, the "code" half is typically not. Simply including an interpreter for source code in \ac{JS} is not an option; this would embed a reliance on a strings and parsing in the core of the system, against our desire for Explicit Structure.

Slightly better would be an interpreter for the \ac{JS} abstract syntax tree. However, Force\ \ref{escape-plaf} still pushes against this. A high-level language interpreter is nontrivial even without parsing and would delay our ability to work in-system. Also, an interpreter is a computer program; this program, or parts of it, might be best expressed or debugged via particular notations; by having it in the substrate, we'd restrict ourselves to the interface of \ac{JS} in our text editor.

Instead, consider what it takes to implement the interpreter for Assembler, a.k.a. the Fetch-Decode-Execute cycle. We fetch the next small change to make to the state (an instruction). We do a simple case-split on the opcode field; we carry out some small change to the state; rinse and repeat. With this, we can surely mirror the real-world development of higher-level languages from lower ones.

\begin{heuristic}[Use Imperative Assembler]
\label{use-asm}
Begin from imperative assembler, as this allows us to make arbitrary changes to the state using a minimal interpreter that is quick to implement. Force\ \ref{escape-plaf} outweighs Force\ \ref{avoid-bp} here.
\end{heuristic}

It is important to stress that this "assembler" is relative to the form of the substrate. If the substrate is binary memory, "assembler" will refer to machine instructions. But in our case of a minimally human-friendly low level (Heuristic\ \ref{min-friendly-ll}), there is nothing binary about them. The instructions express operations on structured objects with names and are, themselves, represented as structured objects with names. Similarly, "imperative" just refers to the fact that the instructions are arranged in a sequence from the point of view of the interpreter, because it is easier to implement a fetch-execute cycle than, say, a resolver for a dependency graph. The above considerations lead us to Heuristic\ \ref{simple-asm}.

\begin{heuristic}[Simple Assembler]
\label{simple-asm}
Prefer fewer instruction types (\acs{RISC}) over more (\acs{CISC}). This reduces the size of the interpreter and will be quickest to implement. It will make programs longer, but this can be mitigated by a high-level programming language. Force\ \ref{escape-plaf} outweighs Force\ \ref{avoid-bp} here too.
\end{heuristic}

Right away, we know there will have to be a special piece of state for the *instruction pointer*. This could indicate the *current* instruction or the *next*; we chose the latter for BootstrapLab and called it `next_instruction`.

The value of this "register" is determined by how exactly we fetch the next instruction. Perhaps each one has a `next` field which we can simply follow. In this case, the `next_instruction` will simply be the instruction itself. This also gives us convenient conditionals (\eg{} fields called `if_true` and `if_false`) but means that instruction *sequences* will have a nesting structure. This latter consequence may be inconvenient for presentation in a tree view. For BootstrapLab, we chose the alternative of numerically indexed lists of instructions which easily display in a column. This choice determined `next_instruction` to instead hold an *address* made of container map and key name:

```
next_instruction: {
  map: <instruction list>,
  key: 1 // i.e. first instruction in the list
}
```

Here, the "fetch" step involves dereferencing the address and then incrementing the key name.

Next, we turn to what types of instructions we need. Alignment (Force\ \ref{alignment}) means that, given a state model, obtaining an instruction set should be more of a "derivation" than a hard design problem. This is because some choices are obviously inappropriate and others clearly fitting to the state model. For example, in a tree-structured state model, it would be foolish to have instructions that can only see the root level:

```
{op: 'copy', from: 'source_key', to: 'dest_key'}
```

Without the ability to read or write keys *within* an arbitrary tree node, as far as programmatic change is concerned, the state becomes a *de facto* flat list instead of a tree. Therefore, it is critical that anywhere in the state can be accessed or modified by an appropriate instruction sequence.

The checklist of basic functions for an instruction set to be Turing-complete is as follows:

1. Copy from one location to another (a "literal" is just copied from the instruction itself)
2. Treat a value as an address and follow the reference
3. Unconditional jump (copy a value to the instruction pointer)
4. Conditional jump (take a path based on a runtime condition)

Force\ \ref{avoid-bp} may push us to include basic boilerplate like arithmetic or an operand stack. Furthermore, it is advisable to have an "escape hatch" into the platform if possible. In BootstrapLab, our platform language \ac{JS} provides the `eval()` function to execute a string of \ac{JS} code. We exposed this as a `js` instruction. This allows us to use and store \ac{JS} code in the *running* system instead of having to edit the source file.

The resulting instruction set for BootstrapLab was derived from these considerations, as well as extreme application of Heuristic\ \ref{simple-asm}. It uses the top-level map as a set of "registers" whose contents are *immediately* accessible. State that is "further away" is accessed by following key paths from there, or from existing map references. There are several special registers used by instructions, but other names in the top level are available as local variables in user code. The instructions are as follows:

* `load` fills the `focus` register with a literal value. \joel{For example, `load reg1` causes the `focus` register to contain the string `reg1`.}
* `deref` treats the value in `focus` as naming a register and copies the register's value into `focus`. \joel{For example, if `reg1` contained the value `42`, `deref` would store `42` in `focus`.}
* `index` expects a map in the `map` register and a key name in `focus`. It looks up this key in `map` and replaces `map` with the value. \joel{For example, if the value in `map` contains a key `foo` with value `42` and `focus` contains `foo`, `index` will store `42` in `focus`.}
* `store` copies the value in `focus` to a named register. Alternatively, if no register is present, it copies the value in the `source` register to the destination identified by `map` and `focus`, as with the `index` instruction.

An instruction is represented as a map with an `op` field for its name and other fields for parameters. For example:

```
{ op: 'store', register: 'source' }
```

It is remarkable that these few operations really are sufficient even for conditional and unconditional jumps. A jump is achieved by overwriting `next_instruction`, and this can be conditionalised by `index`ing a map of code paths based on a selector. We made the decision that `index`, if accessing a key not present in the map, will try and retrieve the special key `_` instead. This supports a generic "else" or "otherwise" clause for conditionals.

The minimal, microcode-like instruction set here was an experiment in extreme parsimony; see Appendix\ section\ \ref{the-minimal-random-access-instruction-set-and-its-perils} for the gory details. Although it was interesting, certain basic operations (such as jumps) are extremely verbose, taking many instructions. Although it was quick to implement these instructions in \ac{JS}, it was too tedious to work with them in-system. In retrospect, it looks like we went too far with Force\ \ref{escape-plaf} here and fell into its associated Turing Tarpit trap. We thus consider an *extreme* interpretation of Heuristic\ \ref{simple-asm} refuted for the purposes of working in-system sooner. We recommend achieving a better balance by including direct path arguments in instructions (\eg{} "copy `a.b.c` to `x.y.z`" as a single instruction), as well as separate (un)conditional jump instructions.

### Graphics and Interaction
Now that we have covered the computer-oriented part of the substrate, we turn to the human-oriented user interface state and change aspects. One way we wish to distinguish BootstrapLab from \ac{COLA} is that graphical interfaces are present from the beginning and not just an afterthought. There are two factors here: how graphics are represented in the substrate, and how they are actually displayed.

It may be useful to see this as a microcosm of the entire journey. First we must select a graphics library available for our platform (\ie{} the *graphics platform*). Then we must decide how graphics are represented in our substrate (a graphics *sub-*substrate) and how these graphics actually end up on our screen.

In BootstrapLab, we chose to build off the THREE.js 3D graphics library as our platform. As for the substrate, we face an immediate choice between so-called "immediate mode" and "retained mode" conventions. In immediate mode, we draw and change graphics by issuing commands; a "code-like" approach. In retained mode, the state of the scene is represented as some structured arrangement of state. When we want to change something, we simply change the relevant part of the state and the display should automatically update.

Immediate-mode in this case could be realised by, say, exposing all the relevant THREE.js functions as special instruction types. In actuality, however, this sounded far too tedious to work with; Force\ \ref{avoid-bp} won out and we opted for retained mode instead. The rest of the design then fell out via Alignment (Force\ \ref{alignment}).

Consider the low-level binary substrate in which microcomputer games were programmed. In this world there is a special region of memory, the *framebuffer*, which is treated as the ground truth of pixels displaying on the screen. To draw, programs rasterise shapes into pixels and write to the framebuffer.^[In Commodore 64 BASIC, this would be accomplished with commands like `POKE 1024,1`.] The framebuffer has a flat structure---two-dimensional, yet not by any means nested---aligning with the substrate it sits within. This suggests that a natural choice for retained-mode graphics representation can be found by inspecting the substrate. In BootstrapLab's case, the natural choice is not a flat "framebuffer" but a tree structure of data describing shapes and text---vector graphics.^[Further rationale for this approach can be seen in\ \parencite{Dadgum66}.]

\begin{heuristic}[In-state graphics]
\label{in-state-graphics}
Make graphical interfaces expressible as ordinary state in a special location. Having graphics built into the substrate responds to Force\ \ref{avoid-bp} while Force\ \ref{alignment} directs us to use a representation that fits the state model.
\end{heuristic}

In BootstrapLab, this is a sub*tree* of the state under the top-level name `scene` (Figure\ \ref{fig:scene-tree}). There are several special keys (\eg{} `text`, `position`, `color`, `children`) which have graphical consequences. Other keys may be used as ordinary state.

\begin{figure}
\centering
\includegraphics[width=8cm]{scene-tree-example.png}
\caption[BootstrapLab scene tree]{Example of how nested tree fields are represented (right) vs. the rendered output (left). The right-hand half is the temporary state view discussed in Section\ \ref{implement-temporary-infrastructure}.}
\label{fig:scene-tree}
\end{figure}

For interaction, we need to expose the platform's ability to listen for user input. In BootstrapLab, we execute a named code sequence in the substrate from JavaScript event handlers, which now function as "device drivers" (Figure\ \ref{lst:devdrv}).

\begin{figure}
\begin{lstlisting}[language=JavaScript]
window.onkeydown = e => {
  state.set('input', 'type', 'keydown');
  state.set('input', 'key', e.key);
  let input_handler_code = state.get('input', 'handler');
  save_context();
  state.set('next_instruction', new state.Map({
    map: input_handler_code, key: 1
  }));
  asm.execute_till_completion();
  restore_context();
};
\end{lstlisting}
\caption[BootstrapLab ``device driver'' for DOM events]{``Device driver'' triggering a generic event handler sequence in-system.}
\label{lst:devdrv}
\end{figure}

This is a basic sketch with some issues elided that a complete account would cover. For example, what happens when an input event occurs during the handling of a previous event? Possibilities include ignoring the extra event or providing some sort of stack analogue^[Of course, in a structured substrate, there is room for improvement on the linear form of the low-level machine stack; see Section\ \ref{implementing-masp-for-bootstraplab} for how we did this for the high-level language.] for nested handlers. Such a data structure may also be necessary for saving and restoring the instruction pointer along with other context. These concerns have analogues in interrupt handling for operating system design, which could be consulted for further guidance.

### BootstrapLab Substrate Summary
Computer state is a graph of maps; lists are just maps with numerical keys. Instructions are `load`, `deref`, `store`, `index`, `js`. Special top-level keys are `focus`, `map`, `source` and `next_instruction`. User Interface state is controlled via the special `scene` subtree of state. Each node may use special keys like `text`, `width`, `height`, `color`, `position`, and `children`, as well as arbitrary other keys for user data.

*What can be changed at the user level?* System state can be modified and instructions can be executed, but only using the cumbersome capabilities of the platform. In case of BootstrapLab, this means using the \ac{JS} debugging console to edit state and call a function to execute a certain number of instructions.

# Implement Temporary Infrastructure
> Use the platform to implement tools for working within the substrate, most importantly a *state viewer* or editor. These tools constitute a "ladder" that we will pull up behind us once we have ascended to in-system implementations of these tools.

In most cases, the base platform will provide some way of viewing and modifying state, but this is typically inconvenient to use. The next step in bootstrapping a self-sustainable system involves implementing temporary infrastructure that lets us work with state more conveniently.

## Early Computing and \ac{COLA}
Temporary infrastructure to support in-system development can be found in many developments of self-sustainable systems. A historical example is the Teletype loader for the Altair 8800. Here, the base platform was the Altair hardware with its memory and native CPU instructions. The only way to modify state through the platform was through the use of hardware switches at the front of the computer (Figure\ \ref{fig:altair}), which could be used to read and set values in a given range of memory.

Programming _in-system_ looked like the tedious setting of switches to poke numerical instructions to memory. To make entering programs easier, the recommended first step when using the Altair 8800 was to manually input instructions for a *boot loader* that communicated over the serial port. When finished, this could be run to load instructions from a paper tape. From here, programmers could write instructions more conveniently using a Teletype terminal and have them loaded into the Altair memory.

In the \ac{COLA} architecture\ \parencite[Section\ 6.1 "Bootstrapping"]{COLAs}, there is a four-step process, the first three of which appear to be throwaway. This includes a compiler for their state model in C++. This is aptly "jettisoned without remorse" once it has been re-implemented in itself, though it is unclear how a state model can perform computation (only after this do they implement the "behavioural layer"). Regardless, this clearly echoes the bootstrapping process for programming languages (Section\ \ref{concepts-and-terminology}).

The problem with these steps is that they are hard to port to a context involving structured, graphical notation and interactive system evolution. Our task is to get the system into a state where the platform, in a sense, can be "jettisoned" in terms of our attention, even though the platform-implemented substrate will be running in the background.

## Temporary Infrastructure in BootstrapLab

On its own, our chosen platform for BootstrapLab only has one way to view parts of the state: issuing \ac{JS} commands via the developer console to poll a current value. This is almost as tedious as toggling switches on the Altair. Being able to see a live view of all of the state would be a highly useful facility early on (recall Section\ \ref{web-pages-web-apps-and-browsers} in which we praised the browser's Element Inspector). In this case, Force\ \ref{avoid-bp} won relative to Force\ \ref{escape-plaf}; we capture this as Heuristic\ \ref{plaf-ed}. We implemented a tree view in the substrate based on an existing \ac{JS} library. State editing can continue to be done via the console (see Figure\ \ref{fig:three-cols}).

\begin{heuristic}[Platform editor]
\label{plaf-ed}
As soon as possible, use the platform to implement a temporary state viewer and/or editor. This temporary infrastructure will later be discarded, but given a capable enough platform, it is very easy to implement. For this reason, it is valuable for simplifying further in-system development. Again, Force\ \ref{avoid-bp} outweighs Force\ \ref{escape-plaf}.
\end{heuristic}

The \ac{JS} tree view is a complex set of functionality set to work and display in one specific way, and all control over this resides at the substrate level. The infrastructure cannot be modified from within the system. Therefore, we regard this situation as *temporary*. It is a ladder that we climb to end up in a state where we can implement a (better) state editor in-system. Once a suitable in-system editor exists, we can pull up the ladder (or if you like, "jettison it without remorse").

\begin{figure}
\centering
\includegraphics[width=\linewidth]{three-columns.png}
\caption[BootstrapLab interface]{The full BootstrapLab interface. From the left: graphics window, temporary HTML state viewer, and browser developer tools.}
\label{fig:three-cols}
\end{figure}

At this point of the bootstrapping process, BootstrapLab's interface consists of three sections (Figure\ \ref{fig:three-cols}). On the right, we have the browser console, inherited through from the platform's interface. In the middle, we have the output of the platform's main graphics technology, the \ac{DOM}, displaying the temporary state viewer. Because we have not chosen to expose \ac{DOM} control from within the system, the system only affects this area indirectly through ordinary state changes. Finally, on the left, we have the THREE.js-backed graphics window, where we will later build a state editor whose behaviour (including appearance) *will* be controlled from within the system.

Ideally, we would have actually supported interactive state *editing* in the temporary infrastructure, not just viewing. In our case, however, we tolerated state editing through console commands until implementing a state editor in terms of the left-hand graphics window (see Section\ \ref{pay-off-outstanding-substrate-debt}).

Another example of temporary infrastructure is zoom-and-pan in the graphics window. Working within a small box is very restrictive if we want to use it for viewing and editing the entirety of the system state. The finite region can be opened up into an infinite workspace by adding the ability to pan and zoom the camera with the mouse. This was important to have early on for BootstrapLab, so once again Force\ \ref{avoid-bp} overrode Force\ \ref{escape-plaf} and we implemented this in \ac{JS}.

*What can be changed at the user level?* The basic activities of viewing or editing state should be made easier by the temporary infrastructure. For the Altair 8800, instruction entry was improved. For \ac{COLA}, the basic state model was made available in the first place. For BootstrapLab, we targeted state visibility.

# Implement a High-Level Language

> The substrate's instruction set (ASM) is cumbersome, so ensure programs can be expressed in-system via high-level constructs. Decide how to represent expressions as structured state and whether to *interpret* or *compile* them into ASM. Ideally, develop such an engine in ASM gradually and interactively. Alternatively, implement it at the platform level and later *port* it to ASM or the high-level language itself.

The temporary infrastructure created in the preceding step may be enough to allow limited development in-system. However, it does not yet provide the barely tolerable programming experience we would need in order to feel comfortable ditching the platform. For this, an additional step is needed.

To make programming in-system pleasant enough, we need a high-level programming language that executes on top of the system substrate. This means that programs and all their necessary runtime state will be stored in the system state and the execution will be done either by a compiler to the substrate's instruction set or an interpreter.

## Shortcuts for Low-Level Substrates
For a programming system built atop a limited platform (\eg{} hardware), the temporary infrastructure may be the best tool that is available for programming. In that case, we would write the compiler or interpreter directly using the instruction set. However, as long as the platform has higher capabilities or one has access to alternative platforms, this may not be optimal. When Paul Allen and Bill Gates wrote the famous BASIC programming language for the Altair 8800, they did not do this *on* the Altair, but using an Intel 8080 CPU emulator written and running on Harvard's PDP-10. The high-level language was thus developed *outside the system.*

In \ac{COLA}, it is unclear how the Lisp-like programming language is built beyond the broad outlines. What is clear is that the bootstrapping process is carried out by means of source code files written in some text editor. In other words, it wisely takes advantage of the affordances of its Unix platform, avoiding the Turing Tarpit failure mode described in Section\ \ref{the-major-design-conflict}.

## High-Level Language for BootstrapLab
If we take \ac{JS}, and strip away the concrete syntax, we get a resulting tree structure of function definitions, object literals, and imperative statements. A similar structure with similar semantics would be obtained from other dynamic languages. In fact, this would largely resemble Lisp S-expressions under Lisp semantics; hardly surprising considering Lisp's famously minimal syntax of expression trees. Furthermore, the evaluation procedure for Lisp is simple and well-established.

For these reasons, we designed a Lisp-like tree language in the substrate. This way, we provide high-level constructs (`if-else`, loops, functions, recursion, and so on) for in-system programming. Alignment (Force\ \ref{alignment}) encouraged us to revisit Lisp's design to better fit with our substrate. For example, ordinary Lisp is based on lists whose entries have *implicit* meanings based on their positions. This fits with the substrate made of S-expressions. Our substrate comes with named labels and suggests a language based around maps whose entries are explicitly *named*, so we called it *Masp.*^[This is not too hard to come up with, but we would like to credit the origin of the name to\ \parencite{Masp} and related discussion.] Figure\ \ref{fig:lisp} contrasts the two.

\tomas{Maybe use more verbose JSON like syntax, because figuring out the nesting rules below is not obvious. (Plus you need to have curly brackets if you want your language to take over the world, right??)}

\begin{figure}
\raggedright
Lisp:
\begin{verbatim}
(define fac
  (lambda (n)
    (if (= n 0)
      1
      (* n (fac (decr n)))))
(fac 3)
\end{verbatim}
Masp:
\begin{verbatim}
apply: define,  name: fac,  as:
  apply: lambda,  arg_names: { 1: n },  body:
    to: n,  apply:
      0: 1,  _:
        apply: *,  1: n,  2:
          apply: fac,  n:
            apply: decr,  1: n
apply: fac,  n: 3
\end{verbatim}
\caption[Lisp vs. Masp]{Lisp, built around lists, vs. Masp, built around maps.}
\label{fig:lisp}
\end{figure}

The equivalent Masp code is more verbose when rendered in ASCII. However, one of our key goals is to enable the use of other, better notations if desired, which we will discuss in the next section. Here, we start from an internal representation that has *more* information (explicit named arguments) than Lisp, but we can choose to display this however we feel appropriate (perhaps by showing a name label only for the entry being edited).

## Choosing an Appropriate Implementation

There are two basic decisions for implementing the high-level language. First, will we do it directly in-system using the instruction set, or using richer capabilities provided by the platform? Second, the language can be either interpreted or compiled. The four combinations have different properties.

A *platform interpreter* is the easiest one to implement, but it cannot be used to easily bootstrap itself. To "jettison" the platform implementation, we later need to *port* the interpreter to the ASM language. (Porting it to the high-level language would not suffice since we would still need the platform interpreter to actually run it.)

A *platform compiler*, while harder to implement, is slightly easier to jettison because it only needs to be ported to the newly developed high-level language. The platform compiler can translate it to ASM, which we can already run in-system. This compiler can then turn any high-level expressions into ASM, including its own source expressions!

Yet harder to implement is an *in-system interpreter*, directly in ASM, but it will exist in-system. However, the interpreter will be less maintainable than if it were written in a high-level language and will likely need to be ported to a high-level language eventually.

Finally, an *in-system compiler* is the most challenging to implement. It will allow the language to exist in-system sooner and possibly more efficiently but, as above, will likely need to be converted to a high-level programming language to allow in-system improvements.

When implementing the interpreter or compiler in-system, all its intermediate state will also be stored in-system. However, in-system state can be used even when implementing the interpreter or compiler *on the platform.* This takes advantage of the platform's high-level language while leveraging the product system for debugging and visualisation, simplifying a later port to in-system implementation (see Heuristic\ \ref{in-state-op}). The transition from platform to in-system implementation can be even more gradual; once the intermediate state is stored in-system, it becomes possible to port *parts* of the interpreter piecemeal to in-system instructions, invoking them from the remaining parts running outside.

\begin{heuristic}[In-state operation]
\label{in-state-op}
Store high-level-language processing state in-system even if the processor runs on the platform. This will ease porting the processor to in-system implementation and support a gradual transition.
\end{heuristic}

## Implementing Masp for BootstrapLab

In BootstrapLab, Force\ \ref{escape-plaf} encouraged us to get executing Masp expressions early to get experience with the language. We choose to implement a *platform interpreter* for Masp using \ac{JS} as this was the easiest way to achieve that.

A naïve approach would simply implement the standard Lisp interpreter routines (`eval` and `apply`) as recursive \ac{JS} functions. However, this would miss an opportunity for visualisation and debugging that is already present in our substrate. Instead, we followed Heuristic\ \ref{in-state-op} and had intermediate interpreter state reside in-system. This made a later in-system port easier by doing half of the work now.

Lisp evaluation is done by walking over the expression tree. At any point, we are looking at a subtree and will evaluate it until reaching a primitive value. Ordinarily, the "current subexpression" is an argument to `eval` at the top of the stack, where the stack records our path from the original top-level expression. Since we already had a tree visualisation, we used that instead of a stack. We did, however, need to maintain references to parent tree nodes (see Appendix\ section\ \ref{graphs-vs.-trees}) in order to backtrack towards the next unevaluated subexpression once the current one is evaluated. Furthermore, instead of *destructively* replacing tree nodes with their "more-evaluated" versions, we "annotate" the tree instead. This design choice follows Subtext\ \parencite{Subtext} and will make it possible to trace provenance and enable novel programming experiences. Figures\ \ref{fig:masp-1}--\ref{fig:masp-n} show some examples.

\begin{figure}
\centering
\includegraphics{masp/1-apply-fac.png}
\caption[Masp Factorial evaluation step 1]{The \texttt{expr} part of the Masp context contains the current expression being evaluated. This represents the initial state for applying the factorial function with parameter \texttt{n} bound to 1.}
\label{fig:masp-1}
\end{figure}

\begin{figure}
\centering
\includegraphics[width=6cm]{masp/2-eval-fac-n.png}
\caption[Masp Factorial evaluation step 2]{After some evaluation steps, both the original expression (the name \texttt{fac}) and its value (its function closure) are visible. Similarly, the literal expression \texttt{1} has evaluated to itself.}
\label{fig:masp-2}
\end{figure}

\begin{figure}
\centering
\includegraphics[width=6cm]{masp/3-expand-fac.png}
\caption[Masp Factorial evaluation step 3]{The next step of evaluation, read as: ``To the value \texttt{1} (which came from the expression \texttt{n}), apply this function literal in an environment where \texttt{n} is bound to \texttt{1}''.}
\label{fig:masp-3}
\end{figure}

\begin{figure}
\centering
\includegraphics[width=8cm]{masp/4-apply-mul.png}
\caption[Masp Factorial evaluation step 4]{Some steps later, we have an application of a built-in multiplication function whose JS code is visible. The second operand is an as-yet unevaluated recursive application of \texttt{fac}.}
\label{fig:masp-n}
\end{figure}

\note{Perhaps the idea of bootstrapping abstractions from the low-level (Heuristic\ \ref{use-asm}) has been refuted? Instead of writing BL-ASM in-system to build things up, it's been so tedious that I haven't touched it and instead leaped to Masp in JavaScript! It is of course still possible in principle, but the complex reality of how I did things and the design decisions I made, caused it to be uneconomical.}

*What can be changed at the user level?* Depending on which of the four implementation paths were chosen, the semantics of the language may or may not (yet) be modifiable from the user level. The user is almost able to use the high-level language in-system for convenient programming \ldots{} but may be unable to enter the expressions conveniently in the first place. This matter will be addressed shortly. 

# Pay Off Outstanding Substrate Debt

> Port all remaining temporary infrastructure into the system, taking advantage of the infrastructure itself and the high-level language. The result is a *self-sustainable* programming system.

If we had developed both the state editor and the high-level programming language in-system, we would already have an elementary self-sustainable system at this point. This would have been our only option if we had been somehow stuck with only a primitive platform, as was the case at the dawn of computing in the 1940s. With a richer platform available, one can choose to implement a state viewer, editor and high-level programming language on it following Heuristic\ \ref{plaf-ed}. Since these will now run outside of the product system, they will be functionally part of the substrate---yet they do not belong there. This *substrate debt*, incurred due to Force\ \ref{escape-plaf}, now needs to be paid off.

## Substrate Debt in BootstrapLab
In the ideal development journey, we would have a high-level programming language and a basic state editor in-system by now. This did not happen for BootstrapLab.

The Masp interpreter we developed used in-system state, but controlled it from \ac{JS}. Our state viewer was also fully implemented in \ac{JS}. Editing took place through the browser development console. The alternative, creating a Masp interpreter and state editor in-system using the low-level ASM instructions, had been technically possible but prohibitively tedious. The in-system tooling was far from supplanting the existing platform interface of \ac{JS} in the text editor. Continuing to use the latter was, therefore, the only sensible choice to make progress.

Nevertheless, to make the high-level language and editor a part of self-sustainable programming system, they ultimately need to be implemented in-system. Thus we incurred a *substrate debt* due to Force\ \ref{escape-plaf} which we now need to pay off. The advantage of delaying this work is that we can at least port \ac{JS} to Masp, which is more convenient than using ASM. Generally, such substrate debt should be paid off as soon as the indebted implementation is complete. In total, we had three parts of it to pay off:

* The temporary state viewer, to be superseded by an in-system editor
* Its replacement state editor, to be ported from JavaScript to Masp
* The Masp interpreter, to be ported from JavaScript to ASM

In BootstrapLab, we took a two-step approach to supplanting the temporary state viewer. We first replaced a viewer that exists fully outside of the system with an editor that uses in-system state and graphics, but is controlled from \ac{JS}. We then started to port the editor code from the platform to in-system Masp, which is where we are at the time of writing.

## Supplanting the Temporary State Viewer
Once we could run Masp programs in the substrate, we needed a better way of entering and editing them. We desired a state editor in the graphics window to make the existing state view obsolete. Considering the proof-of-concept nature of this work, we created a rudimentary tree editor that nevertheless surpassed the existing practice of issuing commands in the \ac{JS} console.

To edit state in \ac{JS}, we needed to either address its parent with a full path from the top level, or to use a reference previously obtained this way. To set a primitive value, we would type a \ac{JS} command including the key name and the value. This was not a high bar to clear. Evidently, we could greatly improve the experience by simply clicking on the relevant key name and typing.

\begin{figure}
\centering
\includegraphics[width=9cm]{editor.png}
\caption[In-system tree editor vs. HTML state viewer]{Left: tree editor in graphics window. Right: temporary state viewer in the DOM.}
\label{fig:editor}
\end{figure}

We implemented a basic tree view in the graphics window (Figure\ \ref{fig:editor}). Nodes can be expanded and collapsed, and entries can be changed by clicking and typing. The display is "on-demand" and breadth-first: map entries are read upon expanding a node. This means that cycles in our graph substrate do not pose a fatal problem, as they did in the temporary state view (see Appendix\ section\ \ref{graphs-vs.-trees}). The basic CRUD operations are accounted for as follows:

* Update (primitive): The `Tab` key commits the value and selects the next entry.
* Create: If the above runs past the end of the map, special "new entry" fields for entering a new key and value are created. These disappear if abandoned without committing.
* Update (composite): `Enter` commits a new, empty map and selects the "new entry" field within it.
* Delete: `Backspace` on an empty value will delete the entry. If it was the only entry, it will be replaced with the "new entry" field.
* Read: The display of the entry in the graphics window provides this.

It is worth noting that Alignment (Force\ \ref{alignment}) applies here too: the structure of the substrate clearly has implications for the structure of the editing interface. If our substrate consisted of low-level bytes, the traditional hex editor interface would be an immediate requirement. Such an interface could plausibly be simpler to implement than the complex nested tree editing we needed for BootstrapLab. This suggests a potential feedback into the choice of substrate: *a more complex substrate will require a more complex editor.*

We might even be tempted to conclude that it only makes sense to use a low-level substrate, since we can complete a basic editor sooner and subsequently work in-system. This neglects the fact, however, that the higher-level structures of our substrate would inevitably be required at some point. Thus we would have to do the same work anyway, but only once we had suffered through the human-unfriendly low-level substrate.

It is also remarkable that, in this restricted interaction domain, we finally *did* manage to surpass our default \ac{JS} text editor. There is a cost to typing out concrete syntax like `:` and `{}` for \ac{JS} map structures, as well as ensuring indentation is correct. For entering state structures, we found the structured editing style to be quicker. As a result, where previously we might have added new persistent state in our \ac{JS} startup code, we now directly entered it into the system and persisted it manually.

There is a caveat to all this. The whole exercise was in the service of paying our substrate debt from earlier---pulling up the state viewer "ladder" that had got us to this point. Ideally, we would have built up its replacement in-system. Yet as pointed out, \ac{JS} was still the most appealing way to program at this stage, so we used it for this editor as well. In other words, we took on a new debt in order to pay off the first one! To resolve it, we would port the \ac{JS} to Masp---a process which is underway at the time of writing for both the Masp interpreter and the state editor.

In general, at the end of this stage the substrate should not contain anything that we wanted to be modifiable in-system. Thus:

*What can be changed at the user level?* The structural "syntax" and semantics of the high-level language can be changed. The graphical interface of the system can also be changed, including the concrete notation for programs and data, which we turn to next.

# Provide for Domain-Specific Notations

> Use the self-sustaining state editor to construct a more convenient interface for editing high-level expressions. Add *novel notations and interfaces* as needed. Use these not just for programming new end-user applications, but also to improve the product system itself.

Because BootstrapLab is currently in the middle of the previous stage, this section describes our plans for when this is complete. At such a point in the journey, the editor implementation would now be part of the product system, so we could modify it from within to our heart's content.

We admitted earlier how, in BootstrapLab, we had not managed to bring the system interface up to a level where it became more effective than \ac{JS}. With the implementation of a state editor, we came closer. Indeed, for entering general state structures, it is not obvious how to improve on it. Yet when it comes specifically to Masp expression structures, we must enter their verbose details even though they are highly regular and could be captured through fewer interactions. If we streamline this *subdomain* of the BootstrapLab interface, it would make Masp programming just as convenient as typing \ac{JS}, if not more so---and we could finally escape the text editor entirely.

## A Taster
First, we propose a restricted proof-of-concept of notational variation from within the system. We choose to target a small part of the problem: the Masp `apply` node, a frequent enough occurence that a small improvement will be helpful.

In the general state editor, one must type each of these key-value pairs for a function application:

```
apply: setColor
red: 11
green: 22
blue: 33
```

Instead, we desire something like autocomplete for parameters. Instead of typing `apply`, we press `a` and enter `setColor`. Subsequent tabbing should fill in the parameter names automatically and let us type the arguments. Furthermore, as a small notational difference we will omit the word `apply`:

```
setColor
red: _
green: _
blue: _
```

The underscores represent unfilled fields right after this structure gets created.

To reprogram the editor to work like this, we would do the following from within the editor. Navigate to the Masp code structures for the editor that synthesise the graphics structures to display a given state node. Enter Masp code that checks for the key `apply` in the given node and, if present, only renders the value of the key instead of the key itself.^[Admittedly, this will display all structures with an `apply` key this way, but further discretion is just as achievable with further programming. The point is that this can be changed at the user level.] Then, navigate to the code that handles key input. Add code that, when `a` is pressed, will insert a new map containing the `apply` key, render this to the graphics, and send text input to its value text box. Finally, navigate to the code that commits an entry on a `Tab` keypress. Change this to detect if it is for an `apply` key and, if so, to look up the symbol in the value node and treat it as a Masp function closure. For each entry in the `arg_names` field, add an entry to the map with a dummy value, render this to graphics, and then proceed with the default behaviour (highlight the next entry in the map). Depending on the precise implementation, it may be the case that only subsequent edits will be rendered this way. Otherwise, care may be necessary to refresh and re-render the entire editor state.

## A More Ambitious Novel Interface
The above "taster" is a simple example of an interface that could be plausibly implemented early in BootstrapLab's self-sustaining lifetime. Beyond this, it points to a more general class of extensions which would support *projectional editing.* Projectional editors are a class of programming interfaces that provide domain-specific interfaces for certain program subexpressions, such as \LaTeX-style mathematical expressions to replace ASCII renderings (recall Section\ \ref{linguistic-freedom}). We would do well to import such ideas into BootstrapLab. We proceed to sketch how such an interface would be added to the system, and how its ramifications are different from ordinary non-self-sustainable projectional editors.

As an example, suppose we want to program some fancy graphics. Fancy graphics require sophisticated vector mathematical formulae. In textual programming languages, these are expressed as ASCII with limited infix notation. The Gezira/Nile project\ \parencite{Gezira,Nile} attempted to improve on this with Unicode mathematical syntax. Obviously the extreme endpoint would be \LaTeX. All we have at the moment is something worse than all of these: verbose, explicit tree views spanning multiple lines.

We think ahead with a view towards making the fancy graphics programming more pleasant. Suppose we decide that we would ideally like to implement them with the aid of concise mathematical notation, as opposed to our current state of verbose trees. How can we achieve this?

The broad approach would be similar to our previous taster example. We would have to start, again, at the code that renders state into graphics. Add a condition that checks for a `math` key, which we would use as a tag to hint at this display preference. Enter code to translate operator names to Unicode symbols, place them at infix positions, place parentheses appropriately, and render the whole thing to a single line in the tree view (ideally keeping the tree structure of the expressions in the graphics state). Then, modify the input handling and tree navigation code to appropriately work on this *inline* tree structure. And so on.

The above points are, of course, a high-level sketch, but it is *programming* all the same and is plausible to achieve with a high-level language. Techniques from the literature would be helpful, such as Hazel's calculus for editing structures with holes\ \parencite{Hazel}, or bi-directional synchronisation between the rendered graphics and the state's ground truth\ \parencite{SnS}.

## Real Example: Colour Preview
While the above speculation has value, it is only fair that we show a real example. We only had time to implement a very modest proof-of-concept: instead of a hex string for colours, let us see a rectangle with that colour instead.

Some Masp code (Figure\ \ref{lst:rendermapentry}) lives under the global register called `render_map_entry`. It is invoked from the \ac{JS} function for rendering map entries in the tree editor. It checks if a map entry is named "color", and if so, returns an appropriately coloured box with a grey border. Figure\ \ref{fig:hex-vs-boxes} shows the difference.

These results so far could have been achieved without going to the trouble of implementing the hook in Masp. \ac{JS} would have sufficed. However, having this code in Masp lets us do something not possible with the equivalent \ac{JS}. The system has access to the explicitly structured Masp code and can choose how to display it.

As this Masp code is evaluated on its own source tree, it encounters the hex constant `0xaaaaaa` representing the grey border and displays this *with the very notation it implements.* See Figure\ \ref{fig:grey-box} for the difference. This is a minimal demonstration of Innovation Feedback (Section\ \ref{the-key-benefit-innovation-feedback}): there is no artificial barrier to innovations (in this case, displaying colour previews) applying to their own implementation code.

\begin{figure}
\begin{lstlisting}
to: key_name,  apply:
  _: { apply: quote,  to: unhandled }
  color:
    apply: block
    1:
      apply: local,  name: box,  is:
        width: 0.45,  height: 0.2
        center: { right: 0.875,  up: -0.1,  forward: -0.9 }
        children:
          1:
            width: 0.5,  height: 0.25
            center: { right: 0,  up: 0,  forward: -1 }
            color: 0xaaaaaa
    2: { apply: set,  map: box,  key: color,  to: value }
    3: box
\end{lstlisting}
\caption[Masp code for local colour preview]{This Masp code checks if a map entry is named ``color''. If so, it returns an appropriately coloured box with a grey border. Otherwise, it returns the string \texttt{unhandled}.}
\label{lst:rendermapentry}
\end{figure}

\begin{figure}
\centering
\includegraphics[width=8cm]{hex-strings-vs-coloured-boxes.png}
\caption[Local colour preview in BootstrapLab]{Before (left) and after (right) activating the Masp rendering hook.}
\label{fig:hex-vs-boxes}
\end{figure}

\begin{figure}
\centering
\includegraphics[width=12cm]{grey-box.png}
\caption[Innovation Feedback in BootstrapLab]{The grey colour constant displays as the hex string \texttt{0xaaaaaa} in the right-hand HTML tree view. However, in the left-hand tree editor, this code runs on its own source representation, turning the colour constant into a grey box instead. This is a minimal example of Innovation Feedback.}
\label{fig:grey-box}
\end{figure}

## The Key Takeaway
In the non-self-sustainable world, a projectional editor is implemented in some traditional programming language and interface; say, Java. The domain-specific notations can benefit a wide variety of programs created using the editor. Yet, this range of beneficiaries nevertheless forms a "light cone" emanating out from the editor, never including the editor itself. For example, any vector formulae used to render the interface of the editor will remain as verbose Java expressions, along with any code for new additions to the editor. The tragedy of non-self-sustainable programming is that it can never benefit from its own innovations.

Conversely, in BootstrapLab, the benefits of the new notation spread across the whole system; the "light cone" *includes* the editor implementation itself. If we previously had to squint and parse verbose maths trees in the implementation of the maths rendering, we can now open up the code again and see it rendered in the more readable way that it itself implements!

In \ac{COLA}, notational variation appears to be limited to variation in concrete syntax. Our uncompromising insistence on *explicit, non-parsed structure* at the core of BootstrapLab, while costly in terms of interface implementation, was precisely in order to be free of such a restriction in the end. While one *could* implement a multiline text field with syntax highlighting in BootstrapLab, it is at least crystal-clear that a vast array of other interfaces are possible, unimpeded by any privileging of text strings.

# Situation, Task, User, Importance
We close this chapter with a description of BootstrapLab expressed in the framework of\ \cite{EvUISR}. It is worth separately applying this to (a) BootstrapLab itself, (b) the technique we have presented as a sequence of steps, and (c) the "ideal BootstrapLab" that we would develop with more time and resources.

Firstly, BootstrapLab itself is made to help the author (User) discover how to interactively achieve self-sustainability and explore its effects (Task) for research (Situation). The claim to Importance is that such a system did not previously exist (Problem Not Previously Solved). 

The technique was developed to help individual programmers (User) escape the limitations of their go-to programming environments for general programming tasks (Task) where they are able and willing to put in this investment (Situation). Such an investment of time and work may not be possible or appropriate in some situations. However, we see it as existing on the same continuum of existing programming investments, such as writing a utility function or library to ultimately pay itself off in productivity gains. This contribution falls under "Generality", applying to all sorts of programming systems taken as the platform. It also qualifies for "Empowering New Participants" in the sense that the benefits of the Three Properties (especially Self-Sustainability) need no longer be confined to specific programming systems like Smalltalk; one should be able to have them "bolted on" to one's own preferred platform.

Finally, the "ideal BootstrapLab" would be an example of this process applied to our preferred platform, the Web browser (User, Situation). It would function as a Smalltalk-like "personal dynamic medium" for both exploring problem spaces and implementing solutions (Task), but one that neatly slots into our familiar programming practices (\eg{} does not require installing and learning Smalltalk). While it is necessarily lifted up by programmers, the availability of mood-specific notations could make it of use to non-programmers (Empowering New Participants).

While the "Situation, Task, User, Importance" structure is helpful for summarising what we have done, it is quite broad and does not include the Three Properties that we intended BootstrapLab to embody. Our remaining task in this dissertation is to examine this issue of evaluating BootstrapLab and programming systems more generally.
