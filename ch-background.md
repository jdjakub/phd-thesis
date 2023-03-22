\joel{ Stories (reminder)
1. Bootstrapping self-sustaining systems from a starting point
2. Fixing/freeing the COLA work by lifting the text restriction
3. Re-building the software stack with the benefit of hindsight and better tech.
}

\todo{Why the 2 properties?}

\chapter{Background}

\joel{
To begin we must explain sys vs languages.
Now we can cover the COLA programming system design including the Id object model.
This introduces self-sustainability, bootstrapping, and notational freedom.
On the latter: here's why COLA's text reliance is a liability.
But why is all this so hard? / big picture: Against Conv Wisdom.
}

In order for the later chapters to make sense, we begin by covering the relevant background concepts and research. This chapter is organised by themes because each one emerges as a reaction to the previous:

* To start with, this thesis is about building programming systems. In order for this to make sense, we first need to introduce "programming systems" and explain how the concept is related to that of a programming language.
* Then, we will examine the COLA system design which exhibits *self-sustainability*, but whose *notational freedom* is limited to *syntactic* freedom.
* Furthermore, the way that a COLA is *bootstrapped* is in terms of batch-mode language transformations, and we explain why we see this as a related limitation.
* These concerns straightforwardly point to an unexplored opportunity which constitutes the contribution of this thesis.
* Finally, we suggest reasons why such a thing has not already been tried and why the Two Properties are so rare.

## Programming Systems vs Languages
Many forms of software have been developed to enable programming. The classic form consists of a *programming language*, a text editor to enter source code, and a compiler to turn it into an executable program. Instances of this form are differentiated by the syntax and semantics of the language, along with the implementation techniques in the compiler or runtime environment. Since the advent of graphical user interfaces (GUIs), programming languages can be found embedded within graphical environments that increasingly define how programmers work with the language---for instance, by directly supporting debugging or refactoring. Beyond this, the rise of GUIs also permits diverse visual forms of programming, including visual languages and GUI-based end-user programming tools.

The classic essay by Gabriel\ \cite{PLrev} distinguishes the *languages* and *systems* paradigms in programming research. The topic of this thesis, and many of the examples we will use to illustrate concepts, rely on understanding this distinction and only make sense within the systems paradigm.
Therefore we shift our attention from *programming languages* to the more general notion of "software that enables programming"---in other words, *programming systems*.

\begin{defn}[Programming System]
A \emph{programming system} is an integrated and complete set of tools sufficient for creating, modifying, and executing programs. These will include notations for structuring programs and data, facilities for running and debugging programs, and interfaces for performing all of these tasks. Facilities for testing, analysis, packaging, or version control may also be present. Notations include programming languages and interfaces include text editors, but are not limited to these.
\end{defn}

This notion covers classic programming languages together with their editors, debuggers, compilers, and other tools. Yet it is intentionally broad enough to accommodate image-based programming environments like Smalltalk, operating systems like UNIX, and hypermedia authoring systems like Hypercard, in addition to various other examples we will mention in Chapter\ \ref{tech-tims}.

In Chapter\ \ref{tech-dims}, we propose a common language as an initial step towards more progressive research on programming systems. Our set of "technical dimensions" seeks to break down the holistic view of systems along various specific “axes”. The dimensions identify a range of possible design choices, characterised by two extreme points in the design space. They are not quantitative, but they allow comparison by locating systems on a common axis. We do not intend for the extreme points to represent “good” or “bad” designs; we expect any position to be a result of design trade-offs. At this early stage in the life of such a framework, we encourage agreement on descriptions of systems first in order to settle any normative judgements later. The set of dimensions can be understood as a map of the design space of programming systems (Figure 1). Past and present systems will serve as landmarks, and with enough of them, we may reveal unexplored or overlooked possibilities. So far, the field has not been able to establish a virtuous cycle of feedback; it is hard for practitioners to situate their work in the context of others’ so that subsequent work can improve on it. Our aim is to provide foundations for the study of programming systems that would allow such development.

While we do have new ideas to propose, part of our contribution is integrating a wide range of existing concepts under a common umbrella. This work is spread out across different domains, but each part connects to programming systems or focuses on a specific characteristic they may have.
\cite{Liveness}
\cite{KellComm}
\cite{STdesign}
\cite{DesPats}
\cite{CogDims}
\cite{MemmodPLs}

\cite{EvProgSys}
\cite{UISTAuthor}
\cite{EvUISR}
\cite{ComplementaryBasic}
\cite{Chang}
\cite{Kuhn}

The notion of "self-sustainable system" is difficult to discuss purely at the programming language level, because it crucially depends on how program execution and production are connected. For this reason, we talk about *programming systems*\ \cite{TDs,PLrev}, which include not only programming languages, but also IDEs, programming environments with non-textual notations, and other tools for creating software.  

\cite{Jupyter}
\cite{CodaWeb}
\cite{DarkWeb}
\cite{ReplitWeb}

[UIST](https://uist.acm.org/)^[ACM Symposium on User Interface Software and Technology]
[VL/HCC](https://conferences.computer.org/VLHCC/)^[IEEE Symposium on Visual Languages and Human-Centric Computing]
[LIVE](https://liveprog.org/)
[PX](https://2021.programming-conference.org/home/px-2021)^[Programming eXperience].

\cite{FPexplain}
\cite{Boomerang}
\cite{Sketchnsketch}

\cite{Domain-workbench}

\cite{Hazel}
\cite{Webstrates}
[Squeak](https://squeak.org/)
[Lively Kernel](https://lively-kernel.org/)
[Pharo](https://pharo.org/)
[Fizzygum](http://fizzygum.org/)
[Io language](https://iolanguage.org)

### Two Fundamentals: State and Change
A good general model of a programming system is like a physical system in the sense of analytical mechanics. There is always a current *state* of the system, and this will necessarily *change over time*. We stress that this is the case regardless of whether the underlying programming metaphor is imperative, purely functional, logic-based, or otherwise eschews a notion of "state" in its conceptual model. This is perhaps vacuously true in the following way. In working with a declarative or functional programming system, the expression you are currently editing or the output you are seeing at a given moment is, by definition, a single state, and this changes whether you interact or simply wait for progress. In other words, anything to which this view is not applicable will not be interactive or interesting.

We include both the visible interface and the "hidden" internal state of the system in this model. Such an all-encompassing "state", of course, isn't comprehensible atomically but is always broken down into substructures (whether byte lists, object graphs, trees or otherwise). Likewise, the actual *change* from one state to another usually does not involve all of the state but only a small part of it. In the limit, there is usually some smallest unit of state (a byte, dictionary entry, tree node) and this gives rise naturally to primitive *instructions* describing a change to such a small unit. Different choices for how to represent the instructions have implications for where it is possible to take the evolution of a system. We will see in Chapter\ \ref{ch-bl} that some choices are more appropriate than others for ensuring a system can be made self-sustainable.

### The Batch-Mode Paradigm
Computer programs originated as “batch-mode” processes. A calculation chugs away and delivers a result at the end. A compiler combs through source code and outputs a machine-level program. In the batch-mode paradigm, a program starts, runs, and then stops. The effect or “behaviour” of a program is its *output;* to change its behaviour, we need to change the executable program. But we can’t change the program directly;^[Most nontrivial changes would invalidate various binary offsets, which would then have to be discovered and adjusted.] instead we change its *source code*, and then *re-generate* the program as another batch-mode operation. However, this is not a problem because any important effects of the program are outside it, whether on a paper printout or saved to magnetic tape. Any data structures the program creates occur within its “working memory”, a temporary scratchpad internal to the program and discarded when it’s done its job.

Thus, the key assumptions of batch-mode programming are:

1. The point of a program is to output a result (i.e. the answer to a question.)
2. Compute and storage (fast and slow) are scarce resources that we can only afford for essential tasks.
3. Because of this, as well as the task itself, it typically takes a long time to create the result.
4. There are only one or two data items we care about long-term (e.g. the result). Any intermediate steps it takes to create the result are unimportant or uninteresting, so they should be discarded to free resources.

The two key consequences of these assumptions are:

1. We implicitly design programs to run in fast (volatile) storage to ensure performance and that the uninteresting intermediate state is not wastefully persisted.
2. For the one or two exceptional data items that we do care about (e.g. the output), we have to remember to write code to move these out of volatile memory and into non-volatile storage. This isn’t tricky, because there are only a few items we care about!

The batch-mode paradigm can be considered an appropriate adaptation to the early conditions of computing: specialised, industrial-scale use cases and extreme resource scarcity (in terms of storage and compute.) Its key contributions to the world are:

1. The *volatility split:* at all times, a programmer must remain aware of, and specify, whether their data is to be stored in volatile or non-volatile storage. An example is the question "Does this belong in a class, or in the database?".
2. The *change by re-creation* model: in order to change something, trace its history to the process that generated it, change the source input, and then re-generate everything from that point onwards. For example, a single-character typo in one's \LaTeX-typeset thesis must be fixed by re-running \LaTeX\ and re-typesetting the entire document.

Of the four assumptions, no.\ 4 is surprisingly consequential. We will refer to it as BMA4 for short.

### The Unix Paradigm
Let us fast-forward to the era of Unix, whose concepts and programming model are the water we swim in whether on Windows, Mac, Linux or something else. Unix was created before the rise of GUIs and naturally preserved the batch-mode norm from its surroundings. Still, a different “interactive” paradigm had been around in embryo for a while.

An "Operating System" like Unix is a *continuously* running master-program (kernel) which allocates compute and storage to batch-mode programs under its supervision. Unix calls these *processes;* each one a sort of virtual processor plus working memory (which it calls "core".) As before, core is merely *instrumental* for the process to do its job quickly, and is discarded upon termination. The other world, of "things that matter", is a hierarchical tree of *files* shared between processes and persisted as they come and go. Users can access the current state of processes and files via the command line *terminal* or *shell*. This runs as a process but is, like the kernel, continuously running, passing input and output between the user and the kernel. Let us summarise all this as the *Unix Paradigm:* a compromise between the batch-mode and interactive paradigms where limited interaction is used to organise batch-mode computation.

There is one further small innovation worth noting. As an Operating System, Unix goes some of the way towards hiding the Volatility Split by *paging* core to disk, prioritising fast storage for processes that need it (the others are paused waiting for some event.) However, as mentioned, Unix still sees processes as temporary scaffolding to be discarded when complete (BMA4.) What this means is that, even though Unix is clearly capable of persisting a process' data while it is still active, it will still be discarded when the process completes. In any case, we see that the Volatility Split and Change By Re-creation were preserved from the Batch-Mode era.

How do we analyse Unix as a programming system? Unix is one big system managing many smaller processes. To the extent that *other* programming systems are implemented as Unix processes, Unix functions as a meta-system supporting subordinate programming systems. Thus there are always *two* levels to programming in the Unix Paradigm: the "large" scope and the "small" scope. In both of these, *state* consists of both core and files thanks to the Volatility Split. However, the two levels emphasise these to different degrees.

At the large scope, it seems appropriate to describe the filesystem as the primary "state" of the Unix system. There is, of course, nontrivial state in core such as the currently running processes and bookkeeping information for them, which will be lost if the system is restarted. Nevertheless, from its own perspective, the "important data" for users all lives in the filesystem; what happens to be running at any given time in core is merely instrumental.^[C.f. Assumption\ 2: if a data structure was important, the programmer would have written code to save it to a file!] The agent of *change* at the large scope is the kernel, and the primitive "instructions" are the system calls used to write to files and change the file tree.

Meanwhile, at the small scope of an individual programming system *process*, the manifestation of state and change will depend on the particular system itself. Yet because we know it is running as a Unix process, we can at least be assured that the lowest level of "state" will be split between core and files, and "change" will occur through the execution of machine instructions.

### The Interactive Paradigm
The interactive paradigm, in its fully developed form, emerges from a feeling of compute/storage abundance. This frees us from having to start with the question "what purposes can computers currently cope with?" and instead ask "what are computers for?" with the answer being roughly equivalent to "anything". This paradigm relaxes or rejects the basic axioms of batch-mode programming:

1. The point of a program is to simulate a piece of the world somehow useful to a human, free of the constraints of physical media substances. Producing an output is but one of many such effects useful to humans.
2. Compute and storage are (or will be^[This was the attitude at Xerox PARC summed up in the principle "design for the hardware of tomorrow".]) sufficiently abundant that we can use them generously in service of higher goals.
3. Therefore, we can expect batch-mode tasks to complete instantaneously to the human eye. For those that still take time, it is important to continue the rest of the simulation and keep it responsive.
4. We do not know *a priori* and in full generality which data items we care about long-term, because the space of human purposes is large. For some tasks, the journey is more important than the destination.^["Maybe the real result was the data we made along the way!"]

And the consequences:

1. We cannot *commit* on principle to saving some data and discarding or hiding other data; we persist everything by default and provide means to free up resources explicitly. We reluctantly abstain from this only where necessary for performance, treating this as a temporary optimisation to relax in future. But we cannot decide for the user what they will be interested in.
2. Code can simply create and manipulate data structures without the programmer needing to keep in mind what type of storage they live in---there is no Volatility Split.
3. It ought to be possible to change the state or behaviour of the system directly, as opposed to changing some upstream specification and re-creating the system from that. This is important both for performance and for avoiding premature deletion of data. Instead of Change By Re-creation, we have "Change by Changing".

This paradigm is embodied in systems like Lisp (which pre-dates Unix) and Smalltalk (which does not), both for different reasons. Lisp originated as a sort of "executable mathematics and logic" for AI research; it makes sense that such a tool had little need to import "batch-mode, industrial-scale computation" as its primary concept. Smalltalk, on the other hand, deliberately rejected this convention to serve its goal of shifting computing out of the industrial capability and into the personal. In neither do we find a mandatory separation between volatile and non-volatile storage, nor between "large objects" (files and processes) and "small objects" (variables and code.) Instead we find a graph of data structures, called "expressions" in Lisp and "objects" in Smalltalk.

From this perspective, the Volatility Split, carefully preserved by Unix from the dawn of computing, looks like an *implementation detail* being obnoxiously raised to the level of major design concern. The question of whether the variable X should live as dancing electrons or as magnetic domains resembles the question of at which precise address a new string buffer should live in memory. So-called "manual" memory allocation routines like `malloc()` actually remove the need for the programmer to "manually" decide such an implementation detail.^[The "manual" term refers to the management of allocation *lifetime*, in contrast to *garbage collection* which automatically decides when to free memory.]

Meanwhile, the Change By Re-creation model seems like an unnecessarily convoluted path to do a simple thing. Suppose we want the field `foo` of object `greeter` to change to `"Hello"`. The system knows the object referred to by the name `greeter` and knows how the field `foo` is stored. It seems odd to have to dig through code and re-generate the object along with everything it touched. Surely we ought be able to just type `greeter.foo = "Hello"`. Bret Victor dubbed this "destroy-the-world programming", though Basman et al. rescue the concept as "Queen of Sheba Adaptation" by addressing the cost (I lose all my data, see Consequence 1)—presumably as an implementation detail (i.e. you can still just type greeter.foo = "Hello".)

As examined by Gabriel\ \cite{WIB} and Kell\ \cite{KellOS}, Unix "won" in a way that Lisp and Smalltalk did not, firmly establishing the Unix Paradigm as ubiquitous. Where Lisp and Smalltalk exist, they are processes sitting within Unix and saving to "image" files. For implementors of novel programming systems, the tenacious Volatility Split and Change By Re-creation model clash with the Interactive Paradigm natural to the enterprise.

## Self-sustainability
This is one of the properties of programming systems that we are interested in for this thesis. To a first approximation, self-sustainability involves being able to evolve and re-program a system, using itself, while it is running. 

It is instructive to start by examining Unix in this light. At the "large" scope, we have shell scripts coordinating files and processes within the running Unix system. We also have individual processes---text editors, compilers, interpreters, debuggers---which change the large-scale system state (files), such as by creating new programs. In this way, a Unix system is evolved and re-programmed using itself, while it is running; Unix (i.e. Programming-in-the-large) is self-sustainable.

The matter is complicated by the fact that a small minority of special changes require restarting the system to take effect. However, the spirit of the "while it is running" condition is that the system does not need to be *destroyed and rebuilt from scratch*. Because the "state" of the large scope is mainly files, the destructive operation here is not so much "restart" as perhaps "reset" or "reinstall".

In contrast, for a programming system that exists as a process within Unix, its data structures in volatile memory will be permanently lost if it is restarted, and these data structures may well be an important part of its state as a continuously running interactive programming system. Indeed, when we turn our attention to the "small" scope of the Unix Paradigm, we mostly do not see self-sustainability.

### Self-Sustainability at the Small Scope
Compiled programming languages like C++ or Java are used via several different Unix processes. These include interactive ones like text editors, and batch-mode ones like the compiler. Some compilers are *self-hosting*, meaning that they can compile their own source code into a functionally identical compiler program:

1. Say we design a novel language *NovLang* that we want to use.
2. We write *NovLang* source code that will compile other *NovLang* source code into a runnable program.
3. We hand-translate this to C/C++ and build a compiler for *NovLang*.
4. We run this to compile the *NovLang* source code from step 2.
5. We obtain a runnable compiler for *NovLang*, which was written in *NovLang* and is now "self-hosting".
6. We can now discard the C/C++ code.

We say the self-hosting compiler has been *bootstrapped* into existence. From this point onwards, it is possible to change the source and thereby change the compiler (in the manner of Change by Re-creation) and hence use a newly evolved version of NovLang. Self-sustainability ought to be the analogue of this property for interactive programming systems.

However, programming systems exhibit this more rarely because they more closely resemble *interpreters* than compilers. If we apply a compiler to its own source code, we get the (functionally) same compiler. But if we apply an *interpreter* to its own source code, all we get is a much slower interpreter! The job of a compiler is to generate a new program, which could (in principle) be a *replacement* for the old compiler. The job of an interpreter is to execute the program that *would* have been generated by the compiler---but unless *that* program is itself a compiler, its effect cannot *replace* the original interpreter. Usually, interpreters are used to run ordinary programs, not compilers.

It is worth noting that the "meaning" of code is different depending on whether it is run through a compiler or an interpreter. Code being compiled can be viewed as describing how data structures and instructions should be outputted to a file; the actual execution is left to a later step. On the other hand, code being interpreted describes how these same things are built up in memory, and possibly executed right away.

Furthermore, this is all still in the batch-mode world where the memory used by the process is disposable and unimportant, hence Change by Re-creation does not cause too many problems. For programming systems, this is often inappropriate or at least highly inconvenient. Since interactive processes are meant to run as long as the user wishes, they contain a lot of important state in memory. Furthermore, the Volatility Split was traditionally just forwarded into the user's mental model, making no guarantees about whether work would be saved in the event of a crash and recommending the user to save regularly. While many applications and programming systems did eventually implement "auto-save", they nevertheless form a clear case where BMA4 fails and re-creating the system risks important data loss (or, at the very least, inconvenience.)

### Key Components of Self-Sustainability
In light of these points, we can discover some key criteria of self-sustainability by considering the Web browser as a programming system. What would it take to make the Web browser self-sustainable?

1. In the first place, the browser is not even *self-hosting*. In order to be self-hosting, it would need to be able to generate a slightly different web browser binary, from slightly different JavaScript source code.
2. However, the browser is a program compiled from C++, not JavaScript. (*Language mismatch*)
3. *Even if* we translate all the C++ to JavaScript, the browser's JS console environment is not built to support such a large, interconnected codebase. (*Scale mismatch*)
4. *Even if* we make it support a large codebase, the JavaScript source will build up data structures in memory instead of outputting machine code to an external file. (*Interpreter/compiler mismatch*)
5. *Even if* we modify the JavaScript source to consist of JavaScript that will *generate* a new browser program (instead of just *embodying* one), simply fixing a typo in a menu will require an hour-long build followed by a loss of the current browser session, tabs, etc. (*Regeneration delay*, *loss of state*)

We see a number of hurdles to achieving a self-hosting state: there must be no language, scale or interpreter/compiler mismatch. This is followed by one or two for self-sustainability: evolving the system must not incur a disproportionate regeneration delay or a loss of important state. We will use these criteria in Chapter\ \ref{tech-dims} as "penalties" for measuring self-sustainability.

## Notational Freedom


## The Legacy of VPRI
With the "programming systems" frame established, we can properly consider some related work downstream of Smalltalk. It emerged from the now-retired Viewpoints Research Institute (VPRI), on whose foundations this thesis is built. VPRI aimed at creating "fundamentally new computing technologies", which is particularly visible throughout the 6-year project known as "STEPS towards a new computing" \cite{Steps08,Steps09,Steps10,Steps11,Steps12}. The aim was to fully replicate a familiar graphical end-user operating system with applications in under 20,000 total lines of code. Such an ambitious goal provided the constraint needed to force innovation in taming complexity, motivating widespread use of domain-specific languages and investment in the "meta"-level. Several writings describing programming systems, or parts of them, came out of STEPS, but the most important for this thesis is the COLA.

### COLAs

The one system that directly influenced our work is the Combined Object Lambda Architecture or COLA\ \cite{COLAs}: a small but expressive starting system that could be self-improved into anything. The system is described as a mutually self-implementing pair of abstractions: a structural object model (the "Object" in the acronym) and a behavioural Lisp-like language (the "Lambda"). The system aims for maximal openness to modification, down to the basic semantics of object messaging and Lisp expressions.

The two key features we see in the COLA idea are *self-sustainability* and *mood-specific languages.* Self-sustainability is straightforwardly inherited from the Smalltalk tradition and amplified, while mood-specific languages are a continuation of the more familiar idea of Domain-Specific Languages.

Self-sustainability allows for innovation feedback that the authors refer to as _internal evolution_:

> Applying \[internal evolution\] locally provides scoped, domain-specific languages in which to express arbitrarily small parts of an application (these might be better called *mood-specific* languages). Implementing new syntax and semantics should be (and is) as simple as defining a new function or macro in a traditional language.

We wish to have mood-specific *notations* more generally, which we call *notational freedom.* A self-sustainable system is brought into existence in a way that is analogous to self-hosting languages; this is called *bootstrapping*. COLA presents its bootstrapping process as if it were such a language, which is odd considering it is a system. (text stuff goes here?)

Instead of only being able to make changes to a mass of source code written in the same language, a COLA is supposed to permit easy proliferation of languages optimised for individual sub-problems. Such languages are typically known as domain-specific languages or DSLs, but the STEPS project sees a range of granularity all the way down to “mood-specific languages” (MSLs). An example of an MSL might be expressing a single line of code with mathematical notation instead of the usual monospaced ASCII subset. The language half of a COLA is designed to make it feasible for a programmer to do this sort of thing on a whim; encouraging “the right language for the job” no matter how small the context, in a way that normal language infrastructure makes too costly to be worthwhile.

\cite{Nile,Gezira}

The aim of the COLA design is to create a maximally flexible self-sustaining system, but its exposition has three limitations:

1. It restricts the form of notations to text, curtailing the ambition of pervasive domain-specific adaptation. We would prefer mood-specific *notations* or *interfaces* generally, including *but not limited to* text.
2. COLA's support of programming languages is presented as a pipeline of traditional batch-mode transformations such as parsing, analysis, and code generation. This further steeps it in a world of linear sequence transformations that obscure the interesting ideas.
3. The bootstrapping process of implementing such a design is also cast in terms of batch-mode transformations of various source code files. We would rather have the ability to gradually *sculpt* a system into a self-sustainable state, interactively, through a combination of manual actions and automatic code.

Because COLA was such a big influence on the present work, we will refer to it repeatedly for comparison and offer some analysis of our own.

### The \OROM{} Object Model

The "structural" half of a COLA is described in \cite{OROM}. It describes a late-bound,^[Fewer commitments; more things determined by runtime conditions.] Smalltalk-style^[OOP with more emphasis on object instances and messaging, as in a distributed system; less emphasis on class hierarchies implementing traditional data structures.] objects and messaging environment that the authors call "Id". We will summarise the important points here.

An \OROM{} *object* is a block of state which can change as a result of messages received by it. Messaging (analogous to *method invocation* in mainstream OOP) works as follows:

* A message is sent by first *bind*-ing its name to its *implementation*: specific code, which is then run in the context of the receiver $R$.
* This "bind" step is accomplished by sending a further message; this time, to the receiver's *vtable* $V(R)$. A vtable is another object that maps "message name" to "implementation code"---it's analogous to a "class" in mainstream OOP.
* This initial "bind" message triggers a similar "bind" to *its* vtable $V(V(R))$, and so on: recursing up the vtable-chain, and terminating at a base case.
* The higher levels of the vtable-chain mean that different kinds of vtables can be supported,^[As well as different kinds of "kinds of vtables", and so on.] each implementing the "bind" operation in its own way.

\cite{OMeta}
\cite{COLAs}
\cite{OROM}
\cite{OECM}

\joel{Bootstrapping allows the language creator to co-evolve the language and the compiler. However, they typically have to do so outside of the environment used for building other products.}

\todo{This is hard to understand.}
A related concept is that of *meta-circular evaluator*, which would be an interpreter for NovLang, written in NovLang, that implements features by deferring to its own implementation. For example, a meta-circular interpreter for Lisp in Lisp would implement `eval` by calling `eval`. This makes the task of writing the interpreter easier, but it does not eliminate the distinction between the product (application) source code and producer (interpreter) source code. \cite{ProcRefl}.

\joel{
The two best-known self-sustainable programming systems are Lisp and Smalltalk. Both are typically discussed in programming language terms, but they are more interesting as programming systems. In Smalltalk and many implementations of Lisp (e.g., Interlisp), the system itself (producer) can be modified from the same environment that is used for creating products. In other words, a Smalltalk image contains both the objects that make up the product as well as the objects that make up the Smalltalk environment itself. We also regard Unix as a whole to be a self-sustainable system, though the individual programs within it seldom have this property.

Most literature discussing self-sustainability\ \cite{SSS08,SSS10} seems to focus on textual languages as the way to get there. We broaden the scope to programming systems, because this is necessary in order to talk about interaction and graphical capabilities. We desire to support graphical or structured ways of expressing programs that go beyond text\ \cite{Subtext,Infra,Varv}, and feel that this has been neglected in prior work.
}

## Text as *presentation*, not *re*presentation
STEPS was a project dedicated to critically examining assumptions about the nature of how computing and programming must work, yet it is surprising that the reliance on "text" or "language" seems to have been preserved wholesale. We find the "domain-specific at many levels" idea appealing, but would prefer to consider "notations" in general, rather than narrow syntax or semantics of languages.

The "language" that we consider too narrow a constraint consists of renderings of text. This is as opposed to other uses of “language” in areas such as design: "visual language" may not include text at all, but uses the "language" term metaphorically for the arrangement of elements. Under this latter meaning, the "domain-specific language" idea has all the degrees of freedom it deserves. Nevertheless, our use of the word "language" will stick to denoting renderings of text glyphs and we shall use words like "notation" or "interface" for the fully general extension.

The problem with "plain text" is that it fuses together two independent concepts: what we could call *presentation* (how one reads and modifies the data) and *representation* (how the data is stored in memory or on disk). For example, the AST of a program *could* be stored in its tree form and *presented* as indented text. But what we do instead is serialise the text, store that, and parse it back before we are able to work with it.

This thesis approaches the default text-centrism of programming with skepticism. We direct the reader to the fuller arguments from the authors of Subtext \cite{Subtext} and Infra \cite{Infra}, but we will summarise the most important points here and offer some of our own. 

### Language isn't everything
Diagrams and pictures are sometimes the form in which a problem or solution is delivered (e.g. in vector mathematics). Programmers ought not to be forced to describe pictures using words.

### Language can be stored differently to a character list
Language always contains *structure*: English paragraphs contain sentences, containing clauses, containing words. Natural language, like English, is typically entered into a digital medium only to be poured out again at some other end, like photo uploads; normally, the computer does not need to dive into the structure at all. On the other hand, for programming languages, the abstract syntax tree is the entire point.

Despite this, programming language source code is universally stored as a sequential recording of keystrokes, rather than as the tree or graph that it represents. This has the downside that every program that consumes or transforms the code must recover (parse) this structure out, discovering any mistakes only at this point of consumption (since these were just recorded with the other characters.) This is like storing vector graphics diagrams as arrays of pixels and using Computer Vision to haphazardly recognise shapes and lines: unnecessary work to recover information that was thrown away at creation time.

More unfortunate work results from having to "escape" characters that have been reserved to denote structure instead of their literal selves. In the worst case, the storage of language as character lists is largely responsible for the class of attacks known as SQL injection. This would not be possible with SQL commands represented as trees containing holes to be filled with user-submitted strings.

### Digital "plain text" is not inherently human-readable
This argument is made best by Hall\ \cite{Infra}, p.\ 14:

> The critical observation is that software infrastructure is heavily involved in supporting the human-readability of text. It is not the case that the bit sequences of UTF8 or any other text encodings are somehow intrinsically understandable to a human. An application interprets the bytes as character codes as per a known standard, which are mapped to glyphs in a font, and rendered to a grid of pixels. This chain of interpretation and transformation starts with clusters of electrons and ends with clusters of photons before the human nervous system takes over. The point being that there is still a necessary software layer performing a transformation in the middle.

> electrons $\rightarrow$ bits $\rightarrow$ charactercodes $\rightarrow$ glyphs $\rightarrow$ pixels $\rightarrow$ photons

> “Human readability” just colloquially implies that it is a standard encoding understood by most text editors. The sense of inherent readability merely comes from the ready availability of tools that render ASCII and Unicode. One can assume that a text editor or some text rendering infrastructure exists in the target system. Therefore, any encoding could technically achieve the same ‘human readable’ status as ASCII if it and its editors were general-purpose enough to warrant an equally ubiquitous install base. Thus, there is an opportunity to expand or upgrade the realm of what can be considered human readable.


\cite{Subtext}
[schematic tables](http://www.subtext-lang.org/OOPSLA07.pdf)

### We Study the Spherical Cow
Text usually functions as a *medium* or rendering of something that is not inherently text. In the first place, text was invented to record speech made by human beings. In programming, text is used a *proxy* for a nested tree-like structure, but is *not the structure itself.* Therefore, to study a programming idea like self-sustainability, it is unfortunate to have the contingencies of text representation "getting in the way" of studying the idea itself. Therefore, even though a real-world programming system may use text, we wish to avoid this obscuring layer for much the same reason as a physicist studies a frictionless sphere in a vacuum instead of, say, a cow in a field. We want to not be distracted with air resistance and complex shapes, so as to focus on the property we're interested in; future work can then add the practical complexities back in again for a more realistic model.

## Visual / End-User Programming
Beyond supporting language-based notations not stored as text, there are situations (domains) where a graphical notation is best. It is important to support these to truly realise the goal of mood-specific notations.
\cite{Boxer}
\cite{Sketchpad}
\cite{WWID}
[Briar](https://graphics.cs.wisc.edu/Papers/1994/Gle94/constraints.pdf)
\cite{Palimpsest}
\cite{DDV}
[Apparatus](http://aprt.us/)
\cite{YWIMC}
\cite{VPcodex}
\cite{VPsurvey}
\cite{GalleryUIs}
\cite{Forms3}
\cite{Hopscotch}

## Towards a "bootstrapping" theory
There is a well-established art of bootstrapping self-hosting compilers and meta-circular languages. However, the same cannot be said for more general programming systems. The seeds of such a foundation are scattered around several papers and blog posts.
[BootstrapLab](https://programmingmadecomplicated.wordpress.com/2018/01/18/3-hacking-together-orom-domctrlshiftj/)
[Ideal goal](https://programmingmadecomplicated.wordpress.com/2018/09/13/what-am-i-aiming-for-again/)
[CookClay](https://www.cemetech.net/forum/viewtopic.php?p=270092#270092)
\cite{Meta-helix}
\cite{PRinPLs}
\cite{Bootfrom0}

## Against Conventional Wisdom
A plausible hypothesis about *why* programming is the way it is---and hence why this thesis has a novel contribution to make---concerns a mismatch between programming's *military-industrial* history and its modern potential for *personal* computing. This is the logic behind the work of Kell and Basman.
\cite{Kell-os}
\cite{Externalise}
\cite{Kell-mmm}
\cite{OAP}
\cite{Entangle}
\cite{Entangle-critique}
\cite{TcherDiss}
\cite{SwStudies}
\cite{Wisdom}
\cite{Top}
