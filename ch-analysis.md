\joel{ Stories (reminder)
1. Bootstrapping self-sustaining systems from a starting point
2. Fixing/freeing the COLA work by lifting the text restriction
3. Re-building the software stack with the benefit of hindsight and better tech.
}

\hypertarget{analysis}{%
\chapter{Analysis}\label{analysis}}

\joel{
To begin we must explain sys vs languages.
Now we can cover the COLA programming system design including the Id object model.
This introduces self-sustainability, bootstrapping, and notational freedom.
On the latter: here's why COLA's text reliance is a liability.
But why is all this so hard? / big picture: Against Conv Wisdom.
}

Having introduced the relevant background knowledge for the dissertation, we will define concepts that we will repeatedly use and the ideas on which they depend. Some of these concepts will already exist, in which case we may use established terms or define new ones as necessary. Others will be novel concepts or interpretations of existing ideas.

This dissertation is about building programming systems. In Chapter\ \ref{Background} we defined this concept and explain how it is related to that of a programming language. Here, we will distinguish the general concepts of *state* and *change* in programming systems. We will illustrate this by distinguishing the *low-level binary* and *minimally human-friendly* levels of abstraction. We will then offer our interpretation of three major sets of conventions in which programming systems have existed, which we call *paradigms.* This will help us go on to define our Three Properties in more detail.

This will let us return to examine the COLA system design in terms of these. While it exhibits self-sustainability, its *notational freedom* is limited to *syntactic* freedom and relies on implicit structure. Furthermore, the way that a COLA is *bootstrapped* is in terms of batch-mode language transformations, and we explain why we see this as a related limitation.

# Two Fundamentals: State and Change
For the present work, our most general model of a programming system is like a physical system in the sense of analytical mechanics \cite{SICM}. There is always a current *state* of the system, and this will necessarily *change over time*. We stress that this is the case regardless of whether the underlying programming metaphor is imperative, purely functional, logic-based, or otherwise eschews a notion of "state" in its conceptual model. To see how this is inevitable, consider the following. In working with a declarative or functional programming system, the expression you are currently editing or the output you are seeing at a given moment is, by definition, a single state, and this changes whether you interact or simply wait for progress. In other words, anything to which this view is not applicable will not be interactive or interesting.

In such a model, we include both the visible interface and the "hidden" internal state of the system (e.g. heap data structures) as part of "state". Such an all-encompassing "state", of course, is not comprehensible atomically but is always broken down into substructures: on the interface side, this is usually various types of rectangles, while internally we see byte lists, object graphs, trees and so on. Likewise, the actual *change* from one state to another usually does not involve all of the state but only a small part of it. In the limit, there is usually some smallest unit of state (a byte, dictionary entry, tree node) and this gives rise naturally to primitive *instructions* describing a change to such a small unit. Different choices for how to represent the instructions have implications for where it is possible to take the evolution of a system. We will see in Chapter\ \ref{ch-bl} that some choices are more appropriate than others for ensuring a system can be made self-sustainable.

## The Low-Level Binary World
While human beings think in terms of names, computer hardware works on numerical bit patterns. This shows through to the lowest level of the software stack, which we call the *low-level*, *machine-level* or *binary world.* Here, state consists of one long line of bytes while change is achieved via machine instructions interpreted by the hardware. There are three noteworthy features of state here for a programmer's mental model:

* *Flatness:* if there is not enough space to insert something, we have to physically move things to make room. For example, if a list of integers is represented by a contiguous array, inserting at the beginning requires moving every later entry one place forward. If there is not enough spare capacity in the array, it needs copying somewhere else with more space.
* *Absoluteness:* while various instruction sets support relative addresses, data structures are established through absolute pointers. Thus when a data structure is moved, any internal pointers need relocating based on the new start address.
* *Numerical meaninglessness:* addresses are numerical, but no number has an inherent meaning. This interchangeability means it is unlikely that two parties will happen to coordinate on the same number for the same purpose. Instead, they must communicate beforehand and agree on which numerical addresses hold which things or correspond to which names.

## The Minimally Human-Friendly World
Very early on in the history of programming, key technologies were developed to allow humans to create software using names instead of numbers: symbolic assemblers, high-level languages, and so on. These free us from having to think about the numerical meaninglessness in the binary world. For example, in the C programming language, we are free to use names for variables, functions and the parts of data structures. However, C's nature as "portable assembly" \cite{Cprog} means that the flatness and absoluteness of memory must remain in our awareness. Even though data structures can be designed to grow by containing slots for pointers to newly allocated blocks, competence at C still requires an understanding of the real nature of low-level memory.

A language like JavaScript, in contrast, does away with this: state in JavaScript is a graph of dictionaries with named entries. One does not allocate so many bytes of memory and receive an absolute pointer, but instead creates an empty dictionary and receives the dictionary itself. We are not sure if we should proclaim this mental model "human friendly" in some absolute sense, but we do assert that this is at least the *minimally* human-friendly model of state that is possible. To be explicit, the three negations of the low-level aspects are as follows:

* *Dynamic and nestable* instead of *flat:* one can simply add new entries, insert items in collections and create new objects.
* *References* instead of *absolute pointers:* there is no distinction between pointers and values, and references are implicit and automatic. One only needs to be aware that some things are passed by reference and some by value. The underlying system can lay out these data structures in memory however it wishes and even move them without the programmer needing to update the references.
* *Names* instead of *numbers:* objects can be identified by a root-level name or by a multi-name path. This provides for logical nesting relationships and makes it easier for parties to coordinate (agreeing on an already-used name is easier than picking an available number and agreeing on it.)

## Let Us Avoid The Low-Level Binary World
In the comparison of programming abstractions, there are what we could call *industrial* virtues: precise control, efficiency, performance, and so on. There are also *leisurely* virtues: simplicity, convenience, lack of responsibilities, etc. The industrial and leisurely virtues are somewhat in opposition and correlate with a model's perception as "low-level" or "high-level". Lower-level abstractions are said to embody more of the "industrial" virtues at the expense of the "leisurely", and vice versa for high-level abstractions.

We say all this to note that we agree with the general pattern, but with one important exception: we believe that there is nothing interesting going on in the low-level binary world. It was historically necessary to begin there, and it still underlies all of the software in which we do our work, but we do not see any benefits to working within that model for building programming systems. Anything involving relocating pointers, resizing structures or mapping names to numbers should be handled automatically and not occupy any cognitive resources.

We will see in Chapter\ \ref{bl} that building a self-sustainable system follows a path similar to the historical development of programming: we begin at a low level and build up more advanced features and conveniences. However, we do not think it is necessary to begin at quite the same low level as the historical case: in all our work, we will take the minimally human-friendly level as our starting point and not give much thought to how it is implemented.

# Paradigms of Programs and Programming
The concept of a scientific "paradigm" was popularised by Kuhn \cite{Kuhn}. It refers to the set of norms and conventions in which scientific questions are pursued and results are interpreted. In computing, a "programming paradigm" is a set of norms around the concepts and style in which programs are built.

In this section, we wish to broaden the scope and consider the foundational assumptions of programming itself, and the effects this has on how it is performed. A paradigm here is a set of norms and conventions centred around an idea of what a "program" is, what programs are for, and what is technologically feasible in the current environment.

We observe that certain periods of computing history and influential programming systems embody distinct paradigms in this way. We propose three: Batch Mode, Unix, and Interactive. Each one accommodates our Three Properties differently. It is important to understand the Interactive Paradigm, whose assumptions pose the least resistance to achieving the Three Properties. Equally important is to be clear on the Unix Paradigm we currently inhabit, because we can only realistically achieve our goals from within it. However, the Unix Paradigm is best understood as inheriting from its predecessor, which we will introduce first.

## The Batch-Mode Paradigm
Computer programs originated as "batch-mode" processes. A calculation chugs away and delivers a result at the end. A compiler combs through source code and outputs a machine-level program.

In this paradigm, a program starts, runs, and then stops. The effect or "behaviour" of a program is its *output;* to change its behaviour, we need to change the executable program. But we cannot change the program binary directly; most nontrivial changes would invalidate various binary offsets, which would then have to be discovered and adjusted. Instead, we change its *source code*, and then *re-generate* the program as another batch-mode operation.

This poses no problem because any important effects of the program are outside it, whether on a paper printout or saved to magnetic tape. Any data structures the program creates occur within its "working memory", a temporary scratchpad internal to the program and discarded when it's done its job. This reflects the technological fact that storage comes in two varieties: one is fast but volatile (it loses its contents without a steady power supply), while the other is non-volatile but slow. Both are expensive, as is processing power.

### Assumptions and Consequences in Batch-Mode
The above points can be distilled into the following assumptions:

1. *Program Outputs Result.* The point of a program is to output a result, such as a numerical calculation or data retrieved from records.
2. *Resource Scarcity.* Compute and storage (fast and slow) are scarce resources that we can only afford for essential tasks.
3. *Expect To Wait.* It typically takes a long time to create the result, whether due to the task's intrinsic difficulty or to Resource Scarcity.
4. *Delete By Default.* There are only one or two data items we care about long-term (e.g. the result). Any intermediate steps taken to create the result are unimportant or uninteresting, so they should be discarded to free resources.

The three key consequences of these assumptions are:

1. *Runtime Is Volatile.* We implicitly design programs to run in the fast/volatile storage. This ensures performance and that the uninteresting intermediate state is not wastefully persisted.
2. *Few Things To Save.* For the one or two exceptional data items that we do care about (such as the output), we have to remember to write code to move these out of volatile memory and into non-volatile storage. This is not tricky, because there are only a few items we care about!
3. *Runtime As Obstacle.* The time during which the program is running is an *obstacle* to us getting the result; a better program is one that terminates sooner.

### Compilers and Change By Re-Creation
In Batch Mode, the programmer writes code in a High-Level Language (HLL) and passes this through a *compiler* which outputs an executable program. The programmer can then make changes to the source code and compile a new executable. Strictly speaking, the old program executable is *replaced* with the newly generated one. Still, the continuity between the versions means "the program" as envisioned by the programmer is changing. In other words, Batch Mode facilitates *change by re-creation:* in order to change something, we trace its history to the process that generated it, change the source input, and then re-generate everything from that point onwards. For example, a single-character typo in one's \LaTeX-typeset thesis is fixed by re-running \LaTeX\ and re-typesetting the entire document.

### Static Commitment in Batch-Mode
The stages of *compile-time* and *run-time* are of comparable importance in this paradigm. Properties that are *static*, or *early-bound*, are invariant over the entire run-time of a program and are "baked in" at compile-time. Those that are *dynamic* or *late-bound* may vary over the run-time. Since a program has a specific answer to work out (Program Outputs Result), then the only properties that *need* to be dynamic are those that directly pertain to such a process. All other properties are candidates for *static commitment:* the compiler (suitably informed, e.g. by type annotations) can assume they will never change, and hence can avoid generating code to deal with the consequences of such changes.

Beyond optimising the program's performance, this also gives programmers an opportunity to make mathematical guarantees about certain properties. This all follows from "Runtime As Obstacle": a program's destiny is to terminate with an answer, and a better program terminates sooner. If requirements change, and some formerly static properties now need to be dynamic, there is no problem: the program is simply re-written and re-compiled, ready to run under the new conditions.

### Key Legacies of Batch-Mode
The batch-mode paradigm can be considered an appropriate adaptation to the early conditions of computing: specialised, industrial-scale use cases and extreme scarcity in storage and compute. Its legacy survives today in the following forms:

1. The *volatility split:* at all times, a programmer must remain aware of, and specify, whether their data is to be stored in volatile or non-volatile storage. An example is the question "Does this belong in a class, or in the database?".
2. The *change by re-creation* model: in order to change something, trace its history to the process that generated it, change the source input, and then re-generate everything from that point onwards.
3. Encouragement of *static commitment:* many innovations in programming take the form of improved ways to enforce static properties over a program's runtime, such as type system features.

## The Unix Paradigm
Let us fast-forward to the era of Unix, a family of Operating Systems dating from the 1970s. For this dissertation, we are not interested in the differences between different Unix versions or descendants. Instead, we refer to the common set of concepts and conventions that form ordinary programming practice today, most importantly *files* and *processes.*

Here we have a *continuously* running master-program (the *kernel*) which allocates compute and storage to batch-mode programs under its supervision. Unix calls these *processes;* each one a sort of virtual processor plus working memory, which it calls "core". As before, core is just a means  for the process to do its job quickly, and is discarded upon termination. The other world, of "things that matter", is a hierarchical tree of *files* shared between processes and persisted as they come and go.

Unix was created before the rise of GUIs and naturally preserved the batch-mode norm from its surroundings. Still, a different “interactive” paradigm had been around in embryo for a while. Users can access the current state of processes and files via the command line *terminal* or *shell*. This runs as a process but is, like the kernel, continuously running, passing input and output between the user and the kernel.

There is one further small innovation worth noting. As an Operating System, Unix goes some of the way towards hiding the Volatility Split by *paging* core to disk, prioritising fast storage for processes that need it (the others are paused waiting for some event.) However, Unix still sees processes as temporary scaffolding to be discarded when complete (Delete By Default.) What this means is that even though Unix is clearly capable of persisting a process' data while it is still active, it will still be discarded when the process completes.

In any case, we see that the Volatility Split, Change By Re-creation, and Static Commitment were preserved from the Batch-Mode era. Let us summarise all this as the *Unix Paradigm:* a compromise between the batch-mode and interactive paradigms where limited interaction is used to organise batch-mode computation (we will describe the interactive paradigm shortly.)

### Unix as a Programming System
How do we analyse Unix as a programming system? Unix is one big system managing many smaller processes. To the extent that *other* programming systems are implemented as Unix processes, Unix functions as a meta-system supporting subordinate programming systems. Thus there are always *two* levels to programming in the Unix Paradigm: the "large" scope and the "small" scope. In both of these, *state* consists of both core and files thanks to the Volatility Split. However, the two levels emphasise these to different degrees.

At the large scope, it seems appropriate to describe the filesystem as the primary "state" of the Unix system. There is, of course, nontrivial state in core such as the currently running processes and bookkeeping information for them, which will be lost if the system is restarted. Nevertheless, from its own perspective, the "important data" for users all lives in the filesystem; what happens to be running at any given time in core is merely a means to an end.^[C.f. "Few Things To Save": if a data structure was important, the programmer would have written code to save it to a file!] The agent of *change* at the large scope is the kernel, and the primitive "instructions" are the system calls used to write to files and change the file tree.

Meanwhile, at the small scope of an individual programming system *process*, the manifestation of state and change will depend on the particular system itself. Yet because we know it is running as a Unix process, we can at least be assured that the lowest level of "state" will be split between core and files, and "change" will occur through the execution of machine instructions.

## The Interactive Paradigm
We define the *interactive paradigm* as the programming model that emerges from conditions of compute/storage abundance. Such abundance frees us from having to start with the question "what purposes can computers currently cope with?" and instead ask "what are computers for?" with the answer being roughly equivalent to "anything".

### Assumptions and Consequences of the Interactive Paradigm
This paradigm relaxes or rejects the basic axioms of batch-mode programming:

1. "Program Outputs Result" is rejected. The point of a program is to simulate a piece of the world somehow useful to a human, free of the constraints of physical media substances. Producing an output is but one of many such effects useful to humans.
2. "Resource Scarcity" is rejected. Compute and storage are (or will be^[This was the attitude at Xerox PARC summed up in the principle "design for the hardware of tomorrow".]) sufficiently abundant that we can use them generously in service of higher goals.
3. "Expect To Wait" is relaxed. We can expect batch-mode tasks to complete instantaneously to the human eye. For those that still take time, it is important to continue the rest of the simulation and keep it responsive.
4. "Few Items To Save" is rejected. We do not know *a priori* and in full generality which data items we care about long-term, because the space of human purposes is large. For some tasks, the journey is more important than the destination.^["Maybe the real result was the data we made along the way!"]

And the consequences:

1. "Delete By Default" is invalidated: we cannot *commit* on principle to saving some data and discarding or hiding other data. We persist everything by default and provide means to free up resources explicitly. We reluctantly abstain from this only where necessary for performance, treating this as a temporary optimisation to relax in future. But we cannot decide for the user what they will be interested in.
2. The Volatility Split is gone: code can simply create and manipulate data structures without the programmer needing to keep in mind what type of storage they live in.
3. Instead of Change By Re-creation, we have "Change by Changing". It ought to be possible to change the state or behaviour of the system directly, as opposed to changing some upstream specification and re-creating the system from that. This is important both for performance and for avoiding premature deletion of data.
4. "Runtime As Obstacle" is invalidated: it is no longer the case that a good program terminates quickly. There may not even be any reason for it to terminate at all.

This paradigm is embodied in Lisp and Smalltalk, both for different reasons. Lisp originated before Unix as a sort of "executable mathematics and logic" for AI research; it makes sense that such a tool had little need to import "batch-mode, industrial-scale computation" as its primary concept. Smalltalk, on the other hand, deliberately rejected this convention to serve its goal of shifting computing out of the industrial capability and into the personal. In neither do we find a mandatory separation between volatile and non-volatile storage, nor between "large objects" (files and processes) and "small objects" (variables and code.) Instead we find a graph of data structures, called "expressions" in Lisp and "objects" in Smalltalk.

## Batch Mode Anachronisms
The relaxed assumptions make sense in the modern computing environment with plentiful compute and storage. However, the Unix Paradigm preserves the assumptions and consequences of Batch Mode. From this perspective, the three Batch Mode legacies feel obsolete:

\paragraph{Volatility Split.} What ought to be an *implementation detail* is obnoxiously raised to the level of major design concern. The question of whether the variable X should live as dancing electrons or as magnetic domains may not even be knowable by the programmer. It resembles the question of at which precise address a new string buffer should live in memory; a function like `malloc()` removes the need for the programmer to "manually" decide such an implementation detail.^[Even though this is known as "manual" memory management, the "manual" refers to the management of allocation *lifetime*, in contrast to *garbage collection* which automatically decides when to free memory. The actual allocation of memory via a call to `malloc()` is automatic, by definition.] Similarly, the storage medium of a variable should surely be deferred to some automatic mechanism.

\paragraph{Change By Re-creation.} This seems like an unnecessarily convoluted path to do a simple thing. Suppose we want the field `foo` of object `greeter` to change to `"Hello"`. The system knows the object referred to by the name `greeter` and knows how the field `foo` is stored. It seems odd to have to dig through code and re-generate the object along with everything it touched. Surely we ought be able to just type `greeter.foo = "Hello"`. Bret Victor dubbed this "destroy-the-world programming" \cite{BretVictor}. Basman et al. \cite{Externalize} suggest neutralising the cost of data loss from "Runtime Is Volatile" to rescue the strategy as an acceptable way of *implementing* changes, which they call "Queen of Sheba Adaptation". The important thing is the user experience, where one can just type `greeter.foo = "Hello"` regardless of what happens under the hood.

\paragraph{Static Commitment.} This now plays a much less helpful role, even an obstructive one. If a program's value derives from its behaviour as an interactive system, it may well be running for a long time or even (ideally) forever. This intensifies any disadvantages of static commitment mechanisms like type systems. On the one hand, if requirements change and a commitment must be relaxed to vary at runtime, the user is forced to terminate the running system and re-generate it. On the other, a mandatory commitment might be clearly *premature* from the perspective of the user. A type commitment like "The name X shall only refer to an integer" may be too restrictive, too soon. Consider a game in which the class `Goblin` is a subclass of `Enemy`. In a language like C++, class relationships are statically enforced, which prevents the player from befriending Goblins later in the game (by internally changing to inherit from `Friend` instead.) There is still clearly a role for commitments and optimisations, but it is piecemeal at smaller scales *during* runtime. As the duration of the program's runtime increases, the set of properties one might wish to statically commit to shrinks correspondingly.

As examined by Gabriel\ \cite{WIB} and Kell\ \cite{Kell-OS}, Unix "won" in a way that Lisp and Smalltalk did not, firmly establishing the Unix Paradigm as ubiquitous. Where Lisp and Smalltalk exist, they are processes sitting within Unix and saving to "image" files. For implementors of novel programming systems, the tenacious Volatility Split, Change By Re-creation model and Static Commitments clash with the Interactive Paradigm natural to the enterprise.

# The Three Properties in More Detail
In Section\ \ref{the-three-properties} we gave introductory definitions for Self-Sustainability, Notational Freedom, and Explicit Structure. Now it is time to go into more detail and examine them in light of the paradigms we identified.

## Self-Sustainability
To a first approximation, self-sustainability involves being able to evolve and re-program a system, using itself, while it is running. We should expect to see this more as our scope gets larger, say for the entirety of software within any of the paradigms in Section\ \ref{paradigms-of-programs-and-programming}. This is simply because there isn't really anything else that could be the cause of further evolution. It is like the fact that, while any nation's economy might be dependent on other nations, the world economy is a closed system that provides its own inputs.

Therefore we should not be surprised to see self-sustainability in our native Unix Paradigm. What may be slightly novel is that an *individual* Unix system is also largely self-sustainable (its "large scope"), and it is interesting to see how this property is lost on the way down to the "small scope". After we explore these two topics, we will distinguish the *user level* and *implementation level* of programming systems. Then we will conclude with a definition of *innovation feedback,* the key advantage a self-sustainable system has over others.

### Self-Sustainability at the Large Scope
At the "large scope" of Unix, we have individual processes---text editors, compilers, interpreters, debuggers---which change the large-scale system state (files), such as by creating new programs. Some of these processes run *shell scripts* to coordinate this activity, this being the de-facto programming language^[Technically, the various shell *dialects* (bash, csh, etc.) form the de-facto *languages* plural, but this is not important for the point.] at the large scope. In this way, a Unix system is evolved and re-programmed using itself, while it is running. Hence, Unix (i.e. Programming-in-the-large) is self-sustainable. This is congruent with the origins of Unix as a system for programmers.

The matter is complicated by the fact that a small minority of special changes require restarting the system to take effect. However, the spirit of the "while it is running" condition is that the system does not need to be *destroyed and rebuilt from scratch*. Because the "state" of the large scope is mainly files, the destructive operation here is not so much "restart" as perhaps "reset" or "reinstall".

In contrast, for a programming system that exists as a process *within* Unix, its data structures in volatile memory will be permanently lost if it is restarted, and these data structures may well be an important part of its state as a continuously running interactive programming system. Indeed, when we turn our attention to the "small" scope of the Unix Paradigm, we mostly do not see self-sustainability.

### Self-Sustainability at the Small Scope
Compiled programming languages like C++ or Java are used via several different Unix processes. These include interactive ones like text editors, and batch-mode ones like the compiler. Self-sustainability ought to be the analogue of the properties in \ref{precursors-of-self-sustainability} for interactive programming systems. To recap, these were self-hosting compilers, the bootstrapping thereof, meta-circular interpreters, and reflection.

The self-hosting compiler is how self-sustainability manifests in the batch-mode world, where the memory used by the process is disposable and unimportant, hence Change by Re-creation does not cause too many problems. For interactive programming systems, this is often inappropriate or at least highly inconvenient, so it is hard to base full self-sustainability on self-hosting.

In this respect, interactive systems more closely resemble *interpreters* than compilers. The job of a compiler is to generate a new program, which could (in principle) be a *replacement* for the old compiler. Moreover, both the new program and its source code live in the filesystem, i.e. non-volatile storage. The job of an interpreter is simply to *execute* a program, and by default any changes we make to its state as a result of the code we feed it will not survive process termination. Unlike interpreters, interactive processes like REPLs or programming systems are meant to run as long as the user wishes and contain a lot of important state in memory.

Traditionally, the Volatility Split was just forwarded into the user's mental model, making no guarantees about whether work would be saved in the event of a crash and recommending the user to save regularly. End-user applications did eventually implement "auto-save", but this is a feature programmed in to preserve specific user data. For programmers, "auto-save" of runtime state involves infrastructure that takes considerable work to implement. Thus, "Delete By Default" fails and re-creating the system risks important data loss. Even if such infrastructure is present, restarting the system to make a change may interrupt the user experience with an inconvenient delay.

Perhaps we can adapt the interpreter-related precursors, meta-circularity and reflection. Meta-circularity is not too interesting unless the inner interpreter is causally connected to the outer one, via reflection. Therefore reflection is the most suitable precursor to develop into self-sustainability, particularly as it manifests in Self \cite{Self} rather than, say, Java. This could be achieved if reflection is scaled up to encompass as much of the system as possible and if reflected data can be changed rather than simply observed. It would also be necessary to protect any changes achieved through reflection from being lost upon process termination. In short, self-sustainability takes much from reflection in interpreters but combines this with the persistent evolution of the self-hosting compiler.

### User vs. Implementation Levels
For any piece of software, there are two levels:

* The *user level* is where software is used for its intended purpose by its target audience. For example, the user level of Firefox involves browsing websites.
* The *implementation level* is where the software is created and changed in ways unavailable at the user level. As a trivial example, by taking all of the source code of Firefox and replacing it with the code for Hello World, a programmer can change Firefox into a Hello World program.

If the software is a programming system, then this can get confusing: both levels involve programming! Consider this example situation: someone is using Python to write a Hello World program, and the programming system (Python interpreter plus editor) is written in C++. We can view this situation in three ways depending on the "user":

1. We can focus on the user of the Hello World program. The user level is Hello World, while the implementation level involves Python.
2. We can focus on the programmer as the user of Python. The user level involves Python *and* the Hello World program (for testing, debugging, etc.) while the implementation level involves C++.
3. We can focus on a different programmer who works on the Python implementation. The user level involves C++ *and* Python (for testing, debugging, etc.) Even though there is an implementation level below, we short-cut the infinite regress here and leave it unspecified.

In this dissertation, we are interested in *building* programming systems with the Three Properties. This means we occupy the third viewpoint, where we are detached from any particular end-user program that might get created. We see our situation as follows:

* We are using some already-existing programming system (C++ in the example.) We did not create it and we do not expect to be able to change it. We call this the *platform*.
* The programming system we create using the platform is called the *product system* or simply "the system" (Python in the example.)

### Platforms and Substrates
Since we seek to build a product system that is Self-Sustainable, the picture becomes more complicated. The point of Self-Sustainability is to blur the distinction between the implementation and user levels. Not only can the system be used to create ordinary programs, but it can also be used to change itself. We use the term *in-system* to refer to changes made within the product system, by using it as a programming system at its user level.

In the Platonic ideal self-sustainable system, there is no distinction between the two levels at all. In practice, the best we can do is attempt to *minimise* the implementation level to a tiny core:^[In the limit, we will leave the world of software only to hit the stubbornly non-malleable world of physical hardware. Still, search "FPGA" in \cite{COLAs} for its speculations on pushing this as far as it can go (see the captions for Figure\ 3 and Figure\ 14 and page\ 23.)] everything else can be changed in-system. In this case, we call this tiny core the *substrate*. Our task as implementors is to use an established platform to write a minimal substrate that can then support a self-sustainable system. Almost all aspects of the system can then be changed in-system, and the rest must be changed in the substrate using the platform.

To summarise the picture in the self-sustainable case:

* The *platform* is the already-existing programming system that we accept as-is and do not expect to have much control over.
* The *substrate* consists of the code we write for the platform. We exercise control over it, but we do not expect it to be accessible in-system, which is why we seek to minimise it.
* The *product system* is the programming system supported by the substrate. Ideally, a tiny part of it is implemented in the substrate while most of it is implemented in itself.

For ordinary software, there is not much point in distinguishing the platform and the substrate; these two together constitute the implementation level. For a self-sustainable system this distinction is necessary, because the "implementation level" extends into the product system itself (ideally being concentrated there.)

### The Key Benefit: Innovation Feedback
A system that is self-sustainable has an advantage over those that are not: we call this *innovation feedback*. Innovations programmed using the system---useful functions, notations, or tools---can benefit their *own* development as well as the rest of the system. In contrast, for a system whose internals cannot be affected by any program within it, innovations can only be exploited for the system's development by *duplicating* the work at the system's implementation level.

At the large scope in Unix, a text editor may be used to improve its own source code, which can then be compiled into an even better text editor. The shell interface, graphical interfaces and all tools are just *programs* which can be replaced with newly compiled improvements, all using other Unix programs. Therefore, a Unix system is not limited to improving detached *separate* distributions of programs destined for a different user, but naturally improves itself as well.

\joel{Stefan warning: Hard to read}
When put this way, it sounds obvious: "of course computer software is used to improve computer software!" Yet how different it is at the "small" scope: the very same text editor *on its own* cannot compile its improved replacement. A Python interpreter written in C++ may empower us to create useful tools, but to improve the interpreter itself we need to inhabit the world of C++, in which our Python tools are unavailable.

From our presentation, it may appear that self-sustainability is a peculiar special case that is naturally hard to achieve. Yet the world of software as a whole is self-sustainable, as is an individual Unix system (large-scope programming.) We conjecture that self-sustainability is a natural *default*, one that was *prevented* by the historical contingency of Unix enforcing its peculiar model on small-scope programming. Of course, we must still accept the Unix Paradigm as a given, and accomplish the tasks of this dissertation on that basis.

## Notational Freedom
In Section\ \ref{precursors-of-notational-freedom} we mentioned the maxim "Use The Right Tool For The Job". This is a noble aspiration, but one that is not currently fulfilled. At the "large scope" of the Unix paradigm, there is a vast array of programming languages specialised for different jobs. Yet we saw some barriers to Polyglot Programming, such as the need for inter-process communication (IPC.) As we scale down to *within* a single process and forego IPC, data sharing between languages gets thornier.

The ideal is where a component at any scale can be expressed in a *notation* that is particularly suited to it. Here, we will go through the intermediate stages of *syntactic* and *linguistic* freedom before arriving at full notational freedom.

### Syntactic Freedom
We noted in Section\ \ref{precursors-of-notational-freedom} that, within a single file, combinations of different languages are occasionally possible. Yet these are fixed sets, pre-approved by language designers and set in stone for all contexts and users. We could call this property *syntactic^[Programming languages differ by semantics as well, but for Notational Freedom we are only interested in the surface notational aspects. Infrastructure for supporting this would already be close to supporting freedom of semantics, as shown by COLA's mood-specific languages.] diversity*, where a language named A additionally permits syntaxes B, C, and D in certain situations. This goes a small way towards "Use The Right Tool For The Job", but what is conspicuously missing is the ability for the *programmer* to decide which syntaxes to use, and where, based on their local context.

This would be syntactic *freedom* beyond pre-approved diversity. It is not unreasonable to expect that a single function, or perhaps even a single line of code, might be best expressed in a different language to the rest of the code, in a way that the language designer cannot predict.

We will use a running example as we build up through the stages to full Notational Freedom. A common occurrence in scientific or graphics programming is a few lines of mathematical operations; it would be convenient to bring these closer to familiar mathematical notation instead of a butchered ASCII approximation. Suppose we start with the following unfortunate notation for a formula: 

```
vec_a.mul(cos(ang_b/2))
     .add(vec_b.mul(cos(ang_a/2)))
     .add(vec_a.cross(vec_b))
```

Syntactic freedom would mean being able to see this situation and specify a local syntax that lets us rewrite it as:

```
cos(ang_b/2) vec_a + cos(ang_a/2) vec_b + vec_a × vec_b
```

It could be objected that using `+` instead of `.add()` is already supported in some languages, such as C++ with its operator overloading. We would then point to the `×` Unicode character and ask for a language that permits this infix operator. We might be met with Haskell, famous for its arbitrary user-defined infix operators that may include Unicode symbols. In this case we would point at the juxtaposition of the two parts of the first term to denote multiplication. Are there any languages that permit overloading of whitespace as an infix operator? Supposing there were, there would be other cases for other problem domains where piecemeal allowances in what can be overloaded is not enough. Custom glyphs or even the symbols available in \LaTeX{} but not in Unicode could suffice as an example, but we will not belabour this further.

We are aware of syntactic freedom in two places: COLA's "mood-specific languages" (MSLs) and Lisp's Reader Macros. This is a significant step in the right direction, but the syntax of strings as defined by formal grammars has limitations. It can only see two directions (left and right) and has no notion of display variations like typefaces, weights, sizes, colours and so on. This encourages us to edit expressions as lists of characters without support for nested boxes or other interfaces that can be useful. In other words, it keeps us in the text editor interface with its Implicit Structure and associated problems (see Section\ \ref{explicit-structure} below.) If we go on to lift these restrictions, we arrive at *linguistic* freedom.

### Linguistic Freedom
By this, we mean freedom to represent expressions as arbitrary written *language* rather than restricted *syntax.* In the physical world, written language takes many forms which are hard to digitise in their full detail---we don't expect our personal handwriting to be adopted as an internationally-recognised font. Yet even in computing, written language takes a variety of forms and supports a variety of display characteristics. In programming, these are stripped away and we generally only have formatless *syntax* to work with.^[Technically, syntax highlighters make keywords bold and add various colours, but these are fixed rules applying to display only. The point is that it is not possible to create a "bold variable" or a "red function".]

In our running mathematical example, there is a very good illustration of the step up to linguistic freedom in the form of publishing-standard mathematical notation (and conveniently for this thesis, a core capability of \LaTeX):

$$\cos\left(\frac{b}{2}\right)\mathbf{a} + \cos\left(\frac{a}{2}\right)\mathbf{b} + \mathbf{a} \times \mathbf{b}$$

This exhibits the following improvements that do not fit into the "syntax" technologies:

* Vertical layout of fractions
* Replacing the `vec_a` and `ang_a` with $\mathbf{a}$ and $a$, distinguished by weight
* No restriction to fixed-width characters or spaces

Beyond these display characteristics, it also leaves open the possibility of an editing interface not restricted to character-by-character string operations. For \LaTeX{} mathematics, we must often write verbose textual source in the manner of Section\ \ref{syntactic-freedom}'s example and render it into the better notation. Yet there do exist interfaces for editing \LaTeX{} mathematics more directly, such as those in Mathcha^[\url{https://www.mathcha.io/}] and Desmos^[\url{https://www.desmos.com/}]. Linguistic freedom permits such "structured" or "projectional" editing as an option. This brings to mind JetBrains' MPS \cite{MPS} as the only system of which we are aware that offers Linguistic Freedom.

For our running example, we have plausibly reached the Right Tool For The Job at this point. In general, however, we still regard "language" as too narrow of a constraint. By this, we simply mean notations that consist of repeated glyph shapes laid out in a (mostly) linear manner. This is different to other uses of the term: "visual language" may not include text at all, but uses the "language" term for the arbitrary arrangement of elements. Under this latter meaning, the "mood-specific language" idea has all the generality it deserves. Nevertheless, in programming, we think the term "language" runs the risk of mentally excluding graphical or interactive possibilities. To ensure they remain, our use of the word "language" will stick to denoting mostly-linear renderings of glyphs and we shall use words like "notation" or "interface" for the fully general extension.

### Full Notational Freedom
Here, we wish to have unrestricted support for graphics and interaction. In our mathematical example, full notational freedom would support an interactive 3D visualisation of the vectors involved if that was what the programmer desired. Language isn't everything---diagrams and pictures are sometimes the form in which a problem or solution is delivered, and programmers ought not to be forced to describe pictures using words.

We should emphasise that we are using the terms "notation" and "interface" interchangeably; we are not just talking about static pictures but dynamic entities on a screen. The "text editor" interface is one such example. One could use a text editor to work on the hex code `0xff00ff` representing the colour magenta. Alternatively, one could use a colour picker interface.

The key thing is that this property is called Notational *Freedom*, rather than something like "Optimal Notation". We recognise that different notations suit different purposes and respect the art of developing them as a separate area of expertise, which we do not claim for ourselves. The idea is to support the *subjective* productivity of the programmer, who we assume is best equipped to judge the appropriateness of notations for herself. This property is about *supporting* the usage of different notations for different contexts.

### What Do We Mean By "Support"?
It is true that there is no such thing as a free lunch; we do not go so far as to suggest that the system should turn a natural language description into a working interface (recent advances in AI notwithstanding.) So long as the programmer is willing to do the necessary work to program a new notation, this should suffice to use it in harmony with all the others.

However, the Unix Paradigm and text editors impose an *additional* "tax" in terms of effort beyond this reasonable standard. In the best case, it might have some plugin architecture, while at worst it may require forking the editor's source code. The actual work involved in creating the notation might be very small, but it will be dwarfed by the task of getting the editor to accept it.

Notational Freedom means the system was designed *without* the assumption that there will only be one notation and lacks these taxes to the extent possible. In short, by "support" we mainly mean the removal of artificial *barriers* that have been put in the way of mood-specific notations.

## Explicit Structure
As we remarked in Section\ \ref{precursors-of-explicit-structure}, Explicit Structure refers to the sense of working with data *directly* rather than through some other medium. To go into more detail, it will be useful to split the life-cycle of a data structure into two halves:

* On the *producer side*, the data structure is created or edited using some interface.
* On the *consumer side*, a programmer is writing code that uses the data structure.

Explicit Structure is hard to define positively because it is the default state of affairs across much of computing, with programming being the notable exception. On the producer side, explicit structure is illustrated by a vector graphics editor like Inkscape: one simply draws a diagram with shapes and saves it as an SVG file. On the consumer side, Explicit Structure looks like a programmer navigating through named parts of the structure:

```
svg.root_nodes[1].children[2].children[0].fill_color = '#ff00ff';
```

Explicit Structure is perhaps easier to define negatively, as a *lack* of Implicit Structure. Implicit Structure is present when we use *plain text* as a communication or storage medium: the structure can only be navigated after parsing the string. The problem with plain text is that it fuses together two independent concepts: what we could call *presentation* (how one reads and modifies the data) and *representation* (how the data is stored in memory or on disk). For example, the AST of a program *could* be stored in its tree form and *presented* as indented text. But what we do instead is serialise the text, store that, and parse it back before we are able to work with it.

This thesis approaches the default Implicit Structure of programming with skepticism. We direct the reader to the fuller arguments from the authors of Subtext \cite{Subtext} and Infra \cite{Infra}, but we will summarise the most important points here and offer some of our own. 

### Language Can Be Stored Differently to a Character List
Language always contains *structure*: English paragraphs contain sentences, containing clauses, containing words. Natural language, like English, is typically entered into a digital medium only to be poured out again at some other end, like photo uploads; normally, the computer does not need to dive into the structure at all. On the other hand, for programming languages, the AST is the entire point.

Despite this, programming language source code is universally stored as a sequential recording of keystrokes, rather than as the tree or graph that it represents. This has the downside that every program that consumes or transforms the code must recover (parse) this structure out, discovering any mistakes only at this point of consumption (since these were just recorded with the other characters.) This is like storing vector graphics diagrams as arrays of pixels and using Computer Vision to haphazardly recognise shapes and lines: unnecessary work to recover information that was thrown away at creation time. \todo{Stefan: bad analogy}

\todo{Stefan: this is naive}
More unfortunate work results from having to "escape" characters that have been reserved to denote structure instead of their literal selves. In the worst case, the storage of language as character lists is largely responsible for the class of attacks known as SQL injection. This would not be possible with SQL commands represented as trees containing holes to be filled with user-submitted strings.

Consider the common practice of embedding SQL commands in the source code of various languages. In C#, these are forced into the syntax of the host language as "embedded queries", yet a programmer may prefer to use SQL syntax directly as part of the source. The traditional path-of-least-resistance to achieving the latter was to have SQL code inside program strings, which created significant security risks. This is by no means intrinsic to having SQL source be what the programmer *types* or *sees* (see Section\ \ref{explicit-structure} below.)

### Digital "plain text" is not inherently human-readable
This argument is made best by Hall\ \cite{Infra}, p.\ 14:

> The critical observation is that software infrastructure is heavily involved in supporting the human-readability of text. It is not the case that the bit sequences of UTF8 or any other text encodings are somehow intrinsically understandable to a human. An application interprets the bytes as character codes as per a known standard, which are mapped to glyphs in a font, and rendered to a grid of pixels. This chain of interpretation and transformation starts with clusters of electrons and ends with clusters of photons before the human nervous system takes over. The point being that there is still a necessary software layer performing a transformation in the middle.

> electrons $\rightarrow$ bits $\rightarrow$ charactercodes $\rightarrow$ glyphs $\rightarrow$ pixels $\rightarrow$ photons

> “Human readability” just colloquially implies that it is a standard encoding understood by most text editors. The sense of inherent readability merely comes from the ready availability of tools that render ASCII and Unicode. One can assume that a text editor or some text rendering infrastructure exists in the target system. Therefore, any encoding could technically achieve the same ‘human readable’ status as ASCII if it and its editors were general-purpose enough to warrant an equally ubiquitous install base. Thus, there is an opportunity to expand or upgrade the realm of what can be considered human readable.

It can be tempting to defend text files as human-readable in contrast to binary files which are not. However, as Hall's argument shows, this is mere *status quo* bias: all of the "human-readability" of a text file is due to the wide availability of text editors. By the same criteria, binary PDF files are human-readable owing to the ubiquity of PDF readers. In other words, the difference between plain text and "binary" data is not so much an essential difference of two kinds but simply a difference in tool availability.

### We Study the Spherical Cow
Text usually functions as a *medium* or rendering of something that is not inherently text. In the first place, text was invented to record speech made by human beings. In programming, text is used as a *proxy* for a nested tree-like structure, but is *not the structure itself.* Therefore, to study a programming idea like self-sustainability, it is unfortunate to have the contingencies of text representation "getting in the way" of studying the idea itself. Therefore, even though a real-world programming system may use text, we wish to avoid this obscuring layer for much the same reason as a physicist studies a frictionless sphere in a vacuum instead of, say, a cow in a field. We want to not be distracted with air resistance and complex shapes, so as to focus on the property we are interested in; future work can then add the practical complexities back in again for a more realistic model.

# COLAs

The one system that directly influenced our work is the Combined Object Lambda Architecture or COLA\ \cite{COLAs}: a small but expressive starting system that could be self-improved into anything. The system is described as a mutually self-implementing pair of abstractions: a structural object model (the "Object" in the acronym) and a behavioural Lisp-like language (the "Lambda"). The system aims for maximal openness to modification, down to the basic semantics of object messaging and Lisp expressions.

The two key features we see in the COLA idea are *self-sustainability* and *mood-specific languages.* Self-sustainability is straightforwardly inherited from the Smalltalk tradition and amplified. It allows for innovation feedback that the authors refer to as _internal evolution_:

> Applying \[internal evolution\] locally provides scoped, domain-specific languages in which to express arbitrarily small parts of an application (these might be better called *mood-specific* languages). Implementing new syntax and semantics should be (and is) as simple as defining a new function or macro in a traditional language.

These Mood-Specific Languages (MSLs) are a continuation of the more familiar idea of Domain-Specific Languages (DSLs.) Instead of only being able to make changes to a mass of source code written in the same language, a COLA is supposed to permit easy proliferation of languages optimised for individual sub-problems. Where DSLs may limit the minimum size of a "domain" to a module or file or even a function, the MSL scope lifts this restriction. An example of such an MSL might be expressing a single line of code with mathematical notation instead of the usual monospaced ASCII subset. The language half of a COLA is designed to make it feasible for a programmer to do this sort of thing on a whim; encouraging "the right language for the job" no matter how small the context, in a way that normal language infrastructure makes too costly to be worthwhile.

This is a step in the right direction, but it is incomplete: we dream of mood-specific *notations* more generally, which we called *notational freedom* in Section\ \ref{notational-freedom}. Moreover, MSL support is presented in the paper \cite{COLAs} as a pipeline of traditional *batch-mode transformations* such as parsing, analysis, and code generation. Similarly, it presents its bootstrapping process in terms of batch-mode transformations of various source code files. This steeps it in the Unix Paradigm (Section\ \ref{the-unix-paradigm}) and feels like it obscures the interesting ideas (recall Section\ \ref{we-study-the-spherical-cow}.)

We would rather have the ability to gradually *sculpt* a system into a self-sustainable state, interactively, through a combination of manual actions and automatic code. This requires that the system should be conceived primarily through a graphical interface, yet the COLA design does not provide guidance in this respect. It is possible to see how a COLA's various languages and components work together as a sort of self-sustainable command-line REPL, but less easy to see how its text-centric approach may apply to arbitrary graphical interfaces.

We mention COLA because it was the most important influence on the work in this thesis. It gestures so tantalisingly at a way to escape the Small Scope of the Unix Paradigm, yet only while making puzzling concessions to it that seem to limit its ambition. We will refer to COLA repeatedly for comparison and offer some analysis of it on our own terms.

# Conclusion

However, before we embark on this journey, we must first go into a little more detail on programming systems in general and establish how we will evaluate according to the Three Properties. This is the topic of the next chapter.
