\chapter{Background}

In order for the later chapters to make sense, we begin by covering the relevant background concepts and research. This chapter is organised by themes because each one emerges as a reaction to the previous.

\joel{ Stories (reminder)
1. Bootstrapping self-sustaining systems from a starting point
2. Fixing/freeing the COLA work by lifting the text restriction
3. Re-building the software stack with the benefit of hindsight and better tech.
}

\todo{Why the 2 properties?}

\joel{
To begin we must explain sys vs languages.
Now we can cover the COLA programming system design including the Id object model.
This introduces self-sustainability, bootstrapping, and notational freedom.
On the latter: here's why COLA's text reliance is a liability.
But why is all this so hard? / big picture: Against Conv Wisdom.
}

To start with, this thesis is about building programming systems. In order for this to make sense, we first need to introduce "programming systems" and explain how the concept is related to that of a programming language.

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

## The Legacy of VPRI
With the "programming systems" frame established, we can properly consider some related work downstream of Smalltalk. It emerged from the now-retired Viewpoints Research Institute (VPRI), on whose foundations this thesis is built. VPRI aimed at creating "fundamentally new computing technologies", which is particularly visible throughout the 6-year project known as "STEPS towards a new computing" \cite{Steps08,Steps09,Steps10,Steps11,Steps12}. The aim was to fully replicate a familiar graphical end-user operating system with applications in under 20,000 total lines of code. Such an ambitious goal provided the constraint needed to force innovation in taming complexity, motivating widespread use of domain-specific languages and investment in the "meta"-level. Several writings describing programming systems, or parts of them, came out of STEPS, but the most important for this thesis is the COLA.

### COLAs

The one system that directly influenced our work is the Combined Object Lambda Architecture or COLA\ \cite{COLAs}: a small but expressive starting system that could be self-improved into anything. The system is described as a mutually self-implementing pair of abstractions: a structural object model (the "Object" in the acronym) and a behavioural Lisp-like language (the "Lambda"). The system aims for maximal openness to modification, down to the basic semantics of object messaging and Lisp expressions.

The two key features we see in the COLA idea are *self-sustainability* and *mood-specific languages.* The self-sustainability allows for innovation feedback that the authors refer to as _internal evolution_:

> Applying \[internal evolution\] locally provides scoped, domain-specific languages in which to express arbitrarily small parts of an application (these might be better called *mood-specific* languages). Implementing new syntax and semantics should be (and is) as simple as defining a new function or macro in a traditional language.

We wish to have mood-specific *notations* more generally, which we call *notational freedom.* A self-sustainable system is brought into existence in a way that is analogous to self-hosting languages; this is called *bootstrapping*. COLA presents its bootstrapping process as if it were such a language, which is odd considering it is a system. (text stuff goes here?)

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

Instead of only being able to make changes to a mass of source code written in the same language, a COLA is supposed to permit easy proliferation of languages optimised for individual sub-problems. Such languages are typically known as domain-specific languages or DSLs, but the STEPS project sees a range of granularity all the way down to “mood-specific languages” (MSLs). An example of an MSL might be expressing a single line of code with mathematical notation instead of the usual monospaced ASCII subset. The language half of a COLA is designed to make it feasible for a programmer to do this sort of thing on a whim; encouraging “the right language for the job” no matter how small the context, in a way that normal language infrastructure makes too costly to be worthwhile.

\cite{Nile,Gezira}

### Bootstrapping

In the context of programming languages, the compiler or interpreter for a language can be implemented in the language itself. This is known as *bootstrapping* and works as follows:

1. We design a novel language *NovLang* that we want to use.
2. We write *NovLang* source code that will compile other *NovLang* source code into a runnable program.
3. We hand-translate this to C/C++ and build a compiler for *NovLang*.
4. We run this to compile the *NovLang* source code from step 2.
5. We obtain a runnable compiler for *NovLang*, which was written in *NovLang* and is now "self-hosting".
6. We can now discard the C/C++ code.

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

The problem with "plain text" is that it fuses together two independent concepts: what we could call *presentation* (how one reads and modifies the data) and *representation* (how the data is stored in memory). For example, the AST of a program *could* be stored in its tree form and *presented* as indented text. But what we do instead is serialise the text, store that, and parse it back before we are able to work with it.

This thesis approaches the default text-centrism of programming with skepticism. We direct the reader to the fuller arguments from the authors of Subtext \cite{Subtext} and Infra \cite{Infra}, but we will summarise the most important points here and offer some of our own. 

Claims:
* *Language isn't everything.*
* *Language can be stored differently to a character list.*
* *Digital "plain text" is not inherently human-readable.*

\cite{Infra}
\cite{Subtext}
[schematic tables](http://www.subtext-lang.org/OOPSLA07.pdf)

### We Study the Spherical Cow
Text usually functions as a *medium* or rendering of something that is not inherently text. In the first place, text was invented to record speech made by human beings. In programming, text is used a *proxy* for a nested tree-like structure, but is *not the structure itself.* Therefore, to study a programming idea like self-sustainability, it is unfortunate to have the contingencies of text representation "getting in the way" of studying the idea itself. Therefore, even though a real-world programming system may use text, we wish to avoid this obscuring layer for much the same reason as a physicist studies a frictionless sphere in a vaccuum instead of, say, a cow in a field. We want to not be distracted with air resistance and complex shapes, so as to focus on the property we're interested in; future work can then add the practical complexities back in again for a more realistic model.

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
