\hypertarget{bl-steps}{%
\chapter{BootstrapLab Journey Summary}\label{bl-steps}}

Here, we provide a succinct reference for the major journey steps in Chapter\ \ref{ch-bl}, along with the requirements, forces and heuristics that we encountered in our experience.

\paragraph{Choose A Starting Platform.} Select an existing programming system on which to build.

\paragraph{Design A Substrate.} Architect the basic form of *state* and *change* in the system, as implemented in the platform.

- Requirement\ \ref{code-as-data}: *Code As Data.* The smallest units of change (instructions) must be readable and writeable as ordinary data.
- Force\ \ref{avoid-bp}: *Avoid Boilerplate.* Prevent the *Turing Tarpit* failure mode by making the substrate more advanced.
- Force\ \ref{escape-plaf}: *Escape The Platform.* Work in-system as soon as possible by tolerating a *less* advanced substrate.
- Heuristic\ \ref{min-friendly-ll}: *Minimally Human-Friendly Low Level.* Ensure that the substrate supports *string names* and *substructures* (Force\ \ref{avoid-bp}\ $>$\ Force\ \ref{escape-plaf}).
- Force\ \ref{alignment}: *Alignment.* Everything should *fit:* instructions, high-level expressions, and graphics expressions should all fit the substrate, and the substrate should fit the platform.
- Heuristic\ \ref{use-asm}: *Use Imperative Assembler.* The fetch-execute cycle is the easiest "language processor" to implement, so begin with that (Force\ \ref{escape-plaf}\ $>$\ Force\ \ref{avoid-bp}).
- Heuristic\ \ref{simple-asm}: *Simple Assembler.* Design a \acs{RISC} rather than \acs{CISC} instruction set (Force\ \ref{escape-plaf}\ $>$\ Force\ \ref{avoid-bp}). *Warning:* our extreme interpretation was counterproductive (Section\ \ref{the-minimal-random-access-instruction-set-and-its-perils}).
- Heuristic\ \ref{in-state-graphics}: *In-State Graphics.* Make graphical interfaces expressible as ordinary state in a special location (Forces\ \ref{avoid-bp} and\ \ref{alignment}).

\paragraph{Implement Temporary Infrastructure.} Use the platform to implement tools for working within the substrate, most importantly a state viewer or editor; these will be discarded once in-system versions are available.

Heuristic\ \ref{plaf-ed}: *Platform Editor.* As soon as possible, use the platform to implement a temporary state viewer and/or editor (Force\ \ref{avoid-bp}\ $>$\ Force\ \ref{escape-plaf}).

\paragraph{Implement a High-Level Language.} The substrate’s instruction set is cumbersome, so ensure programs can be expressed in-system via high-level constructs. 

Heuristic\ \ref{in-state-op}: *In-State Operation.* Store high-level-language processing *state* in-system, even if the language processor *code* remains running on the platform (Force\ \ref{escape-plaf}).

\paragraph{Pay Off Outstanding Substrate Debt.} Port all remaining temporary infrastructure into the system to arrive at a *self-sustainable* system.

\paragraph{Provide For Domain-Specific Notations.} Use the self-sustaining state editor to construct a more convenient interface for editing high-level expressions. Add
novel notations and interfaces as needed.