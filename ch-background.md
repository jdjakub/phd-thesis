\chapter{Background}

# Stories (reminder)
1. Bootstrapping self-sustaining systems from a starting point
2. Fixing/freeing the COLA work by lifting the text restriction
3. Re-building the software stack with the benefit of hindsight and better tech.

# Background
## Systems vs languages
## Why the 2 properties

This chapter is org'd by themes because each one is kinda a response / reaction to the previous. Systems vs languages struggle.

### Programming Systems
The classic essay by Gabriel \cite{PLrev} distinguishes the *languages* and *systems* paradigms to programming research. The topic of this thesis, and many of the examples we will use to illustrate concepts, rely on understanding this distinction and only make sense within the systems paradigm.

\cite{Jupyter}
\cite{CodaWeb}
\cite{DarkWeb}
\cite{ReplitWeb}

[UIST](https://uist.acm.org/)^[ACM Symposium on User Interface Software and Technology]
[VL/HCC](https://conferences.computer.org/VLHCC/)^[IEEE Symposium on Visual Languages and Human-Centric Computing]
[LIVE](https://liveprog.org/)
[PX](https://2021.programming-conference.org/home/px-2021)^[Programming eXperience].

\cite{fpexplain}
\cite{boomerang}
\cite{sketchnsketch}

\cite{domain-workbench}

\cite{Hazel}
\cite{Webstrates}
[Squeak](https://squeak.org/)
[Lively Kernel](https://lively-kernel.org/)
[Pharo](https://pharo.org/)
[Fizzygum](http://fizzygum.org/)
[Io language](https://iolanguage.org)

### The Legacy of VPRI
This thesis stands on the shoulders of the retired Viewpoints Research Institute (VPRI). It aimed at creating "fundamentally new computing technologies", which is particularly visible throughout the 6-year project known as "STEPS towards a new computing" \cite{steps08,steps09,steps10,steps11,steps12}. The aim was to fully replicate a familiar graphical end-user operating system with applications in under 20,000 total lines of code. This ambitious goal provided the constraint needed to force innovation in "factoring" complexity. This motivated widespread use of domain-specific languages and investment in the "meta"-level. Several writings describing programming systems, or parts of them, came out of STEPS, the most important for this thesis being the COLA.

\cite{OMeta}
\cite{colas}
\cite{orom}
\cite{oecm}

Instead of only being able to make changes to a mass of source code written in the same language, a COLA is supposed to permit easy proliferation of languages optimised for individual sub-problems. Such languages are typically known as domain-specific languages or DSLs, but the STEPS project sees a range of granularity all the way down to “mood-specific languages” (MSLs). An example of an MSL might be expressing a single line of code with mathematical notation instead of the usual monospaced ASCII subset. The language half of a COLA is designed to make it feasible for a programmer to do this sort of thing on a whim; encouraging “the right language for the job” no matter how small the context, in a way that normal language infrastructure makes too costly to be worthwhile.

\cite{nile,gezira}

### Text as *presentation*, not *re*presentation
In a project dedicated to critically examining assumptions about the nature of how computing and programming must work, it is surprising that the reliance on "text" or "language" seems not to have been questioned. I find the "domain-specific at many levels" idea appealing, but would prefer to consider "notations" in general, rather than narrow syntax or semantics of languages.

The "language" that I consider too narrow a constraint consists of renderings of text. This is as opposed to other uses of “language” in areas such as design: "visual language" may not include text at all, but uses the "language" term metaphorically for the arrangement of elements. Under this latter meaning, the "domain-specific language" idea has all the degrees of freedom it deserves. Nevertheless, our use of the word "language" will stick to denoting renderings of text glyphs and we shall use words like "notation" or "interface" for the fully general extension.

The problem with "plain text" is that it fuses together two independent concepts: what we could call *presentation* (how one reads and modifies the data) and *representation* (how the data is stored in memory). For example, the AST of a program *could* be stored in its tree form and presented as indented text. But what we do instead is serialise the text, store that, and parse it back before we are able to work with it.
\cite{infra}
\cite{subtext}
[schematic tables](http://www.subtext-lang.org/OOPSLA07.pdf)

### Visual / End-User Programming
Beyond supporting language-based notations not stored as text, there are situations (domains) where a graphical notation is best. It is important to support these to truly realise the goal of mood-specific notations.
\cite{boxer}
\cite{sketchpad}
\cite{wwid}
[Briar](https://graphics.cs.wisc.edu/Papers/1994/Gle94/constraints.pdf)
\cite{palimpsest}
\cite{ddv}
[Apparatus](http://aprt.us/)
\cite{ywimc}
\cite{vpcodex}
\cite{vpsurvey}
\cite{galleryUIs}
\cite{forms3}
\cite{hopscotch}

### Towards a "bootstrapping" theory
There is a well-established art of bootstrapping self-hosting compilers and meta-circular languages. However, the same cannot be said for more general programming systems. The seeds of such a foundation are scattered around several papers and blog posts.
[BootstrapLab](https://programmingmadecomplicated.wordpress.com/2018/01/18/3-hacking-together-orom-domctrlshiftj/)
[Ideal goal](https://programmingmadecomplicated.wordpress.com/2018/09/13/what-am-i-aiming-for-again/)
[CookClay](https://www.cemetech.net/forum/viewtopic.php?p=270092#270092)
\cite{meta-helix}
\cite{PRinPLs}
\cite{bootfrom0}

### Against Conventional Wisdom
A plausible hypothesis about *why* programming is the way it is---and hence why this thesis has a novel contribution to make---concerns a mismatch between programming's *military-industrial* history and its modern potential for *personal* computing. This is the logic behind the work of Kell and Basman.
\cite{kell-os}
\cite{externalise}
\cite{kell-mmm}
\cite{oap}
\cite{entangle}
\cite{entangle-critique}
\cite{TcherDiss}
\cite{SwStudies}
\cite{wisdom}
\cite{top}

### Tech Dims
We propose a common language as an initial step towards a more progressive research on programming systems. Our set of technical dimensions seeks to break down the holistic view of systems along various specific “axes”. The dimensions identify a range of possible design choices, characterized by two extreme points in the design space. They are not quantitative, but they allow comparison by locating systems on a common axis. We do not intend for the extreme points to represent “good” or “bad” designs; we expect any position to be a result of design trade-offs. At this early stage in the life of such a framework, we encourage agreement on descriptions of systems first in order to settle any normative judgements later. The set of dimensions can be understood as a map of the design space of programming systems (Figure 1). Past and present systems will serve as landmarks, and with enough of them, we may reveal unexplored or overlooked possibilities. So far, the field has not been able to establish a virtuous cycle of feedback; it is hard for practitioners to situate their work in the context of others’ so that subsequent work can improve on it. Our aim is to provide foundations for the study of programming systems that would allow such development.

While we do have new ideas to propose, part of our contribution is integrating a wide range of existing concepts under a common umbrella. This work is spread out across different domains, but each part connects to programming systems or focuses on a
specific characteristic they may have.
\cite{Liveness}
\cite{KellComm}
\cite{STdesign}
\cite{DesPats}
\cite{CogDims}
\cite{memmodPLs}

\cite{EvProgSys}
\cite{UISTAuthor}
\cite{EvUISR}
\cite{ComplementaryBasic}
\cite{Chang}
\cite{Kuhn}
