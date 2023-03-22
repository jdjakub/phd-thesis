\chapter*{Background}

# Proposal

My interests here follow two strands: meta-circularity, and alternatives to plain text. I have already made some practical investigations in both of these areas.

## Meta-circularity
Meta-circular languages claim to be implemented "in themselves". This means that there is considerable freedom to alter syntax and semantics *without* the usual "stepping up the toolchain ladder", but instead in the same source code or runtime environment being used.

The paper\ \cite{orom} demonstrates a minimal object system called "Id", of 3 object types and 5 methods, which can be used to extend itself --- even as far as arbitrary single/multiple inheritance, mixin, or traits semantics. It forms part of the authors' wider work on "COLAs"\ \cite{colas}, which draws heavily from two famously meta-circular languages: LISP and Smalltalk. They propose a fusion of the "even more late-bound Smalltalk" data model described in\ \cite{orom}, and an "even more late-bound LISP" described in\ \cite{oecm}.

A term used frequently in the COLA work, from the field of compilers, is "bootstrapping". This is the gradual building-up of a system, from some small starting point (the *substrate*), using itself. For example, we can use machine code to write the first assembler. Then, automatically-translated assembly can implement the first high-level compiler. The next language ecosystem is then born from the previous, and so on. This need not start from machine code --- "self-hosting" compilers start from whatever is available.

There are other, fully-fledged Smalltalk derivatives:  [Squeak](https://squeak.org/), [Lively Kernel](https://lively-kernel.org/), [Pharo](https://pharo.org/), [Fizzygum](http://fizzygum.org/), and the [Io language](https://iolanguage.org).

Of course, my interest is in meta-circularity *as an intuitive alternative* to the "toolchain ladder". Stephen Kell's work\ \cite{kell-mmm}, on the boundaries between software artefacts, could provide a valuable counterpoint to investing too much in this view of the problem. One could argue that a meta-circular system defines its own little world, in competition with the host OS and ultimately aiming to replace it. This picture is rather different to one in which different meta-circular ecosystems can all co-exist and use each other's features.

The Open Authorial Principle\ \cite{oap} and Entanglements\ \cite{entangle} resemble the COLA work in aims and language ("open" / "closed"), if not in method. A  critique\ \cite{entangle-critique} of the latter is available, providing insights on the concepts of "substrate" and "open" systems. Its depiction of use / development as a relationship between "scope of change" and "difficulty" is relevant for the proposed Task 1.

## Alternative substrates to character lists
As mentioned, the standard interface of automation is *language*, specifically its rendering as byte-lists. This is the case regardless of what exactly is being automated: whether transformations of other byte-lists, or movements of a 3D skeletal model. While it may have made sense in the day of the Model 33 Teletype, it can feel hopelessly anachronistic developing this way for modern use cases in which graphics, layout and direct manipulation are ubiquitous.

Ivan Sutherland's Sketchpad\ \cite{sketchpad} was an early exploration of human-computer interaction via geometry instead of language. It is notable for the originality of the concept in 1962, the emphasis on constraint solving, and its facilities for graphical abstraction.

The Geometer's Sketchpad is a "programmable geometry" prototype amongst other relevant projects in the book Watch What I Do\ \cite{wwid}. The [Briar](https://graphics.cs.wisc.edu/Papers/1994/Gle94/constraints.pdf) drawing program adds constraints on top of direct manipulation. Palimpsest\ \cite{palimpsest} is a non-textual environment for programming layer-based graphical compositions.

Bret Victor has presented video demonstrations\ \cite{ddv} and interactive [prototypes](http://aprt.us/) of programmatically animating shapes primarily via shapes.

Infra\ \cite{infra} presents an extensible structure that does away with constant parsing and serialisation. Worth mentioning is its Patch computation model (Section 6). Patch nodes give Infra structures "homoiconicity" in that evolutions of state (code) are represented in the same medium as the state itself. Infra could be a better substrate than bitstrings.

The Subtext project\ \cite{subtext} contains many years of serious research at doing better than character lists for specifying computation. One early innovation was the use of 2D ["schematic tables"](http://www.subtext-lang.org/OOPSLA07.pdf) to simplify complex conditional logic.

## My own work
[BootstrapLab](https://programmingmadecomplicated.wordpress.com/2018/01/18/3-hacking-together-orom-domctrlshiftj/) began as a table-based Web prototype of the Id object system. It let me work with a live analogue of the authors' C code, without constantly typing commands to read and write the state of objects. It was a success at helping me understand their proposed "initial version" of a meta-circular system. It was, however, not clear how to scope the task of implementing the system "in itself", because of the rich HTML substrate and GUI.

Due to this experience of "meta-circularity via text" outside of the terminal, I explored some avenues of text-free meta-circularity. Progress was more ad-hoc and still without guiding theory, so it was necessary to make some [independent steps into](https://programmingmadecomplicated.wordpress.com/2018/09/13/what-am-i-aiming-for-again/) an interaction-first model of computation, resembling the approach in Entanglements.

## Other
My online collaborator Dan Cook has been developing ideas and prototypes in these areas. His explanation of the "clay" idea can be found [here](https://www.cemetech.net/forum/viewtopic.php?p=270092#270092).

Finally, the phrasing "production of software by means of software" seems an apt way of describing this whole topic. It is a nod to the [work](https://archive.org/details/SraffaP.ProductionOfCommoditiesByMeansOfCommodities) of the Italian economist Piero Sraffa. Despite sharing an "input-output" recipe model, the connection might not be anything deep, since the core of Sraffa's work is quantitative, and it isn't clear how to quantify things like "amount of C compiler".

# Mini-thesis
This section covers the literature situating our project at the intersection of several sub-fields, some of which are well established, while others arguably exist implicitly and un-named. It is split into a set of "core sources" which pertain to the work done this year, and other sources which are relevant for subsequent work.

## Core Sources for the Work So Far

### The Legacy of VPRI
Possibly *the* major instigator of our research programme lies in the output of the (now retired) Viewpoints Research Institute, or VPRI. One of its aims (among others) was to create "fundamentally new computing technologies", which is particularly visible throughout the 6-year project known as "STEPS towards a new computing".

The STEPS reports \cite{steps08,steps09,steps10,steps11,steps12} chart its annual progress between 2006 and 2012. The aim was to fully replicate a familiar graphical end-user operating system with applications---in under 20,000 total lines of code. This ambitious goal provided the constraint needed to generate innovative ways of "factoring" complexity in an efficient manner. Among the ways this manifested is a widespread use of domain-specific languages, and a persistent drive for functionality at the "meta"-level.

### COLAs and bootstrapping
The STEPS team was led by Alan Kay and included several of his colleagues, having previously collaborated on (amongst other things) the Object-Oriented *Smalltalk* system. Kay is a believer in (his version of) Object-Orientation as a tool for tackling software complexity, drawing inspiration from cellular biology and the Internet.

Thus, one key "factor" carved out of the problem space was a uniform and compact way to implement various Object-Oriented relations: objects, classes, meta-classes, and beyond. A minimal object model called "Id" underlies all data representation and expresses the above concepts in a general way \cite{OROM}. Most of the work so far has been concerned with implementing Id as a visual and explorable representation, so see Section~\ref{work-so-far} for details on Id itself.

In the paper \cite{COLAs}, this object model is revealed to be a *structural* half ("Pepsi") of a whole. It is complemented by a Lisp-like programming and transformation language ("Coke") resulting in a Combined Object Lambda Architecture, or COLA.

A COLA is intended as a base substrate on which languages, applications and systems can sit. However, unlike a traditional Operating System, a COLA is modifiable at a fine granularity, using itself, *at runtime*, and via high-level abstractions. It is intended to be "bootsrapped" into a self-sustaining, or "self-implementing" state from an initial language (such as C++), which can then be discarded once the process is complete.

### Domain-specific to "mood"-specific languages
Instead of only being able to make changes to a mass of source code written in the same language, a COLA is supposed to permit easy proliferation of languages optimised for individual sub-problems. Such languages are typically known as domain-specific languages or DSLs, but the STEPS project sees a range of granularity all the way down to "mood-specific languages" (MSLs).

An example of an MSL might be expressing a single line of code with mathematical notation, instead of the usual monspaced ASCII subset. The language half of a COLA is designed to make it feasible for a programmer to do this sort of thing on a whim; encouraging "the right language for the job" no matter how small the context, in a way that normal language infrastructure makes too costly to be worthwhile.

The Abstract Syntax Tree (AST) level is supported by a general "tree transformer" architecture, which supports both "compile-time" programming (compilation, macros) as well as runtime evaluation of expressions (by transforming to lists of machine instructions) and is inspired by Lisp \cite{OECM}. The concrete syntax level is supported by OMeta \cite{OMeta}, an extension of Parsing Expression Grammars for implementing syntax and semantics with minimal noise^[The OMeta parser is also implemented via its own grammar.].

This is all terribly impressive and intresting in its own right. However, in a project dedicated to critically examining assumptions about the nature of how computing and programming must work, it is surprising that the reliance on "text" or "language" seems not to have been questioned. We find the "domain-specific at many levels" idea appealing, but would prefer to consider "representations" in general, rather than language specifically.

### Beyond Mere Text
The "language" that we consider too narrow a constraint consists of renderings of text. This is as opposed to other uses of "language" in areas such as design: "visual language" may not include text at all, but uses the "language" term metaphorically for the arrangement of elements. Under this latter meaning, the "domain-specific language" idea has all the degrees of freedom it deserves. Nevertheless, our use of the word "language" will stick to denoting renderings of *text glyphs* and we shall use words like "representation", "notation", or "substrate" for the fully general extension.

A convincing argument that language is only one of many ways to work with ideas and software entities has been presented by Bret Victor in his essays and talks. The main work that won us over is the "Drawing Dynamic Visualisations" demo \cite{DDV}, in which he shows how a variety of data-driven graphics (such as bar charts) could be "programmed" to match a set of data, in a mostly visual way.

While Victor focuses on *sketching* alternative ways of programming, Boxer \cite{boxer} is a complete proof-of-concept implementation of a more visual programming system. More specifically, it is a "reconstructible computational medium" consisting of text and graphics inside nested, labelled boxes. True, the way computation happens is through textual code *inside* the boxes. However, Boxer does at least succeed in replacing the *structural* uses of text (for organising code, or describing data structures) with an arguably more appropriate, two-dimensional, form. While there are more advanced and subtle aspects to Boxer, the bulk of its relevance to our work so far comprises its nested boxes, which formed the basis of our Id implementation.

In full truth, this was not a conscious influence so much as convergent evolution; nevertheless, it seems sensible to act *as if* we had deliberately sought to derive from Boxer, and to incorporate more of its innovations into our system.

## Pertaining to Future Work

### Towards a meta-circularity or "bootstrapping" theory
Actual theory or generalisations on the topics of "self-implementing" or "self-starting" systems seemes scant at first. There is a "Meta-Helix" paper \cite{meta-helix} addressing a specific problem: namely, the inevitable confusions resulting from the ambiguity and complexity of self-reference and self-improvement. The essay "Bootstrapping A Simple Compiler From Nothing" \cite{bootfrom0} provides a practical walkthrough of implementing a specific artefact, which could at least serve as an example to analyse.

Fortunately, there does appear to be one extremely promising source here: *Procedural Reflection in Programming Languages* \cite{PRinPLs}, a doctoral thesis cited in the initial STEPS proposal. It concerns itself with demonstrating, by example, how computational systems *in general* might be made to reflect on themselves, but with a parallel development of explicit theory. It earns further credibility by opening with acknowledgement of the linguistic, philosophical and cognitive difficulties one meets when simply trying to nail down what the task even means.

Optimistically, the contents of this document provide precisely the theoretical framework we seek in our proposal (Section~\ref{task-1}), freeing us from the task of developing one ourselves. If this is the case, then much insight can be obtained by applying such theory to analyse systems like Id, and to create or predict novel ones.

### Visual / End-User Programming
Another doctoral thesis, Sketchpad \cite{sketchpad}, is evidence that drawing shapes contains unexpected expressive and abstractional power. Sketchpad was innovative in many respects, from proposing graphical interaction in the first place, to its generalised and extensible constraint system. The achievement that grabs our attention, however, is simply that the author drew glyphs for the letters of the alphabet, after which he could type them into labels. This inverts the usual programming reality (writing shapes as textual descriptions), suggesting that it might be possible to treat text as something to *build in-system* instead of as an axiom underlying it.

The book *Watch What I Do* \cite{wwid}, and its sequel *Your Wish Is My Command* \cite{ywimc}, compile many reports on Programming By Demonstration (PBD) both in research prototypes and in industry. They, combined with the "Visual Programming Codex" \cite{vpcodex} and the "Survey of Visual Programming" \cite{vpsurvey}, form a "map" of what has been tried before in this area. The "Gallery of Programming UIs" \cite{galleryUIs} does perhaps the same thing, but for the *look-and-feel* of visual systems---which can still be revelatory of deeper matters than mere aesthetics.

Several more specific systems deserve a mention. In addition to the previously-mentioned Boxer, Forms/3 \cite{forms3} constructs computation via boxes. Palimpsest \cite{palimpsest}, a purely-visual means for expressing layered image manipulations, deserves investigation in the same vein as Sketchpad. *Hopscotch: Towards UI Composition* \cite{hopscotch} presents a UI framework in opposition to the traits embodied in Smalltalk's class browser, and contains some theoretical depth to their approach.

A pair of sub-projects from STEPS, Nile and Gezira \cite{nile,gezira}, comprise a unique and innovative approach to vector graphics. Nile is a declarative, Haskell-inspired stream processing DSL using Unicode mathematical symbols; it is used to concisely write Gezira, a fully functional 2D vector graphics rendering pipeline. This work is interesting because it builds on a deliberate "mathematisation" of the rendering problem, in contrast to the traditional "imperative algorithms plus mass of special cases" approach; the complexities of the latter are seen as ossified optimisations from an era of scarce compute and memory. The idea is that on modern hardware (i.e. of the late 2000s) we really ought to be able to express the problem in its "pure" form, i.e. to colour a given pixel as a function of the scene shape data, which is inherently parallelisable. Unfortunately, the author was unable to complete this work; as a result, it is ill-documented and somewhat arcane to understand. Nevertheless, it could be worthwhile to reverse-engineer, and other interested parties are making their own efforts.

### Text as *presentation*, not *re*presentation
The Infra Structure thesis \cite{infra} proposes a middle ground between the extremes of the pure-visual fringe and pure-textual mainstream.

The problem with "plain text" is that it fuses together two independent concepts: what we could call *presentation* (how one reads and modifies the data) and *representation* (how the data is stored in memory). For example, the AST of a program *could* be stored in its tree form and presented as indented text. But what we do instead is serialise the text, store that, and parse it back before we are able to work with it.

Interface and storage ought not to be artificially locked together. Infra rightly recognises the artifice of having to choose *between* a "human-readable" format (text) and a "binary" format. Its solution is a general and flexible system for giving stored binary data human-friendly interfaces: it supports "sub-structure, inter-reference, dynamic dependencies, abstraction, computation, and context (metadata)". The expression of arbitrarily-nested annotation of metadata is impressive enough, but the facility for including *computations* (with side effects sandboxed until user approval) is particularly interesting. It could serve as a study of homoiconicity (representing code in the same form as data) and possibly even meta-circularity.

Another project, Subtext \cite{subtext}, is similar to Infra in many ways. Its literature includes effective communication of the problems with the *status quo* and introduces novel "copying-based" models of computation. It has also developed visualisations specifically for code (such as for complex Boolean conditionals), which would be rather too narrow for Infra. However, the emphasis of Subtext is as an experiment in programming system design, while Infra directly aims to be a stable-enough, ready-for-use basis for aspects of computing.

### Reversible computation
One common cause behind much programming, usability and debuggability frustration can be suggested as *irreversibility*. Specifically, the majority of operations in computing tend not to preserve information that allows them to be *automatically* reversed.

Compilers are one example of this, even with debugging information enabled. Another is illustrated by Bret Victor: lines of code might rasterise a shape into pixels on the screen, but these pixels have no record of which code generated them---something that would make debugging procedural graphics much easier. Further, modifying the code changes the shapes---but not the other way round.

One approach that addresses this issue is the work of Perera et al. \cite{fpexplain}. It uses the notion of "program slicing" to design a functional language in which programs produce an explanatory trace of computation. This "self-explainability" was a desire seen throughout the STEPS project and is worthwhile independently of the difficulties of irreversibility.

Otherwise, there is an area of *Bi-directional* programming directly addressing this problem. Boomerang \cite{boomerang} is an OCaml-based language revolving around *lenses*, an abstraction for reversible operations (inspired by the reversibility of light in optics). The bulk of the applications seem to be language-based, involving parsing and translating between different syntaxes, but the principle could be generalised. Indeed, Sketch-n-Sketch \cite{sketchnsketch} is a system comprising a functional programming language and a 2D graphical scene; changes made to the latter are systematically "pushed back" through the code to change it, resting on a formal semantics of "reversible \lambda-calculus".

### Game development
Software encompasses a great deal of sub-genres, with their own requirements and problems. Within "software in general" lies both *graphical* software and compute-intensive "simulations". And within the intersection of these two sub-genres sits that of *games*.

We consider games to be of special interest to anyone interested in the difficulties of general programming, seconded only perhaps by Operating Systems. This is because games are the convergence of the entire range of programming sub-problems: graphics, interaction, sound, networking, data modelling, AI, resource management and multiprocessing. In fact, when developing a game one often has to replicate many features of an Operating System: custom solutions for managing memory, disk resources, and *especially* multitasking (if only for animations) are commonplace and encouraged. Unlike in other areas (such as office applications) it is considered *gauche* to use the standard GUI look-and-feel of the host OS, or to use its files and directories for anything but the coarsest-grained resource organisation.

There is also a culture of encouraging the use of visual editors and representations where they are most appropriate (such as the creation of 2D or 3D virtual worlds). One can typically "paint" or "sculpt" a game world as directly as possible; this contrasts favourably with the amateur approach of, say, encoding enemies as \# characters in a text file, or "writing" shapes and animations as lists of numbers. However, this also extends (in varying degrees) to less overtly visual domain of code and game logic (such as visual AI decision trees).

It would be instructive to compare graphical game creation suites, like GameMaker and Godot, with the traditional "source files, general-purpose languages and libraries" approach to this domain. There is much "transferrable knowledge" here that pertains to visual and non-visual programming.

### Against Conventional Wisdom
Kell observes that today's software is "fragmented" on a fundamental level; identical functions, libraries and toolchains can be found duplicated in each language ecosystem \cite{kell-os}. This is due to a neglect of inter-language communication, or at least the relegation thereof to niche use cases. What can only result from this is a solipsistic mindset among each language X; that it is its own isolationist silo, and any new functionality must have a library written in X.

Notice just how odd this is; the choice of language used in implementing software surely ought to be a mere *implementation detail* that users can be blind to. C++, Java and Haskell ought to compile to "binary" rather than "C++ binary", "Java binary" and "Haskell binary". Yet in practice, source language always becomes part of the integration "interface" of deployed software, and the usual approach of Foreign Function Interfaces has problems that discourage inter-language interop as a commonplace activity.

Kell proposes an alternative approach that goes beyond hiding of *implementation* to hiding of *interface* itself; exactly how this works is subtle, but fleshed out across his publications (beginning with \cite{kell-mmm}). Notably, his approach is in avowed opposition to the mindset behind Smalltalk (and hence STEPS), insofar as they rely on "taking over the world" as a new sort of base layer on which all else sits.

Basman's work, while approving of the above, goes on to radically question most other deep-set axioms of computing. One sensible idea falls under "addressibility", "co-ordinatisation", or "externalisability" \cite{externalise}: the need for any end-user friendly software to have all its "moving parts" not only editable, but *referrable to* in the first place. For example, assembly programming enjoys the virtue of everything (including program instructions, stack frames and data) having a *memory address* (albeit one that is flat, numeric and not permanent). This is presented in opposition to "information hiding" doctrine.

Higher-level languages undoubtedly improved on the many human-unfriendly aspects to the "bare metal" view. However, "structuring" or "scoping" rules introduce closed worlds that can only be referenced from within (such as the local variables of closures). This extends even to syntactic constructs like function composition (specifically, the "join point" is anonymous, preventing retroactively modifying or inserting something into it). And as a final example, while the "blueprints" for sub-computations are addressable (e.g. named procedures), individual *runs* thereof are transient and quickly overwritten on the stack. Basman argues that all this "information hiding" ultimately works against mass empowerment of end-users, in spite of (or even because of) its stated benefits.

### Other
The archived "Programming Wisdom Center" \cite{wisdom} presents a detailed attack on Object-Orientation, or at least the totalising and heavily hyped version in fashion through the 1990s and 2000s, arguing in favour of a "relational" or "table-oriented" programming. This approach of having *relational databases* instead of hierarchical filesystems be the organising model of code seems quite original, and there are plenty of diversions into re-thinking programming language concepts like types, collections and one-dimensional code. The same author continues these discussions on the C2 Wiki under the handle "TopMind" \cite{top}.

*Memory Models of Programming Languages* \cite{memmodPLs} acts as a survey of how various programming languages conceptualise memory (but perhaps better called "state"). One of these is the relational database model ("memory is a collection of multivalued finite functions"), another is that of COBOL ("memory is a tax form"). The discussion has relevance to multiple ontology support (see Section~\ref{context-appropriate-ontologies}), and demonstrates how low-level assumptions of a language can shape a lot of the rest down the line.

# Tech Dims

While we do have new ideas to propose, part of our contribution is integrating a wide range of *existing* concepts under a common umbrella. This work is spread out across different domains, but each part connects to programming systems or focuses on a specific characteristic they may have.

\paragraph{From languages to systems.}
Our approach lies between a narrow focus on programming languages and a broad focus on programming as a socio-political and cultural subject. Our concept of a programming system is technical in scope, although we acknowledge the technical side often has important social implications as in the case of the "Adoptability" dimension (Section\ \ref{adoptability}). This contrasts with the more socio-political focus found in Tchernavskij\ \cite{TcherDiss} or in software studies\ \cite{SwStudies}. It overlaps with Kell's conceptualization of UNIX, Smalltalk, and Operating Systems generally\ \cite{KellOS}, and we have ensured that UNIX has a place in our framework.

The distinction between more narrow _programming languages_ and broader _programming systems_ is more subtle. Richard Gabriel noted an invisible paradigm shift from the study of "systems" to the study of "languages" in computer science\ \cite{PLrev}, and this observation informs our distinction here. One consequence of the change is that a *language* is often formally specified apart from any specific implementations, while *systems* resist formal specification and are often *defined by* an implementation. We recognize programming language implementations as a *small region* of the space of possible systems, at least as far as interaction and notations might go. Hence we refer to the *interactive programming system* aspects of languages, such as text editing and command-line workflow.

\paragraph{Programming systems research.}
There is renewed interest in programming systems in the form of recent non-traditional programming tools:

- Computational notebooks such as Jupyter\ \cite{Jupyter} facilitate data analysis by combining code snippets with text and visual output. They are backed by stateful "kernels" and used interactively.
- "Low code" end-user programming systems allow application development (mostly) through a GUI. One example is Coda\ \cite{CodaWeb}, which combines tables, formulas, and scripts to enable non-technical people to build "applications as documents".
- Domain-specific programming systems such as Dark\ \cite{DarkWeb}, which claims a "holistic" programming experience for cloud API services. This includes a language, a direct manipulation editor, and near-instantaneous building and deployment.
- Even for general purpose programming with conventional tools, systems like Replit\ \cite{ReplitWeb} have demonstrated the benefits of integrating all needed languages, tools, and user interfaces into a seamless experience, available from the browser, that requires no setup.

Research that follows the programming systems perspective can be found in a number of research venues. Those include Human-Computer Interaction conferences such as [UIST](https://uist.acm.org/)^[ACM Symposium on User Interface Software and Technology] and [VL/HCC](https://conferences.computer.org/VLHCC/)^[IEEE Symposium on Visual Languages and Human-Centric Computing]. However, work in those often emphasizes the user experience over technical description. Programming systems are often presented in workshops such as [LIVE](https://liveprog.org/) and [PX](https://2021.programming-conference.org/home/px-2021)^[Programming eXperience]. However, work in those venues is often limited to the authors' individual perspectives and suffers from the aforementioned difficulty of comparing to other systems.

Concrete examples of systems appear throughout the paper. Recent systems which motivated some of our dimensions include Subtext\ \cite{Subtext}, which combines code with its live execution in a single editable representation; Sketch-n-sketch\ \cite{SnS}, which can synthesize code by direct manipulation of its outputs; Hazel\ \cite{Hazel}, a live functional programming environment with typed holes to enable execution of incomplete or ill-typed programs; and Webstrates\ \cite{Webstrates}, which extends Web pages with real-time sharing of state.

\paragraph{Already-known characteristics.}
There are several existing projects identifying characteristics of programming systems. Some revolve around a single one, such as levels of liveness\ \cite{Liveness}, or plurality and communicativity\ \cite{KellComm}. Others propose an entire collection. *Memory Models of Programming Languages*\ \cite{MemMod} identifies the "everything is an X" metaphors underlying many programming languages; the *Design Principles of Smalltalk*\ \cite{STdesign} documents the philosophical goals and dicta used in the design of Smalltalk; the "Gang of Four" *Design Patterns*\ \cite{DesPats} catalogues specific implementation tactics; and the *Cognitive Dimensions of Notation*\ \cite{CogDims} introduces a common vocabulary for software's *notational surface* and for identifying their trade-offs.

The latter two directly influence our proposal. Firstly, the Cognitive Dimensions are a set of qualitative properties which can be used to analyze *notations*. We are extending this approach to the "rest" of a system, beyond its notation, with *Technical* Dimensions. Secondly, our individual dimensions naturally fall under larger *clusters* that we present in a regular format, similar to the presentation of the classic Design Patterns. As for characteristics identified by others, part of our contribution is to integrate them under a common umbrella: the existing concepts of liveness, pluralism, and uniformity metaphors ("everything is an X") become dimensions in our framework.

\paragraph{Methodology.}
We follow the attitude of *Evaluating Programming Systems*\ \cite{EvProgSys} in distinguishing our work from HCI methods and empirical evaluation. We are generally concerned with characteristics that are not obviously amenable to statistical analysis (e.g. mining software repositories) or experimental methods like controlled user studies, so numerical quantities are generally not featured.

Similar development seems to be taking place in HCI research focused on user interfaces. The UIST guidelines\ \cite{UISTAuthor} instruct authors to evaluate system contributions holistically, and the community has developed heuristics for such evaluation, such as *Evaluating User Interface Systems Research*\ \cite{EvUISR}. Our set of dimensions offers similar heuristics for identifying interesting aspects of programming systems, though they focus more on underlying technical properties than the surface interface.

Finally, we believe that the aforementioned paradigm shift from programming systems to programming languages has hidden many ideas about programming that are worthwhile recovering and developing further\ \cite{ComplementaryBasic}. Thus our approach is related to the idea of _complementary science_ developed by Chang\ \cite{Chang} in the context of history and philosophy of science. Chang argues that even in disciplines like physics, superseded or falsified theories may still contain interesting ideas worth documenting. In the field of programming, where past systems are discarded for many reasons besides empirical failure, Chang's _complementary science_ approach seems particularly suitable.

\paragraph{Programming systems deserve a theory too!}
In short, while there is a theory for programming languages, programming *systems* deserve a theory too. It should apply from the small scale of language implementations to the vast scale of operating systems. It should be possible to analyse the common and unique features of different systems, to reveal new possibilities, and to build on past work in an effective manner. In Kuhnian terms\ \cite{Kuhn}, it should enable a body of "normal science": filling in the map of the space of possible systems (Figure \ref{fig:tech-dims-diagram}), thereby forming a knowledge repository for future designers.