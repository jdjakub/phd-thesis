\chapter{Introduction}

\joel{Stefan:
- Situate, vision (high-level problems)
- Identify specific problems to set expectations
- State solution approaches
- E.g: programming systems => propose a framework, which we break down for evaluating BL, which fills a gap
- Thesis statement: existence proof of such a system
- Conclude intro
- Include papers and how they map to chapters
}

# Problem Statement
When we have an idea for some computer software, and try and make this idea a reality, we are forced to confront two types of complexity: the *essential* and the *accidental*. We know there is "no such thing as a free lunch", so we are able to accept the burden of whatever complexity is actually intrinsic to our idea. If we have a simple idea, we are prepared to do a little work; if it is more ambitious, we will accept having to do more work. Yet this *essential* complexity is often swamped by unwelcome incursions of tedious busy-work. Concepts that appear simple to us must be spelled out in great detail for a computer or require much work to make them machine-readable. This is the *accidental complexity* that is widespread in programming.

In our experience, the three most important sources of accidental complexity in programming are as follows:

1. In order to make a small change to a program, one must go to the source code which may require an entirely different language and way of thinking.
2. We must describe graphical constructs with language in order to fit them into program code.
3. We have to avoid syntax errors, escape certain characters, and write code for parsing and serialising.

These are not quite observations about programming *languages.* Instead, they concern the wider environment of tools in which programming is performed, such as the editor interface and facilities for running the programs.

It is important not to conflate "coding" in a programming language with programming itself. We see *programming* as the general act of making a computer do things by itself. By this definition, coding, visual programming, programming by example and deep learning are some specific *means* by which to program. If we ignore this subtlety, we risk unwittingly limiting the scope of innovative ideas.

For example, instead of seeking the right notation, interface or representation for the job, we might seek the right *textual syntax* for the job. If we cannot find one, the real reason may simply be that text is not well-suited to the job. If text is all we know, we will be under the false impression that it is an *intrinsically* hard job.
* Instead of being able to make changes to a running program, we are stuck changing its blueprint and re-creating it. It is easy to make hermetically-sealed programs this way and hard to make open ones. 
* Instead of seeking a software *system* open to unanticipated changes as it runs, we might seek intricate *language* features that give flexibility only for *compiling* a program.

A key problem is that there is no established term for this scope of programming research, and hence no body of work in which we may situate it. This problem needs to be addressed in order to combat the accidental complexities we mentioned.

# Research Context

# Research Contributions
In order to properly address the accidental complexities we mentioned, we need a more general programming *systems* approach instead of the usual *languages* framing. Put simply, a programming system is a language plus the interactive environment in which it is used. We will develop this idea in Chapters\ \ref{analysis} and\ \ref{tech-dims} and propose a systematic framework by which to analyse programming systems. This framework will then help us refine three properties that mitigate each of the accidental complexities and are central to the dissertation. We will develop them in detail in coming chapters, but we will give a brief summary here.

# The Three Properties
We are interested in exploring, developing, and achieving these three properties in a programming system:

1. *Self-sustainability:* being able to evolve and re-program a system, using itself, while it is running.
2. *Notational Freedom:* being free to use any notation as desired to create any part of a program, at no additional cost beyond that required to implement the notation itself.
3. *Explicit Structure:* being able to work with data structures directly, unencumbered by the complexities of parsing and serialising strings.

We will refine and expand these definitions in later chapters, but they are reasonable to start with. Each one brings its own advantages to a programming system:

1. Self-sustainability reduces the accidental complexity of having to make changes using a separate, unfamiliar programming system. It also permits *innovation feedback:* anything helpful created using the system can benefit not only other programs sitting atop the system, but also the system's own development.
2. Notational Freedom makes it easier to use the "Right Tool For The Job." There is always the difficulty of *deciding* what this is, but at least once a programmer has made this decision they can get on with using it more easily. This there is no longer any need to describe graphical constructs using language.
3. Explicit Structure avoids various pitfalls of strings, both in terms of correctness and convenience. Consumers of a structure benefit from an editor that can only save valid structures, and producers benefit by discovering errors early instead of later during consumption. Writing programs to use such structures is improved if one does not have to maintain parsing/serialising code or think about escaping.

\joel{
These three properties each mitigate some common accidental complexities in programming. Self-sustainability means that a small change to the system can be made using its languages, abstractions and interface. This contrasts with the usual case, where one must go to the source code of the system which may require an entirely different language and way of thinking. Without Notational Freedom, we must describe graphical constructs with language in order to fit them into program code. Without Explicit Structure, we have to avoid syntax errors, escape certain characters, and write code for parsing and serialising.
}

No doubt, these properties also have drawbacks. Yet the advantages mean that they at least deserve *further exploration* in programming. Unfortunately, these properties are rarely exhibited. Even where they do exist, they are isolated from one another and not combined in the same system.

These properties are also entangled with each other from a research perspective. Self-sustainability makes it easier to add *new* notations to a system with Notational Freedom. It also makes it easier to add Notational Freedom *itself* to a system that lacks it, and lets the benefits flow into all aspects of the system's development. Yet self-sustainability is currently best understood as a vague analogy to self-hosting compilers, with even the COLA work not making it clear how such a property can be achieved in interactive, graphical systems. Notational Freedom is impossible to achieve in a world of parsed strings and text editors (that's merely *syntactic* freedom) so it needs Explicit Structure as a necessary foundation. Finally, Explicit Structure lets us study the other two properties more purely, without getting confused by the accidental complexities of parsing and escaping.

## Thesis Statement
The statement of our thesis is as follows:

> It is possible to add notational freedom to the web browser programming system by embedding a self-sustainable system built on explicit structure.

We prove this by constructing such a system which we call *BootstrapLab.* It is evaluated according to *technical dimensions* that we derive from the Three Properties. These dimensions fit within a methodological framework that we propose for studying programming systems.

# Supporting Papers
The following papers form chapters in this thesis:

* \cite{TechDims} forms Chapter\ \ref{tech-dims}.
* \cite{CCS20} forms Chapter\ \ref{year1}.
* \cite{Onward22} forms Chapter\ \ref{bl}.
