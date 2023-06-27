\chapter{Related Work}
In the preceding chapters, we referenced programming systems and research literature that were directly relevant to the topics we were discussing. At this point, having presented the BootstrapLab and evaluated it according to the Technical Dimensions, we can situate our contribution in the setting of more general related work. Some references may be repeated from earlier for the sake of completeness.

# STEPS and the Legacy of VPRI
The COLA system design\ \parencite{COLA}, from which we have drawn the most in this work, emerged from the now-retired Viewpoints Research Institute (VPRI). VPRI aimed at creating "fundamentally new computing technologies", which is particularly visible throughout the 6-year project known as "STEPS towards a new computing" \cite{Steps08,Steps09,Steps10,Steps11,Steps12}. The aim was to fully replicate a familiar graphical end-user operating system with applications in under 20,000 total lines of code. Such an ambitious goal provided the constraint needed to force innovation in distinguishing essential and accidental complexity and ways to reduce the latter. Innovations included the widespread use of domain-specific languages supported by OMeta\ \parencite{OMeta} and investment in highly flexible core abstractions as evidenced by COLA's object and composition models \parencite{OROM,OECM}.

Two of our Three Properties, Self-Sustainability and Notational Freedom, recur as themes in the STEPS work. Explicit Structure, on the other hand, is absent, but this is to be expected owing to the niche status of Explicit Structure (see Section\ \ref{structured-editing-and-its-variations}). Self-Sustainability is exhibited in the COLA work. Mood-Specific Languages in COLA and those supported by OMeta demonstrate what we called *syntactic freedom* in Section\ \ref{syntactic-freedom}. The Gezira\ \parencite{Gezira} and Nile\ \parencite{Nile} projects amount to a custom mathematical syntax (taking advantage of Unicode characters) for expressing graphics code that is cumbersome in ordinary languages. While this does not amount to Syntactic Freedom, it is a good example of the sort of thing that Syntactic Freedom enables; we can expect more innovations like this only if it is not too difficult to deploy a custom syntax once one has been designed.

# Self-Sustainability and Bootstrapping
The only name we are aware of for the concept we called Self-Sustainability is "self-sustaining", seen in the two workshops on such systems \parencite{SSS08,SSS10} which featured the COLA work. We derived our term "Self-Sustainability" to be able to refer to a property that systems can have or lack. We have referred to Self-Sustain*able* systems rather than Self-Sustain*ing* systems for consistency with this.

Self-Sustainability appears in related work as Smalltalk variants. Much of the STEPS work took place via the Squeak^[\url{https://squeak.org/}] variant, of which Pharo^[\url{https://pharo.org/}] is a descendant. Glamorous Toolkit^[\url{https://gtoolkit.com/}] is a "moldable development environment" built in Pharo. The Lively Kernel^[\url{https://lively-kernel.org/}] is a Web implementation of a Smalltalk-like environment; Fizzygum^[http://fizzygum.org/] is similar.

The problem with all of these, as regards our goals in this dissertation, is that they are all complicated software systems, with their own histories, made to be practically useful to researchers or industry. As such, knowledge of the principles and tricks for *implementing* these systems is sequestered away in the practical experience of their developers and not written down in a discoverable location. Furthermore, it would be difficult to separate knowledge about the property we are interested in (Self-Sustainability) from the various other aspects of the implementation of these systems (such as useful libraries or optimisations).

These facts made it clear to us that we would be best equipped to understand Self-Sustainability by trying to achieve it ourselves in a minimal context with minimal distractions. The related systems are self-sustainable in order to be useful for certain communities; BootstrapLab aims for Self-Sustainability to better understand it (along with the other two Properties).

We are only aware of a few sources that aim at a similar goal of understanding. The "Meta-Helix" approach of\ \parencite{Meta-helix} is intended to reduce confusion when implementing meta-circular Meta-Object-Protocols. As we mentioned in Section\ \ref{precursors-of-self-sustainability}, meta-circularity is a specific manifestation of Self-Sustainability. The exhaustive development of Procedural Reflection for Lisp-like languages in \parencite{PRinPLs} is helpful for its philosophical rigour, \eg the use-mention distinction and careful precision of terminology. The process described in\ \parencite{Bootfrom0} is a parallel of what we did with BootstrapLab in the restricted context of batch-mode interpreters of text strings. Finally, for an informal and valiant attempt to articulate the subtleties of Self-Sustainability,

https://www.cemetech.net/forum/viewtopic.php?p=270092#270092



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

\joel{
The two best-known self-sustainable programming systems are Lisp and Smalltalk. Both are typically discussed in programming language terms, but they are more interesting as programming systems. In Smalltalk and many implementations of Lisp (e.g., Interlisp), the system itself (producer) can be modified from the same environment that is used for creating products. In other words, a Smalltalk image contains both the objects that make up the product as well as the objects that make up the Smalltalk environment itself. We also regard Unix as a whole to be a self-sustainable system, though the individual programs within it seldom have this property.

Most literature discussing self-sustainability seems to focus on textual languages as the way to get there. We broaden the scope to programming systems, because this is necessary in order to talk about interaction and graphical capabilities. We desire to support graphical or structured ways of expressing programs that go beyond text\ \cite{Subtext,Infra,Varv}, and feel that this has been neglected in prior work.
}

# Structured Editing and Its Variations
Explicit Structure has precedent in structure editors, projectional editors and block-based languages, but these approaches have met many difficulties in terms of widespread adoption.

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
