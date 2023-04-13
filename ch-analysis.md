\joel{ Stories (reminder)
1. Bootstrapping self-sustaining systems from a starting point
2. Fixing/freeing the COLA work by lifting the text restriction
3. Re-building the software stack with the benefit of hindsight and better tech.
}

\chapter{Analysis}

\joel{
To begin we must explain sys vs languages.
Now we can cover the COLA programming system design including the Id object model.
This introduces self-sustainability, bootstrapping, and notational freedom.
On the latter: here's why COLA's text reliance is a liability.
But why is all this so hard? / big picture: Against Conv Wisdom.
}

Now we have covered the existing background work, we will add our own interpretations and introduce novel concepts. This chapter is organised by themes because each one emerges as a reaction to the previous.

To start with, this thesis is about building "programming systems". We will define this concept and explain how it is related to that of a programming language. We will distinguish the general concepts of *state* and *change* in programming systems and cover three important paradigms: Batch Mode, Unix, and Interactive. Then, we will be able to define *self-sustainability*, *notational freedom* and *explicit structure* as properties of programming systems.

This will let us return to examine the COLA system design in terms of these. While it exhibits self-sustainability, its *notational freedom* is limited to *syntactic* freedom and relies on implicit structure. Furthermore, the way that a COLA is *bootstrapped* is in terms of batch-mode language transformations, and we explain why we see this as a related limitation.

These concerns point to an unexplored opportunity which constitutes the contribution of this thesis. We discuss how the Three Properties feed into each other and hence why we study them together. We close with a summary of what the thesis contribution is.

# Programming Systems vs Languages
Many forms of software have been developed to enable programming. The classic form consists of a *programming language*, a text editor to enter source code, and a compiler to turn it into an executable program. Instances of this form are differentiated by the syntax and semantics of the language, along with the implementation techniques in the compiler or runtime environment. Since the advent of graphical user interfaces (GUIs), programming languages can be found embedded within graphical environments that increasingly define how programmers work with the language---for instance, by directly supporting debugging or refactoring. Beyond this, the rise of GUIs also permits diverse visual forms of programming, including visual languages and GUI-based end-user programming tools.

The classic essay by Gabriel\ \cite{PLrev} distinguishes the *languages* and *systems* paradigms in programming research. The topic of this thesis, and many of the examples we will use to illustrate concepts, rely on understanding this distinction and only make sense within the systems paradigm.
Therefore we shift our attention from *programming languages* to the more general notion of "software that enables programming"---in other words, *programming systems*.

\begin{defn}[Programming System]
A \emph{programming system} is an integrated and complete set of tools sufficient for creating, modifying, and executing programs. These will include notations for structuring programs and data, facilities for running and debugging programs, and interfaces for performing all of these tasks. Facilities for testing, analysis, packaging, or version control may also be present. Notations include programming languages and interfaces include text editors, but are not limited to these.
\end{defn}

This notion covers classic programming languages together with their editors, debuggers, compilers, and other tools. Yet it is intentionally broad enough to accommodate image-based programming environments like Smalltalk, operating systems like Unix, and hypermedia authoring systems like Hypercard, in addition to various other examples we will mention in Chapter\ \ref{tech-tims}.

In Chapter\ \ref{tech-dims}, we propose a common language as an initial step towards more progressive research on programming systems. Our set of "technical dimensions" seeks to break down the holistic view of systems along various specific “axes”. The dimensions identify a range of possible design choices, characterised by two extreme points in the design space. They are not quantitative, but they allow comparison by locating systems on a common axis. We do not intend for the extreme points to represent “good” or “bad” designs; we expect any position to be a result of design trade-offs. At this early stage in the life of such a framework, we encourage agreement on descriptions of systems first in order to settle any normative judgements later. The set of dimensions can be understood as a map of the design space of programming systems (Figure 1). Past and present systems will serve as landmarks, and with enough of them, we may reveal unexplored or overlooked possibilities. So far, the field has not been able to establish a virtuous cycle of feedback; it is hard for practitioners to situate their work in the context of others’ so that subsequent work can improve on it. Our aim is to provide foundations for the study of programming systems that would allow such development.

While we do have new ideas to propose, part of our contribution is integrating a wide range of existing concepts under a common umbrella. This work is spread out across different domains, but each part connects to programming systems or focuses on a specific characteristic they may have.

\joel{
The notion of "self-sustainable system" is difficult to discuss purely at the programming language level, because it crucially depends on how program execution and production are connected. For this reason, we talk about *programming systems*\ \cite{TDs,PLrev}, which include not only programming languages, but also IDEs, programming environments with non-textual notations, and other tools for creating software.}

## Two Fundamentals: State and Change
A good general model of a programming system is like a physical system in the sense of analytical mechanics \cite{SICM}. There is always a current *state* of the system, and this will necessarily *change over time*. We stress that this is the case regardless of whether the underlying programming metaphor is imperative, purely functional, logic-based, or otherwise eschews a notion of "state" in its conceptual model. This is perhaps vacuously true in the following way. In working with a declarative or functional programming system, the expression you are currently editing or the output you are seeing at a given moment is, by definition, a single state, and this changes whether you interact or simply wait for progress. In other words, anything to which this view is not applicable will not be interactive or interesting.

We include both the visible interface and the "hidden" internal state of the system (e.g. heap data structures) in this model. Such an all-encompassing "state", of course, is not comprehensible atomically but is always broken down into substructures (whether byte lists, object graphs, trees or otherwise). Likewise, the actual *change* from one state to another usually does not involve all of the state but only a small part of it. In the limit, there is usually some smallest unit of state (a byte, dictionary entry, tree node) and this gives rise naturally to primitive *instructions* describing a change to such a small unit. Different choices for how to represent the instructions have implications for where it is possible to take the evolution of a system. We will see in Chapter\ \ref{ch-bl} that some choices are more appropriate than others for ensuring a system can be made self-sustainable.

## The Batch-Mode Paradigm
Computer programs originated as “batch-mode” processes. A calculation chugs away and delivers a result at the end. A compiler combs through source code and outputs a machine-level program. In the batch-mode paradigm, a program starts, runs, and then stops. The effect or “behaviour” of a program is its *output;* to change its behaviour, we need to change the executable program. But we cannot change the program binary directly; most nontrivial changes would invalidate various binary offsets, which would then have to be discovered and adjusted. Instead, we change its *source code*, and then *re-generate* the program as another batch-mode operation. However, this is not a problem because any important effects of the program are outside it, whether on a paper printout or saved to magnetic tape. Any data structures the program creates occur within its “working memory”, a temporary scratchpad internal to the program and discarded when it’s done its job.

Thus, the key assumptions of batch-mode programming are:

1. The point of a program is to output a result (i.e. the answer to a question.)
2. Compute and storage (fast and slow) are scarce resources that we can only afford for essential tasks.
3. Because of this, as well as the task itself, it typically takes a long time to create the result.
4. There are only one or two data items we care about long-term (e.g. the result). Any intermediate steps it takes to create the result are unimportant or uninteresting, so they should be discarded to free resources. This is a surprisingly consequential assumption; we will refer to it as *delete-by-default.*

The three key consequences of these assumptions are:

1. We implicitly design programs to run in fast (volatile) storage to ensure performance and that the uninteresting intermediate state is not wastefully persisted.
2. For the one or two exceptional data items that we do care about (e.g. the output), we have to remember to write code to move these out of volatile memory and into non-volatile storage. This is not tricky, because there are only a few items we care about!
3. The time during which the program is running is an *obstacle* to us getting the result; a better program is one that terminates sooner.

Note how the stages of *compile-time* and *run-time* are of comparable importance in this paradigm. Properties that are *static*, or *early-bound*, are invariant over the entire run-time of a program and are "baked in" at compile-time. Those that are *dynamic* or *late-bound* may vary over the run-time. Since a program has a specific answer to work out (Assumption 1), then the only properties that *need* to be dynamic are those that directly pertain to such a process. All other properties are candidates for *static commitment:* the compiler (suitably informed, e.g. by type annotations) can assume they will never change, and hence can avoid generating code to deal with the consequences of such changes.

Beyond optimising the program's performance, this also gives programmers an opportunity to make mathematical guarantees about certain properties. This all follows from Consequence 3: a program's destiny is to terminate with an answer, and a better program terminates sooner. If requirements change, and some formerly static properties now need to be dynamic, there is no problem: the program is simply re-compiled, ready to run under the new conditions.

The batch-mode paradigm can be considered an appropriate adaptation to the early conditions of computing: specialised, industrial-scale use cases and extreme resource scarcity (in terms of storage and compute.) Its key contributions to the world are:

1. The *volatility split:* at all times, a programmer must remain aware of, and specify, whether their data is to be stored in volatile or non-volatile storage. An example is the question "Does this belong in a class, or in the database?".
2. The *change by re-creation* model: in order to change something, trace its history to the process that generated it, change the source input, and then re-generate everything from that point onwards. For example, a single-character typo in one's \LaTeX-typeset thesis must be fixed by re-running \LaTeX\ and re-typesetting the entire document.
3. Encouragement of *static commitment:* many innovations in programming take the form of improved ways to enforce static properties over a program's runtime, such as type system features.

## The Unix Paradigm
Let us fast-forward to the era of Unix, a family of Operating Systems dating from the 1970s. For this dissertation, we are not interested in the differences between different Unix versions or descendants. Instead, we refer to the common set of concepts and conventions that form ordinary programming practice today, most importantly *files* and *processes.*

Here we have a *continuously* running master-program (the *kernel*) which allocates compute and storage to batch-mode programs under its supervision. Unix calls these *processes;* each one a sort of virtual processor plus working memory, which it calls "core". As before, core is just a means  for the process to do its job quickly, and is discarded upon termination. The other world, of "things that matter", is a hierarchical tree of *files* shared between processes and persisted as they come and go.

Unix was created before the rise of GUIs and naturally preserved the batch-mode norm from its surroundings. Still, a different “interactive” paradigm had been around in embryo for a while. Users can access the current state of processes and files via the command line *terminal* or *shell*. This runs as a process but is, like the kernel, continuously running, passing input and output between the user and the kernel.

There is one further small innovation worth noting. As an Operating System, Unix goes some of the way towards hiding the Volatility Split by *paging* core to disk, prioritising fast storage for processes that need it (the others are paused waiting for some event.) However, Unix still sees processes as temporary scaffolding to be discarded when complete (*delete-by-default.*) What this means is that even though Unix is clearly capable of persisting a process' data while it is still active, it will still be discarded when the process completes.

In any case, we see that the Volatility Split, Change By Re-creation, and Static Commitment were preserved from the Batch-Mode era. Let us summarise all this as the *Unix Paradigm:* a compromise between the batch-mode and interactive paradigms where limited interaction is used to organise batch-mode computation.

### Unix as a Programming System
How do we analyse Unix as a programming system? Unix is one big system managing many smaller processes. To the extent that *other* programming systems are implemented as Unix processes, Unix functions as a meta-system supporting subordinate programming systems. Thus there are always *two* levels to programming in the Unix Paradigm: the "large" scope and the "small" scope. In both of these, *state* consists of both core and files thanks to the Volatility Split. However, the two levels emphasise these to different degrees.

At the large scope, it seems appropriate to describe the filesystem as the primary "state" of the Unix system. There is, of course, nontrivial state in core such as the currently running processes and bookkeeping information for them, which will be lost if the system is restarted. Nevertheless, from its own perspective, the "important data" for users all lives in the filesystem; what happens to be running at any given time in core is merely instrumental.^[C.f. Consequence\ 2: if a data structure was important, the programmer would have written code to save it to a file!] The agent of *change* at the large scope is the kernel, and the primitive "instructions" are the system calls used to write to files and change the file tree.

Meanwhile, at the small scope of an individual programming system *process*, the manifestation of state and change will depend on the particular system itself. Yet because we know it is running as a Unix process, we can at least be assured that the lowest level of "state" will be split between core and files, and "change" will occur through the execution of machine instructions.

## The Interactive Paradigm
We define the *interactive paradigm* as the programming model that emerges from a feeling of compute/storage abundance. Such abundance frees us from having to start with the question "what purposes can computers currently cope with?" and instead ask "what are computers for?" with the answer being roughly equivalent to "anything". This paradigm relaxes or rejects the basic axioms of batch-mode programming:

1. The point of a program is to simulate a piece of the world somehow useful to a human, free of the constraints of physical media substances. Producing an output is but one of many such effects useful to humans.
2. Compute and storage are (or will be^[This was the attitude at Xerox PARC summed up in the principle "design for the hardware of tomorrow".]) sufficiently abundant that we can use them generously in service of higher goals.
3. Therefore, we can expect batch-mode tasks to complete instantaneously to the human eye. For those that still take time, it is important to continue the rest of the simulation and keep it responsive.
4. We do not know *a priori* and in full generality which data items we care about long-term, because the space of human purposes is large. For some tasks, the journey is more important than the destination.^["Maybe the real result was the data we made along the way!"]

And the consequences:

1. We cannot *commit* on principle to saving some data and discarding or hiding other data; we persist everything by default and provide means to free up resources explicitly. We reluctantly abstain from this only where necessary for performance, treating this as a temporary optimisation to relax in future. But we cannot decide for the user what they will be interested in.
2. Code can simply create and manipulate data structures without the programmer needing to keep in mind what type of storage they live in---there is no Volatility Split.
3. It ought to be possible to change the state or behaviour of the system directly, as opposed to changing some upstream specification and re-creating the system from that. This is important both for performance and for avoiding premature deletion of data. Instead of Change By Re-creation, we have "Change by Changing".
4. It is no longer the case that a good program terminates quickly. There may not even be any reason for it to terminate at all.

This paradigm is embodied in systems like Lisp (which pre-dates Unix) and Smalltalk (which does not), both for different reasons. Lisp originated as a sort of "executable mathematics and logic" for AI research; it makes sense that such a tool had little need to import "batch-mode, industrial-scale computation" as its primary concept. Smalltalk, on the other hand, deliberately rejected this convention to serve its goal of shifting computing out of the industrial capability and into the personal. In neither do we find a mandatory separation between volatile and non-volatile storage, nor between "large objects" (files and processes) and "small objects" (variables and code.) Instead we find a graph of data structures, called "expressions" in Lisp and "objects" in Smalltalk.

From this perspective, the Volatility Split, carefully preserved by Unix from the dawn of computing, looks like an *implementation detail* being obnoxiously raised to the level of major design concern. The question of whether the variable X should live as dancing electrons or as magnetic domains resembles the question of at which precise address a new string buffer should live in memory. So-called "manual" memory allocation routines like `malloc()` actually remove the need for the programmer to "manually" decide such an implementation detail.^[The "manual" term refers to the management of allocation *lifetime*, in contrast to *garbage collection* which automatically decides when to free memory.]

Meanwhile, the Change By Re-creation model seems like an unnecessarily convoluted path to do a simple thing. Suppose we want the field `foo` of object `greeter` to change to `"Hello"`. The system knows the object referred to by the name `greeter` and knows how the field `foo` is stored. It seems odd to have to dig through code and re-generate the object along with everything it touched. Surely we ought be able to just type `greeter.foo = "Hello"`. Bret Victor dubbed this "destroy-the-world programming" \cite{BretVictor}. Basman et al. \cite{Externalize} rescue the concept by neutralising the cost (data loss, due to Consequence 1) to make this an acceptable way of *implementing* changes, which they call "Queen of Sheba Adaptation". The important thing is the user experience, where one can just type `greeter.foo = "Hello"` regardless of what happens under the hood.

Finally, *static commitment* now plays a much less helpful role, even an obstructive one. If a program's value derives from its behaviour as an interactive system, it may well be running for a long time or even (ideally) forever. This intensifies any disadvantages of static commitment mechanisms like type systems. On the one hand, if requirements change and a commitment must be relaxed to vary at runtime, the user is forced to terminate the running system and re-generate it. On the other, a mandatory commitment might be clearly *premature* from the perspective of the user. A type commitment like "The name X shall only refer to an integer" may be too restrictive, too soon. Consider a game in which the class `Goblin` is a subclass of `Enemy`. In a language like C++, class relationships are statically enforced, which prevents the player from befriending Goblins later in the game (by internally changing to inherit from `Friend` instead.) There is still clearly a role for commitments and optimisations, but it is piecemeal at smaller scales *during* runtime. As the duration of the program increases, the set of properties one might wish to statically commit to shrinks correspondingly.

As examined by Gabriel\ \cite{WIB} and Kell\ \cite{Kell-OS}, Unix "won" in a way that Lisp and Smalltalk did not, firmly establishing the Unix Paradigm as ubiquitous. Where Lisp and Smalltalk exist, they are processes sitting within Unix and saving to "image" files. For implementors of novel programming systems, the tenacious Volatility Split, Change By Re-creation model and Static Commitments clash with the Interactive Paradigm natural to the enterprise.

# Self-sustainability
This is one of the properties of programming systems that we are interested in for this thesis. To a first approximation, self-sustainability involves being able to evolve and re-program a system, using itself, while it is running. It is instructive to start by examining Unix in this light.

## Self-Sustainability at the Large Scope
At the "large" scope, we have individual processes---text editors, compilers, interpreters, debuggers---which change the large-scale system state (files), such as by creating new programs. Some of these processes run *shell scripts* to coordinate this activity, this being the de-facto programming language^[Technically, the various shell *dialects* (bash, csh, etc.) form the de-facto *languages* plural, but this is not important for the point.] at the large scope. In this way, a Unix system is evolved and re-programmed using itself, while it is running. Hence, Unix (i.e. Programming-in-the-large) is self-sustainable.

The matter is complicated by the fact that a small minority of special changes require restarting the system to take effect. However, the spirit of the "while it is running" condition is that the system does not need to be *destroyed and rebuilt from scratch*. Because the "state" of the large scope is mainly files, the destructive operation here is not so much "restart" as perhaps "reset" or "reinstall".

In contrast, for a programming system that exists as a process *within* Unix, its data structures in volatile memory will be permanently lost if it is restarted, and these data structures may well be an important part of its state as a continuously running interactive programming system. Indeed, when we turn our attention to the "small" scope of the Unix Paradigm, we mostly do not see self-sustainability.

## Self-Sustainability at the Small Scope
Compiled programming languages like C++ or Java are used via several different Unix processes. These include interactive ones like text editors, and batch-mode ones like the compiler. Some compilers are *self-hosting*, meaning that they can compile their own source code into a functionally identical compiler program:

\todo{Existing Background: self-hosting, bootstrapping, refs}

1. Say we design a novel language *NovLang* that we want to use.
2. We write *NovLang* source code that will compile other *NovLang* source code into a runnable program.
3. We hand-translate this to C/C++ and build a compiler for *NovLang*.
4. We run this to compile the *NovLang* source code from step 2.
5. We obtain a runnable compiler for *NovLang*, which was written in *NovLang* and is now "self-hosting".
6. We can now discard the C/C++ code.

We say the self-hosting compiler has been *bootstrapped* into existence. From this point onwards, it is possible to change the source and thereby change the compiler (in the manner of Change by Re-creation) and hence use a newly evolved version of NovLang. Self-sustainability ought to be the analogue of this property for interactive programming systems.

However, programming systems exhibit this more rarely because they more closely resemble *interpreters* than compilers. If we apply a compiler to its own source code, we get the (functionally) same compiler. But if we apply an *interpreter* to its own source code, all we get is a much slower interpreter! The job of a compiler is to generate a new program, which could (in principle) be a *replacement* for the old compiler. The job of an interpreter is to execute the program that *would* have been generated by the compiler---but unless *that* program is itself a compiler, its effect cannot *replace* the original interpreter. Usually, interpreters are used to run ordinary programs, not compilers.

\todo{Define impl vs user level!}

It is worth noting that the "meaning" of code is different depending on whether it is run through a compiler or an interpreter. Code being compiled can be viewed as describing how data structures and instructions should be outputted to a file; the actual execution is left to a later step. On the other hand, code being interpreted describes how these same things are built up in memory, and possibly executed right away.

Furthermore, the discussion so far has taken place in the batch-mode world, where the memory used by the process is disposable and unimportant, hence Change by Re-creation does not cause too many problems. For programming systems, this is often inappropriate or at least highly inconvenient. Since interactive processes are meant to run as long as the user wishes, they contain a lot of important state in memory. Furthermore, the Volatility Split was traditionally just forwarded into the user's mental model, making no guarantees about whether work would be saved in the event of a crash and recommending the user to save regularly. While many applications and programming systems did eventually implement "auto-save", they nevertheless form a clear case where delete-by-default fails and re-creating the system risks important data loss (or, at the very least, inconvenience.)

## Key Components of Self-Sustainability
In light of these points, we can discover some key criteria of self-sustainability by considering the Web browser as a programming system. What would it take to make the Web browser self-sustainable? In the first place, the browser is not even self-hosting. So our criteria will take us through to a self-hosting state first, then consider what additionally needs to be done for self-sustainability.  

1. In order to be self-hosting, the web browser would need to be able to generate a slightly different web browser binary, from slightly different JavaScript source code.
2. However, the browser is a program compiled from C++, not JavaScript. (*Language mismatch*)
3. *Even if* we translate all the C++ to JavaScript, the browser's JS console environment is not built to support such a large, interconnected codebase. (*Scale mismatch*)
4. *Even if* we make it support a large codebase, the JavaScript source will build up data structures in memory instead of outputting machine code to an external file. (*Interpreter/compiler mismatch*)
5. Suppose we modify the source to consist of JavaScript that will *generate* a new browser program (instead of just *embodying* one). Now the system is self-hosting. *Even so,* simply fixing a typo in a menu will require an hour-long build followed by a loss of the current browser session, tabs, etc. (*Regeneration delay*, *loss of state*)
6. If we are able to modify the source code in-system, "commit" the changes to take effect, and immediately see the effects of the changes without loss of state, we finally have self-sustainability.

We see a number of hurdles to achieving a self-hosting state: there must be no language, scale or interpreter/compiler mismatch. This is followed by one or two hurdles for self-sustainability: evolving the system must not incur a disproportionate regeneration delay or a loss of important state. We will use these criteria in Chapter\ \ref{tech-dims} as "penalties" for measuring self-sustainability.

## The Key Benefit: Innovation Feedback
A system that is self-sustainable (or self-hosting) has an advantage over those that are not: we call this *innovation feedback*. Innovations programmed using the system---useful functions, notations, or tools---can benefit their *own* development as well as the rest of the system. In contrast, for a system whose internals cannot be affected by any program within it, innovations can only be exploited for the system's development by *duplicating* the work in the system's implementation level.

In the case of Unix, a text editor may be used to improve its own source code, which can then be compiled into an even better text editor. The shell interface, graphical interfaces and all tools are just *programs* which can be replaced with newly compiled improvements, all using other Unix programs. Therefore, a Unix system is not limited to improving detached *separate* distributions of programs destined for a different user, but naturally improves itself as well.

\todo{Stefan: Hard to read}
When put this way, it sounds obvious: "of course computer software is used to improve computer software!" Yet how different it is at the "small" scope: the very same text editor *on its own* cannot compile its improved replacement. A Python interpreter written in C++ may empower us to create useful tools, but to improve the interpreter itself we need to inhabit the world of C++ in which our Python tools are unavailable.

From our presentation, it may appear that self-sustainability is a peculiar special case that is naturally hard to achieve. Yet the world of software as a whole is self-sustainable, as is an individual Unix system (large-scope programming.) We see self-sustainability as a natural *default*, *prevented* by the historical accident of Unix enforcing *its* peculiar model on small-scope programming. Still, we must accept the Unix Paradigm as a given, and accomplish the tasks of this dissertation on that basis.

# Notational Freedom
A famous maxim in programming is "use the right tool for the job" \cite{RightTool}. A noble aspiration, but one that is not widely fulfilled. At the "large scope" of the Unix paradigm, there is indeed a dizzying array of programming languages specialised for different jobs. Yet there are non-trivial barriers to using multiple languages for the same project, a practice unusual enough to merit a name: "polyglot programming". Going further, the scale of the "job" stops, as might be expected, at the process level.

At the "small scope", it is rare to see multiple languages used to build a single program binary. Within the scope of a single source file, multiple languages are seen occasionally as fixed combinations: HTML, CSS and JavaScript, or Perl with its Regex syntax. However, the *arbitrary* mixing of user-selected languages in a source file is out of the question. Yet it is not unreasonable to expect that a single function, or perhaps even a single line of code, might be best expressed in a different language to the rest of the code, in a way that the language designer cannot predict. For example, a common occurrence in scientific or graphics-heavy programming is a few lines of mathematical operations; it would be convenient to have these render as familiar mathematical notation instead of a butchered ASCII approximation.

As another example, it is common to embed SQL commands somehow in the source code of another programming language. In C#, these are forced into the syntax of the host language as "embedded queries", yet a programmer may prefer to use SQL directly as part of the source. The traditional alternative to both of these was to have SQL code inside program strings, which created significant security risks; however, this is by no means intrinsic to having SQL source be what the programmer *types* or *sees* (see Section\ \ref{explicit-structure} below.)

More generally, the ideal is where a component at any scale can be expressed in a *notation* that is particularly suited to it. We prefer to consider "notations" in general, including the different syntaxes of programming languages but not limited to them. Language isn't everything---diagrams and pictures are sometimes the form in which a problem or solution is delivered (e.g. in vector mathematics). Programmers ought not to be forced to describe pictures using words.

The "language" that we consider too narrow a constraint consists of renderings of text. This is as opposed to other uses of "language" in areas such as design: "visual language" may not include text at all, but uses the "language" term for the arrangement of elements. Under this latter meaning, the "domain-specific language" idea has all the degrees of freedom it deserves. Nevertheless, in programming, the term "language" runs the risk of mentally excluding graphical possibilities. To ensure they remain, our use of the word "language" will stick to denoting renderings of text glyphs and we shall use words like "notation" or "interface" for the fully general extension.

We should also clarify that we are using the terms "notation" and "interface" interchangeably; we're not just talking about static pictures but dynamic entities on a screen. The "text editor" interface is one such example. One could use a text editor to work on the hex code `0xff00ff` representing the colour magenta. Alternatively, and perhaps more appropriately, one could work via a colour picker interface instead.

The key thing is that this property is called Notational *Freedom*, rather than something like "Optimal Notation". We recognise that different notations suit different purposes and respect the art of developing them as a separate area of expertise, which we do not claim for ourselves. The idea is to support the *subjective* productivity of the programmer, who we assume is best equipped to judge the appropriateness of notations for herself. This property is about *supporting* the usage of different notations for different contexts.

It is true that there is no such thing as a free lunch; we do not go so far as to suggest that the system should turn a natural language description into a working interface (recent advances in AI notwithstanding.) So long as the programmer is willing to do the necessary work to program a new notation, this should suffice to use it in harmony with all the others. However, the Unix paradigm and text editors impose an *additional* "tax" in terms of effort beyond this reasonable standard. In the best case, it might have some plugin architecture, while at worst it may require forking the editor's source code. Notational Freedom means the system was designed without the assumption that there will only be one notation and lacks these taxes to the extent possible.

## Key Components of Notational Freedom
We can propose three tiers of notational freedom:

1. Many syntaxes
2. Many syntaxes and notations
3. User-supplied syntaxes and notations

Consider the Web browser again; what would it take to achieve notational freedom in its JavaScript editor?

1. Again, we see that it does not yet satisfy the more modest condition of *syntactic freedom*. To achieve this, it would need to support *mood-specific languages* (see Section\ \ref{colas} below.) This means it would need some way of accepting user-supplied grammars (*Custom Grammars*) and some way of detecting which should be used for different parts of the source code (*Grammar Map.*) This latter task is made particularly troublesome because of a reliance on Implicit Structure (see Section\ \ref{explicit-structure}.)
2. *Even if* we get this syntactic freedom, we probably still have to force everything to fit in monospaced ASCII (or perhaps Unicode, as we see in the Nile/Gezira projects \cite{Nile,Gezira}.) We still do not have full *linguistic freedom,* whereby we could, for example, render mathematics with the proper layout and formatting (as this does not quite fit into a horizontal list of non-overlapping characters.) This may require a leap from specifying grammars (which describe legal symbol sequences) to interfaces (how input causes shapes to appear, and how these are rendered.) Call these *Custom Interfaces* and *Interface Map.*
3. Once we have such infrastructure in place, it is not so small a step to generalise these interfaces to things that need not resemble a language (e.g. a colour picker.) This gives us full *notational freedom.*

# Explicit Structure
The problem with "plain text" is that it fuses together two independent concepts: what we could call *presentation* (how one reads and modifies the data) and *representation* (how the data is stored in memory or on disk). For example, the AST of a program *could* be stored in its tree form and *presented* as indented text. But what we do instead is serialise the text, store that, and parse it back before we are able to work with it.

This thesis approaches the default text-centrism of programming with skepticism. We direct the reader to the fuller arguments from the authors of Subtext \cite{Subtext} and Infra \cite{Infra}, but we will summarise the most important points here and offer some of our own. 

## Language can be stored differently to a character list
Language always contains *structure*: English paragraphs contain sentences, containing clauses, containing words. Natural language, like English, is typically entered into a digital medium only to be poured out again at some other end, like photo uploads; normally, the computer does not need to dive into the structure at all. On the other hand, for programming languages, the AST is the entire point.

Despite this, programming language source code is universally stored as a sequential recording of keystrokes, rather than as the tree or graph that it represents. This has the downside that every program that consumes or transforms the code must recover (parse) this structure out, discovering any mistakes only at this point of consumption (since these were just recorded with the other characters.) This is like storing vector graphics diagrams as arrays of pixels and using Computer Vision to haphazardly recognise shapes and lines: unnecessary work to recover information that was thrown away at creation time. \todo{Stefan: bad analogy}

\todo{Stefan: this is naive}
More unfortunate work results from having to "escape" characters that have been reserved to denote structure instead of their literal selves. In the worst case, the storage of language as character lists is largely responsible for the class of attacks known as SQL injection. This would not be possible with SQL commands represented as trees containing holes to be filled with user-submitted strings.

## Digital "plain text" is not inherently human-readable
This argument is made best by Hall\ \cite{Infra}, p.\ 14:

> The critical observation is that software infrastructure is heavily involved in supporting the human-readability of text. It is not the case that the bit sequences of UTF8 or any other text encodings are somehow intrinsically understandable to a human. An application interprets the bytes as character codes as per a known standard, which are mapped to glyphs in a font, and rendered to a grid of pixels. This chain of interpretation and transformation starts with clusters of electrons and ends with clusters of photons before the human nervous system takes over. The point being that there is still a necessary software layer performing a transformation in the middle.

> electrons $\rightarrow$ bits $\rightarrow$ charactercodes $\rightarrow$ glyphs $\rightarrow$ pixels $\rightarrow$ photons

> “Human readability” just colloquially implies that it is a standard encoding understood by most text editors. The sense of inherent readability merely comes from the ready availability of tools that render ASCII and Unicode. One can assume that a text editor or some text rendering infrastructure exists in the target system. Therefore, any encoding could technically achieve the same ‘human readable’ status as ASCII if it and its editors were general-purpose enough to warrant an equally ubiquitous install base. Thus, there is an opportunity to expand or upgrade the realm of what can be considered human readable.

## We Study the Spherical Cow
Text usually functions as a *medium* or rendering of something that is not inherently text. In the first place, text was invented to record speech made by human beings. In programming, text is used as a *proxy* for a nested tree-like structure, but is *not the structure itself.* Therefore, to study a programming idea like self-sustainability, it is unfortunate to have the contingencies of text representation "getting in the way" of studying the idea itself. Therefore, even though a real-world programming system may use text, we wish to avoid this obscuring layer for much the same reason as a physicist studies a frictionless sphere in a vacuum instead of, say, a cow in a field. We want to not be distracted with air resistance and complex shapes, so as to focus on the property we are interested in; future work can then add the practical complexities back in again for a more realistic model.

## Key Components of Explicit Structure
Given all this, how can we detect explicit structure in a programming system? Consider once again the Web browser. It has two major subdivisions of state: the DOM tree and the JavaScript object graph. For change, it only has those special JavaScript objects known as functions, worked on via the console and the source code view. We can examine these three areas separately:

\paragraph{DOM Tree.} The DOM tree is accessed manually through the Element Inspector and programmatically through JavaScript APIs, but is in both cases largely explicitly structured. The main exception involves the APIs taking HTML strings to create elements, and the initial stage of loading the HTML and JS source files. But once these structures have been recovered and built, much can take place without the intermediary of strings.

The other important exception to this occurs in SVG structures. SVG uses element *attributes* for composite data like matrix transformations, and hence has to store these as strings. For example, a node might have a `transform` of `translate(100,200)`. This means that, in order to move the element 10 units horizontally, the programmer must write code to extract the first co-ordinate as a number, add 10, and render the whole expression back to a string.

Related to attributes, CSS classes are a mixed case; on the one hand, the `style` attribute contains CSS source code, yet the JavaScript API does include properties for accessing this information explicitly.

\paragraph{JSOG.} The JavaScript object graph is also explicitly structured at runtime, while object definitions in the source code are obviously not.

\paragraph{JS Code.} Unlike the other two areas, JavaScript code is not explicitly structured even in the running system. There is a minimal level of structure: namely, the source file is split into Function objects (intervening lines with any side effects have already been executed and are inaccessible.) Beyond this, however, the body of each function is only accessible as a string. The obvious condition to meet here is that the AST should be navigable via some JavaScript API (*AST Access.*) Such a single level of explicit structure is shared with spreadsheets (where any structure within a cell is implicit) and databases (data within a column is implicitly structured.)

It appears that there are two realms in which explicit structure may be sought, corresponding to the Volatility Split: the contents of the source files vs. the content of the running system. Each of these could be further split between State and Change; in the Web browser, State largely has explicit structure in memory, but not on disk, and Change lacks it in both places.

\todo{Unix Files, spreadsheet, database}

# COLAs

The one system that directly influenced our work is the Combined Object Lambda Architecture or COLA\ \cite{COLAs}: a small but expressive starting system that could be self-improved into anything. The system is described as a mutually self-implementing pair of abstractions: a structural object model (the "Object" in the acronym) and a behavioural Lisp-like language (the "Lambda"). The system aims for maximal openness to modification, down to the basic semantics of object messaging and Lisp expressions.

The two key features we see in the COLA idea are *self-sustainability* and *mood-specific languages.* Self-sustainability is straightforwardly inherited from the Smalltalk tradition and amplified. It allows for innovation feedback that the authors refer to as _internal evolution_:

> Applying \[internal evolution\] locally provides scoped, domain-specific languages in which to express arbitrarily small parts of an application (these might be better called *mood-specific* languages). Implementing new syntax and semantics should be (and is) as simple as defining a new function or macro in a traditional language.

These Mood-Specific Languages (MSLs) are a continuation of the more familiar idea of Domain-Specific Languages (DSLs.) Instead of only being able to make changes to a mass of source code written in the same language, a COLA is supposed to permit easy proliferation of languages optimised for individual sub-problems. Where DSLs may limit the minimum size of a "domain" to a module or file or even a function, the MSL scope lifts this restriction. An example of such an MSL might be expressing a single line of code with mathematical notation instead of the usual monospaced ASCII subset. The language half of a COLA is designed to make it feasible for a programmer to do this sort of thing on a whim; encouraging "the right language for the job" no matter how small the context, in a way that normal language infrastructure makes too costly to be worthwhile.

This is a step in the right direction, but it is incomplete: we dream of mood-specific *notations* more generally, which we called *notational freedom* in Section\ \ref{notational-freedom}. Moreover, MSL support is presented in the paper \cite{COLAs} as a pipeline of traditional *batch-mode transformations* such as parsing, analysis, and code generation. Similarly, it presents its bootstrapping process in terms of batch-mode transformations of various source code files. This steeps it in the Unix Paradigm (Section\ \ref{the-unix-paradigm}) and feels like it obscures the interesting ideas (recall Section\ \ref{we-study-the-spherical-cow}.)

We would rather have the ability to gradually *sculpt* a system into a self-sustainable state, interactively, through a combination of manual actions and automatic code. This requires that the system should be conceived primarily through a graphical interface, yet the COLA design does not provide guidance in this respect. It is possible to see how a COLA's various languages and components work together as a sort of self-sustainable command-line REPL, but less easy to see how its text-centric approach may apply to arbitrary graphical interfaces.

We mention COLA because it was the most important influence on the work in this thesis. It gestures so tantalisingly at a way to escape the Small Scope of the Unix Paradigm, yet only while making puzzling concessions to it that seem to limit its ambition. We will refer to COLA repeatedly for comparison and offer some analysis of it on our own terms.

# The Missing Synthesis
Each of our Three Properties brings advantages to a programming system:

* Self-sustainability permits innovation feedback.
* Notational Freedom makes it easier to use the Right Tool For The Job.
* Explicit Structure avoids various pitfalls of text, both in terms of correctness and convenience.

No doubt, they also have drawbacks. Yet the advantages mean that they at least deserve *further exploration* in programming. Unfortunately, these properties are rarely exhibited. Even where they do exist, they are isolated from one another and not combined in the same system.

The Three Properties are entangled with each other from a research perspective. Self-sustainability makes it easier to add *new* notations to a system with Notational Freedom. It also makes it easier to add Notational Freedom *itself* to a system that lacks it, and lets the benefits flow into all aspects of the system's development. Yet self-sustainability is currently best understood as a vague analogy to self-hosting compilers, with even the COLA work not making it clear how such a property can be achieved in interactive, graphical systems. Notational Freedom is impossible to achieve in a world of parsed strings and text editors (that's merely *syntactic* freedom) so it needs Explicit Structure as a necessary foundation. Finally, as we remarked in Section\ \ref{we-study-the-spherical-cow}, Explicit Structure lets us study the other two properties more purely, without getting confused by the accidental complexities of parsing and escaping.

We can roughly topological-sort these dependencies as follows. Our primary goal is to explore Notational Freedom in interactive, graphical programming systems. To support this, we should achieve Self-Sustainability. To do both of these with minimal distraction, we should make sure to build on a foundation of Explicit Structure.

In this thesis, we do not follow this order strictly, but it shows a sort of logic as to how each property fits into the bigger picture. We see that the only way discover how to achieve these goals is by *doing,* so we work to build a prototype programming system called *BootstrapLab* that makes progress on the Three Properties simultaneously.

This system itself is only a secondary contribution; primarily, we contribute the necessary steps and principles that its construction led us to *discover.* We believe that it should be possible to build these three properties atop a wide variety of programming systems, and our hope is to document enough of a generalisable technique to make this feasible for the average programmer.

Instead of seeking to master the ins-and-outs of Smalltalk, Unix or indeed BootstrapLab, what is needed is to steal the best ideas and synthesise them into something fresh---to have our cake and eat it too. It is as if we have developed the study of sorting by coming up with a prototype sorting algorithm---the new clarity is the important part, while the concrete program was just the vehicle that got us there.

However, before we embark on this journey, we must first go into a little more detail on programming systems in general and establish how we will evaluate according to the Three Properties. This is the topic of the next chapter.
