\hypertarget{tech-dims}{%
\chapter{Technical Dimensions of Programming Systems}\label{tech-dims}}

\setlength{\epigraphwidth}{0.7\linewidth}
\epigraph{A systematic presentation removes ideas from the ground that made them grow and arranges them in an artificial pattern.}{\textit{The Tyranny of Science} \\ \textsc{Paul Feyerabend}}

\joel{
\renewcommand{\epigraphflush}{flushleft}
\renewcommand{\sourceflush}{flushleft}
}

\epigraph{Irony is said to allow the artist to continue his creative production while immersed in a sociocultural context of which he is critical.}{\emph{Irony; or, the Self-Critical Opacity of Postmodernist Architecture} \\ \textsc{Emmanuel Petit} }

In Section\ \ref{programming-systems-vs-languages}, we introduced the concept of a *programming system.* Not only is such a concept necessary for framing the work in this dissertation, there is also a growing interest in programming systems in both research and industry. Yet while programming *languages* are a well-established concept, analysed and compared with a common vocabulary, no similar foundation exists for the wider range of programming *systems.* In this chapter,^[Adapted from our paper for Programming 2023 \cite{TechDims} which won the Editors' Choice Award for the journal.] we will examine this problem and propose a framework of "Technical Dimensions" to kickstart systematic research on programming systems. We will then solidify our Three Properties (Section\ \ref{the-three-properties}) as dimensions under this framework.

# Barriers to Programming Systems Research
Researchers are studying topics such as *programming experience* \cite{PX} and *live programming* \cite{LIVE} that require considering not just the *language*, but further aspects of a given system. At the same time, companies are building new programming environments like Replit\ \cite{ReplitWeb} or "low-code" tools like Dark\ \cite{DarkWeb} and Glide\ \cite{GlideWeb}. Yet, such topics remain at the sidelines of mainstream programming research. 

The academic research on programming suffers from this lack of common vocabulary. While we may thoroughly assess programming *languages*, as soon as we add interaction or graphics into the picture, evaluation beyond subjective "coolness" becomes fraught with difficulty.^[The same difficulty in the context of user interface systems has been analysed by Olsen\ \cite{EvUISR}. Interesting future work would be a detailed analysis of publications on programming systems to understand this issue in depth. One notable characteristic is that publications tend to present (parts of) new systems. This is the case for 5/6 and 6/7 papers in the LIVE 2020 and 2021 workshops respectively\ \cite{LIVE20, LIVE21}. In contrast, publications in the field of programming *languages* often address specific issues of interest to a greater number of languages.] Moreover, when designing new systems, inspiration is often drawn from the same few standalone sources of ideas. These might be influential past systems like Smalltalk, programmable end-user applications like spreadsheets, or motivational illustrations like those of Bret Victor\ \cite{BretVictor}.

Instead of forming a solid body of work, the ideas that emerge are difficult to relate to each other. The research methods used to study programming systems lack the rigorous structure of programming language research methods. They tend to rely on singleton examples, which demonstrate their author's ideas, but are inadequate methods for comparing new ideas with the work of others. This makes it hard to build on top and thereby advance the state of the art.

Studying *programming systems* is not merely about taking a programming language and looking at the tools that surround it. It presents a *paradigm shift* \cite{Kuhn} to a perspective that is, at least partly, *incommensurable* with that of languages. When studying programming languages, everything that matters is in the program code; when studying programming systems, everything that matters is in the *interaction* between the programmer and the system. As documented by Gabriel\ \cite{PLrev}, looking at a *system* from a *language* perspective makes it impossible to think about concepts that arise from interaction with a system, but are not reflected in the language. Thus, we must proceed with some caution. As we will see, when we talk about Lisp as a programming system, we mean something very different from a parenthesis-heavy programming language!

# Contributions
While we do have new ideas to propose, part of our contribution is integrating a wide range of existing concepts under a common umbrella. This work is spread out across different domains, but each part connects to programming systems or focuses on a specific characteristic they may have.

We propose a common language as an initial step towards a more progressive research on programming systems. Our set of *technical dimensions* seeks to break down the holistic view of systems along various specific "axes". The dimensions identify a range of possible design choices, characterized by two extreme points in the design space. They are not quantitative, but they allow comparison by locating systems on a common axis. We do not intend for the extreme points to represent "good" or "bad" designs; we expect any position to be a result of design trade-offs. At this early stage in the life of such a framework, we encourage agreement on descriptions of systems first in order to settle any normative judgements later.

The set of dimensions can be understood as a map of the design space of programming systems (Figure\ \ref{fig:tech-dims-diagram}). Past and present systems will serve as landmarks, and with enough of them, we may reveal unexplored or overlooked possibilities. So far, the field has not been able to establish a virtuous cycle of feedback; it is hard for practitioners to situate their work in the context of others' so that subsequent work can improve on it. Our aim is to provide foundations for the study of programming systems that would allow such development.

The contributions of this chapter are organised as follows:

1. We develop *self-sustainability,* *notational freedom,* and *explicit structure* as Technical Dimensions, following on from the discussion in Section\ \ref{the-three-properties-in-more-detail}. For each one, we give examples that illustrate the range of values it spans. The rest can be found in Appendix\ \ref{technical-dimensions-catalogue}, organised into related clusters: *interaction*, *notation*, *conceptual structure*, *customisability*, *complexity*, *errors*, and *adoptability*.
2. In Section\ \ref{evaluation}, we sketch two ways of using the framework. In Section\ \ref{evaluating-the-dark-programming-system}, we use it to evaluate a recent interesting programming system. In Section\ \ref{exploring-the-design-space}, we use it to make Section\ \ref{the-missing-synthesis}'s "missing synthesis" idea more precise by plotting the unexplored part of the design space. This will let us envision a potential novel programming system to be developed in Chapters\ \ref{the-state-first-approach-to-bootstraplab} and\ \ref{the-code-first-approach-to-bootstraplab}.

\begin{figure}
  \centering
  \includegraphics[width=0.6\linewidth]{plot-figure0.pdf}
  \caption{One 2-dimensional slice of the space of possible systems, to be examined in more detail in Section\ \ref{exploring-the-design-space}.\label{fig:tech-dims-diagram}}
\end{figure}

# Related work

While we do have new ideas to propose, part of our contribution is integrating a wide range of *existing* concepts under a common umbrella. This work is spread out across different domains, but each part connects to programming systems or focuses on a specific characteristic they may have.

\paragraph{From languages to systems.}
Our approach lies between a narrow focus on programming languages and a broad focus on programming as a socio-political and cultural subject. Our concept of a programming system is technical in scope, although we acknowledge the technical side often has important social implications as in the case of the "Adoptability" dimension (Section\ \ref{adoptability}). This contrasts with the more socio-political focus found in Tchernavskij\ \cite{TcherDiss} or in software studies\ \cite{SwStudies}. It overlaps with Kell's conceptualisation of Unix, Smalltalk, and Operating Systems generally\ \cite{KellOS}, and we have ensured that Unix has a place in our framework.

The distinction between more narrow _programming languages_ and broader _programming systems_ is more subtle. Richard Gabriel noted an invisible paradigm shift from the study of "systems" to the study of "languages" in computer science\ \cite{PLrev}, and this observation informs our distinction here. One consequence of the change is that a *language* is often formally specified apart from any specific implementations, while *systems* resist formal specification and are often *defined by* an implementation. We recognise programming language implementations as a *small region* of the space of possible systems, at least as far as interaction and notations might go. Hence we refer to the *interactive programming system* aspects of languages, such as text editing and command-line workflow.

\paragraph{Programming systems research.}
There is renewed interest in programming systems in the form of recent non-traditional programming tools:

- Computational notebooks such as Jupyter\ \cite{Jupyter} facilitate data analysis by combining code snippets with text and visual output. They are backed by stateful "kernels" and used interactively.
- "Low code" end-user programming systems allow application development (mostly) through a GUI. One example is Coda\ \cite{CodaWeb}, which combines tables, formulas, and scripts to enable non-technical people to build "applications as documents".
- Domain-specific programming systems such as Dark\ \cite{DarkWeb}, which claims a "holistic" programming experience for cloud API services. This includes a language, a direct manipulation editor, and near-instantaneous building and deployment.
- Even for general purpose programming with conventional tools, systems like Replit\ \cite{ReplitWeb} have demonstrated the benefits of integrating all needed languages, tools, and user interfaces into a seamless experience, available from the browser, that requires no setup.

Research that follows the programming systems perspective can be found in a number of research venues. Those include Human-Computer Interaction conferences such as [UIST](https://uist.acm.org/)^[ACM Symposium on User Interface Software and Technology] and [VL/HCC](https://conferences.computer.org/VLHCC/)^[IEEE Symposium on Visual Languages and Human-Centric Computing]. However, work in those often emphasises the user experience over technical description. Programming systems are often presented in workshops such as [LIVE](https://liveprog.org/) and [PX](https://2021.programming-conference.org/home/px-2021)^[Programming eXperience]. However, work in those venues is often limited to the authors' individual perspectives and suffers from the aforementioned difficulty of comparing to other systems.

Concrete examples of systems appear throughout the paper. Recent systems which motivated some of our dimensions include Subtext\ \cite{Subtext}, which combines code with its live execution in a single editable representation; Sketch-n-sketch\ \cite{SnS}, which can synthesise code by direct manipulation of its outputs; Hazel\ \cite{Hazel}, a live functional programming environment with typed holes to enable execution of incomplete or ill-typed programs; and Webstrates\ \cite{Webstrates}, which extends Web pages with real-time sharing of state.

\paragraph{Already-known characteristics.}
There are several existing projects identifying characteristics of programming systems. Some revolve around a single one, such as levels of liveness\ \cite{Liveness}, or plurality and communicativity\ \cite{KellComm}. Others propose an entire collection. *Memory Models of Programming Languages*\ \cite{MemMod} identifies the "everything is an X" metaphors underlying many programming languages; the *Design Principles of Smalltalk*\ \cite{STdesign} documents the philosophical goals and dicta used in the design of Smalltalk; the "Gang of Four" *Design Patterns*\ \cite{DesPats} catalogues specific implementation tactics; and the *Cognitive Dimensions of Notation*\ \cite{CogDims} introduces a common vocabulary for software's *notational surface* and for identifying their trade-offs.

The latter two directly influence our proposal. Firstly, the Cognitive Dimensions are a set of qualitative properties which can be used to analyse *notations*. We are extending this approach to the "rest" of a system, beyond its notation, with *Technical* Dimensions. Secondly, our individual dimensions naturally fall under larger *clusters* that we present in a regular format, similar to the presentation of the classic Design Patterns. As for characteristics identified by others, part of our contribution is to integrate them under a common umbrella: the existing concepts of liveness, pluralism, and uniformity metaphors ("everything is an X") become dimensions in our framework.

\paragraph{Methodology.}
We follow the attitude of *Evaluating Programming Systems*\ \cite{EvProgSys} in distinguishing our work from HCI methods and empirical evaluation. We are generally concerned with characteristics that are not obviously amenable to statistical analysis (e.g. mining software repositories) or experimental methods like controlled user studies, so numerical quantities are generally not featured.

Similar development seems to be taking place in HCI research focused on user interfaces. The UIST guidelines\ \cite{UISTAuthor} instruct authors to evaluate system contributions holistically, and the community has developed heuristics for such evaluation, such as *Evaluating User Interface Systems Research*\ \cite{EvUISR}. Our set of dimensions offers similar heuristics for identifying interesting aspects of programming systems, though they focus more on underlying technical properties than the surface interface.

Finally, we believe that the aforementioned paradigm shift from programming systems to programming languages has hidden many ideas about programming that are worthwhile recovering and developing further\ \cite{ComplementaryBasic}. Thus our approach is related to the idea of _complementary science_ developed by Chang\ \cite{Chang} in the context of history and philosophy of science. Chang argues that even in disciplines like physics, superseded or falsified theories may still contain interesting ideas worth documenting. In the field of programming, where past systems are discarded for many reasons besides empirical failure, Chang's _complementary science_ approach seems particularly suitable.

\todo{Include Methodology Appendix}

\paragraph{Programming systems deserve a theory too!}
In short, while there is a theory for programming languages, programming *systems* deserve a theory too. It should apply from the small scale of language implementations to the vast scale of operating systems. It should be possible to analyse the common and unique features of different systems, to reveal new possibilities, and to build on past work in an effective manner. In Kuhnian terms\ \cite{Kuhn}, it should enable a body of "normal science": filling in the map of the space of possible systems (Figure \ref{fig:tech-dims-diagram}), thereby forming a knowledge repository for future designers.

# The Three Properties as Dimensions
\todo{this section}

## Self-Sustainability
In light of the points in Section\ \ref{self-sustainability}, we can discover some key criteria of self-sustainability by considering the Web browser as a programming system. Using the terminology from Section\ \ref{user-vs-implementation-levels}, let us consider the browser as the *product system* and C++ as the *platform.* What would it take to make this self-sustainable? 

### Minimise The Substrate
Most of the named entities in the C++ code are stuck at the implementation level, inaccessible at the user level of JavaScript, so we must move the former into the latter.

We can begin with the graphical surface of the product system. For each graphical element, we inquire into the causes of its display; this will include graphical rendering code, but also the data that is being rendered and the code that generated it. By tracing backwards in this way we discover the web of causes that produced the shape on the screen. This will often go through the user level (JavaScript), but if we keep tracing back, we will hit the implementation level. Each time this happens, we port the code from the implementation level to the user level.

We continue this until it is no longer feasible; for example, there will ultimately have to be some native machine-code interpreter for JavaScript in the running system. In practice, there would need to be the usual investments in JIT compilation and optimisation technology as seen in VMs for Smalltalk and other languages.

These ideas suggests a quantitative criterion of *substrate size* as a *penalty* for self-sustainability. \joel{ref bl}

### Fix "Delete By Default"
As we discussed from Section\ \ref{assumptions-and-consequences-in-batch-mode} onward, the activities of a running process under Unix are considered disposable. In order for a system to be self-sustainable, it has to be able to preserve developments of its state through process termination. The standard VM solution is to have most of the system state saved in an "image" file and concentrate the substrate in a runnable binary that need not be changed. However this is accomplished, *persistence* of run-time changes is necessary to encourage indefinite evolution of the system. This applies to the whole browser, but could also be a concern for individual tabs or web pages that can be closed or refreshed.

This suggests a qualitative criterion of *persistence effort*. 

### Support Editing of the Code
The browser's JS console makes it technically possible to make arbitrary changes expressible as JS commands, and the source code can be viewed but not edited. We would need to make a small change so that the source code viewer could also be used to edit code in a persistent manner.

### Support the Manipulation of Code as Data
Once can type text inside the system, we will be able to write code. However, this code will be inert unless the system can interpret data structures as programs and actually execute them. This is the case whether these data structures were created manually or by a program. If this is not possible, re-programming the system beyond selecting from a predefined list of behaviours will not be possible. The browser does already satisfy this criterion since JS has an `eval()` function that can execute a string of JS code.

1. *Can you add new items to system namespaces without a restart?* The canonical example of this is in JavaScript, where "built-in" classes like `Array` or `Object` can be augmented at will (and destructively modified, but that would be a separate point). Concretely, if a user wishes to make a new `sum` operation available to all Arrays, they are not *prevented* from straightforwardly adding the method to the Array prototype as if it were just an ordinary object (which it is). Having to re-compile or even restart the system would mean that this cannot be meaningfully achieved from within the system. Conversely, being able to do this means that even "built-in" namespaces are modifiable by ordinary programs, which indicates less of a implementation level vs. user level divide and seems important for self-sustainability.
2. *Can programs generate programs and execute them?* This property, related to "code as data" or the presence of an `eval()` function, is a key requirement of self-sustainability. Otherwise, re-programming the system, beyond selecting from a predefined list of behaviors, will require editing an external representation and restarting it. If users can type text inside the system then they will be able to write code---yet this code will be inert unless the system can interpret internal data structures as programs and actually execute them.
3. *Are changes persistent enough to encourage indefinite evolution?* If initial tinkering or later progress can be reset by accidentally closing a window, or preserved only through a convoluted process, then this discourages any long-term improvement of a system from within. For example, when developing a JavaScript application with web browser developer tools, it is possible to run arbitrary JavaScript in the console, yet these changes apply only to the running instance. After tinkering in the console with the advantage of concrete system state, one must still go back to the source code file and make the corresponding changes manually. When the page is refreshed to load the updated code, it starts from a fresh initial state. This means it is not worth using the *running* system for any programming beyond tinkering.
4. *Can you reprogram low-level infrastructure within the running system?* This is a hopefully faithful summary of how the COLAs work aims to go beyond Lisp and Smalltalk in this dimension.
5. *Can the user interface be arbitrarily changed from within the system?* Whether classed as "low-level infrastructure" or not, the visual and interactive aspects of a system are a significant part of it. As such, they need to be as open to re-programming as any other part of it to classify as truly self-sustainable.

## Key Components of Notational Freedom
We can propose three tiers of notational freedom:

1. Many syntaxes
2. Many syntaxes and notations
3. User-supplied syntaxes and notations

Consider the Web browser again; what would it take to achieve notational freedom in its JavaScript editor?

1. Again, we see that it does not yet satisfy the more modest condition of *syntactic freedom*. To achieve this, it would need to support *mood-specific languages* (see Section\ \ref{colas} below.) This means it would need some way of accepting user-supplied grammars (*Custom Grammars*) and some way of detecting which should be used for different parts of the source code (*Grammar Map.*) This latter task is made particularly troublesome because of a reliance on Implicit Structure (see Section\ \ref{explicit-structure}.)
2. *Even if* we get this syntactic freedom, we probably still have to force everything to fit in monospaced ASCII (or perhaps Unicode, as we see in the Nile/Gezira projects \cite{Nile,Gezira}.) We still do not have full *linguistic freedom,* whereby we could, for example, render mathematics with the proper layout and formatting (as this does not quite fit into a horizontal list of non-overlapping characters.) This may require a leap from specifying grammars (which describe legal symbol sequences) to interfaces (how input causes shapes to appear, and how these are rendered.) Call these *Custom Interfaces* and *Interface Map.*
3. Once we have such infrastructure in place, it is not so small a step to generalise these interfaces to things that need not resemble a language (e.g. a colour picker.) This gives us full *notational freedom.*

1. *Are there multiple syntaxes for textual notation?* Obviously, having more than one textual notation should count for notational diversity. However, for this dimension we want to take into account notations beyond the strictly textual, so we do not want this to be the only relevant question. Ideally, things should be weighted so that having a wide diversity of notations within some *narrow class* is not mistaken for notational diversity in a more global sense. We want to reflect that UNIX, with its vast array of different languages for different situations, can never be as notationally diverse as a system with many languages *and* many graphical notations, for example.
2. *Does the system make use of GUI elements?* This is a focused class of non-textual notations that many of our example systems exhibit.
3. *Is it possible to view and edit data as tree structures?* Tree structures are extremely common in programming, but they are usually worked with as text in some way. A few of our examples provide a graphical notation for this common data structure, so this is one way they can be differentiated from the rest.
4. *Does the system allow freeform arrangement and sizing of data items?* We still felt Boxer and spreadsheets exhibited something not covered by the previous three questions, which is this. Within their respective constraints of rendering trees as nested boxes and single-level grids, they both provide for notational variation that can be useful to the user's context. These systems *could* have decided to keep boxes neatly placed or cells all the same size, but the fact that they allow these to vary scores an additional point for notational diversity.

## Key Components of Explicit Structure
Given all this, how can we detect explicit structure in a programming system? Consider once again the Web browser. It has two major subdivisions of state: the DOM tree and the JavaScript object graph. For change, it only has those special JavaScript objects known as functions, worked on via the console and the source code view. We can examine these three areas separately:

\paragraph{DOM Tree.} The DOM tree is accessed manually through the Element Inspector and programmatically through JavaScript APIs, but is in both cases largely explicitly structured. The main exception involves the APIs taking HTML strings to create elements, and the initial stage of loading the HTML and JS source files. But once these structures have been recovered and built, much can take place without the intermediary of strings.

The other important exception to this occurs in SVG structures. SVG uses element *attributes* for composite data like matrix transformations, and hence has to store these as strings. For example, a node might have a `transform` of `translate(100,200)`. This means that, in order to move the element 10 units horizontally, the programmer must write code to extract the first co-ordinate as a number, add 10, and render the whole expression back to a string.

Related to attributes, CSS classes are a mixed case; on the one hand, the `style` attribute contains CSS source code, yet the JavaScript API does include properties for accessing this information explicitly.

\paragraph{JSOG.} The JavaScript object graph is also explicitly structured at runtime, while object definitions in the source code are obviously not.

\paragraph{JS Code.} Unlike the other two areas, JavaScript code is not explicitly structured even in the running system. There is a minimal level of structure: namely, the source file is split into Function objects (intervening lines with any side effects have already been executed and are inaccessible.) Beyond this, however, the body of each function is only accessible as a string. The obvious condition to meet here is that the AST should be navigable via some JavaScript API (*AST Access.*) Such a single level of explicit structure is shared with spreadsheets (where any structure within a cell is implicit) and databases (data within a column is implicitly structured.)

It appears that there are two realms in which explicit structure may be sought, corresponding to the Volatility Split: the contents of the source files vs. the content of the running system. Each of these could be further split between State and Change; in the Web browser, State largely has explicit structure in memory, but not on disk, and Change lacks it in both places.

\todo{Unix Files, spreadsheet, database}

# Exploring the Design Space
Here, we use technical dimensions to identify a new unexplored point in the design space of programming systems and envision a new design that could emerge from the analysis.

With a little work, technical dimensions can let us see patterns or gaps in the design space by plotting their values on a simple scatterplot. Here, we will look at two dimensions, *notational diversity*^[This is simply the dimension we named as *uniformity of notations* (\ref{dimension-uniformity-of-notations}), but flipped in the opposite direction.] and *self-sustainability*, for the following programming systems: Haskell, Jupyter notebooks, Boxer, HyperCard, the Web, spreadsheets, Lisp, Smalltalk, UNIX, and COLAs.

While our choice to describe dimensions as qualitative concepts was necessary for coming up with them, *some* way of generating numbers is clearly necessary for visualizing their relationships like this. For simplicity,^[There are undoubtedly many ways to turn our descriptions into various measures, with strengths and weaknesses for different purposes, but this is beyond the scope of the present paper. Here, we merely wish to demonstrate that such a thing is possible and show what one can do with the results.] we adopt the following scheme. For each dimension, we distill the main idea into several yes/no questions (Appendix\ \ref{making-dimensions-quantitative}) that capture enough of the distinctions we observe between the systems we wish to plot. Then, for each system, we add up the number of "yes" answers and obtain a plausible score for the dimension.

\begin{figure}
  \centering
  \includegraphics[width=0.6\linewidth]{plot-figure0.pdf}
  \caption{Example programming systems (or system families) measured against \emph{self-sustainability} and \emph{notational diversity}, revealing an absence of systems with a high degree of both. \label{fig:design-space-plot}}
\end{figure}

Figure\ \ref{fig:design-space-plot} shows the results we obtained with our sets of questions. It shows that application-focused systems span a range of notational diversity, but only within fairly low self-sustainability. The "OS-likes" (Section \ref{os-like-programming-systems}) cluster in an "island" at the right, sharing identical notational diversity and near-identical self-sustainability. There is also a conspicuous blank space at the top-right, representing an unexplored combination of high values on both dimensions. With other pairs of dimensions, we might take this as evidence of an oppositional relationship, such that more of one inherently means less of the other (perhaps looking for a single new dimension that describes this better.) In this case, though, there is no obvious conflict between having many notations and being able to change a system from within. Therefore, we interpret the gap as a new opportunity to try out: combine the self-sustainability of COLAs with the notational diversity of Boxer and Web development. In fact, this is more or less the forthcoming dissertation of the primary author.

# Conclusions
There is a renewed interest in developing new programming systems. Such systems go beyond the simple model of code written in a programming language using a more or less sophisticated text editor. They combine textual and visual notations, create programs through rich graphical interactions, and challenge accepted assumptions about program editing, execution and debugging. Despite the growing number of novel programming systems, it remains difficult to evaluate the design of programming systems and see how they improve over work done in the past. To address the issue, we proposed a framework of “technical dimensions” that captures essential characteristics of programming systems in a qualitative but rigorous way.

The framework of technical dimensions puts the vast variety of programming systems, past and present, on a common footing of commensurability. This is crucial to enable the strengths of each to be identified and, if possible, combined by designers of the next generation of programming systems. As more and more systems are assessed in the framework, a picture of the space of possibilities will gradually emerge. Some regions will be conspicuously empty, indicating unrealized possibilities that could be worth trying. In this way, a domain of "normal science" is created for the design of programming systems.

Specifically for this thesis, however, we can note the following: now that we have established such a framework, we can use it in the next two chapters to evaluate the systems they describe.
