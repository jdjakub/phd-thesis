\chapter{Related Work}

SS:
Smalltalk
Squeak
Pharo
Fizzygum
Lively Kernel
Cook Clay
COLA (OROM, OECM)
Game Engines

NF:
MPS
Eco
OMeta

Notations:
Gezira / Nile
Sketch n Sketch
WWID / YWIMC
Boxer
Sketchpad
[Briar](https://graphics.cs.wisc.edu/Papers/1994/Gle94/constraints.pdf)
Palimpsest
DDV
[Apparatus](http://aprt.us/)
VPcodex
VPsurvey
GalleryUIs
Forms3
Hopscotch

ES:
Subtext
Infra

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

\cite{OMeta}
\cite{COLAs}
\cite{OROM}
\cite{OECM}

\joel{Bootstrapping allows the language creator to co-evolve the language and the compiler. However, they typically have to do so outside of the environment used for building other products.}

\joel{This is hard to understand.
A related concept is that of *meta-circular evaluator*, which would be an interpreter for NovLang, written in NovLang, that implements features by deferring to its own implementation. For example, a meta-circular interpreter for Lisp in Lisp would implement `eval` by calling `eval`. This makes the task of writing the interpreter easier, but it does not eliminate the distinction between the product (application) source code and producer (interpreter) source code. \cite{ProcRefl}.
]

\joel{
The two best-known self-sustainable programming systems are Lisp and Smalltalk. Both are typically discussed in programming language terms, but they are more interesting as programming systems. In Smalltalk and many implementations of Lisp (e.g., Interlisp), the system itself (producer) can be modified from the same environment that is used for creating products. In other words, a Smalltalk image contains both the objects that make up the product as well as the objects that make up the Smalltalk environment itself. We also regard Unix as a whole to be a self-sustainable system, though the individual programs within it seldom have this property.

Most literature discussing self-sustainability\ \cite{SSS08,SSS10} seems to focus on textual languages as the way to get there. We broaden the scope to programming systems, because this is necessary in order to talk about interaction and graphical capabilities. We desire to support graphical or structured ways of expressing programs that go beyond text\ \cite{Subtext,Infra,Varv}, and feel that this has been neglected in prior work.
}

\joel{
## Text as *presentation*, not *re*presentation
STEPS was a project dedicated to critically examining assumptions about the nature of how computing and programming must work, yet it is surprising that the reliance on "text" or "language" seems to have been preserved wholesale. We find the "domain-specific at many levels" idea appealing, but would prefer to consider "notations" in general, rather than narrow syntax or semantics of languages.
}

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

# Against Conventional Wisdom
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
