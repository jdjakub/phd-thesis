\hypertarget{background}{%
\chapter{Background}\label{background}}

We will now set out the relevant background knowledge for the dissertation. This chapter is mostly concerned with existing concepts and work; this will let us introduce novel concepts and interpretations in the next chapter. This chapter is structured as follows:

* In Section\ \ref{programming-systems-vs-languages}, we define *programming systems* in contrast to programming languages and discuss why this is necessary.
* In Section\ \ref{examples-of-programming-systems}, we illustrate this with landmark examples of programming systems from the past.
* In Section\ \ref{precursors-of-the-three-properties}, we survey the existing patterns in programming that take us part of the way to the Three Properties.

# Programming Systems vs Languages
Many forms of software have been developed to enable programming. The classic form consists of a *programming language*, a text editor to enter source code, and a compiler to turn it into an executable program. Instances of this form are differentiated by the syntax and semantics of the language, along with the implementation techniques in the compiler or runtime environment. Since the advent of graphical user interfaces (GUIs), programming languages can be found embedded within graphical environments that increasingly define how programmers work with the language---for instance, by directly supporting debugging or refactoring. Beyond this, the rise of GUIs also permits diverse visual forms of programming, including visual languages and GUI-based end-user programming tools.

The classic essay by Gabriel\ \cite{PLrev} distinguishes the *languages* and *systems* paradigms in programming research. The topic of this thesis, and many of the examples we will use to illustrate concepts, rely on understanding this distinction and only make sense within the systems paradigm.
Therefore we shift our attention from *programming languages* to the more general notion of "software that enables programming"---in other words, *programming systems*.

\begin{defn}[Programming System]
A \emph{programming system} is an integrated and complete set of tools sufficient for creating, modifying, and executing programs. These will include notations for structuring programs and data, facilities for running and debugging programs, and interfaces for performing all of these tasks. Facilities for testing, analysis, packaging, or version control may also be present. Notations include programming languages and interfaces include text editors, but are not limited to these.
\end{defn}

This notion covers classic programming languages together with their editors, debuggers, compilers, and other tools. Yet it is intentionally broad enough to accommodate image-based programming environments like Smalltalk, operating systems like Unix, and hypermedia authoring systems like Hypercard, in addition to various other examples we will mention next.

# Examples of Programming Systems
We illustrate the notion of a programming system through a number of example systems. We draw these from three broad reference classes:

* Software ecosystems built around a text-based programming *language*. They consist of a set of tools such as compilers, debuggers, and profilers. These tools may exist as separate command-line programs, or within an Integrated Development Environment (IDE).
* Those that resemble an *operating system* (OS) in that they structure the execution environment and encompass the resources of an entire machine (physical or virtual). They provide a common interface for communication, both between the user and the computer, and between programs themselves.
* Programmable *applications*, typically optimised for a specific domain, offering a limited degree of programmability which may be increased with newer versions.

\joel{
We will proceed to detail some systems under this grouping. This will provide an intuition for the notion of a programming system and establish a collection of go-to examples for the rest of the paper.}

## Systems Based Around Languages

Text-based programming languages sit within programming systems whose boundaries are not explicitly defined. To speak of a programming system we must include a language with, at minimum, an editor and a compiler or interpreter. There is wiggle room in how we choose to circumscribe these elements. Do we mean a specific compiler version? Do we include common plugins or extensions? Still, we would expect these choices to have enough of a common overlap that we can proceed with analysis without worrying too much about the variations.

\paragraph{Java with the Eclipse ecosystem.}
The Java language\ \cite{Java} alone does not form a programming system, but it does if we consider it as embedded in an ecosystem of tools. A minimalistic delineation would consist of a text editor to write Java code and a command line compiler. A more realistic one is Java as embedded in the Eclipse IDE\ \cite{Eclipse}. The programming systems view permits us to see whatever there may be beyond the textual code. In the case of Eclipse, this includes the debugger, refactoring tools, testing and modelling tools, GUI designers, and so on.

\paragraph{Haskell tools ecosystem.}
Haskell is another language-focused programming system. It is used through the command-line *GHC*  compiler\ \cite{GHC} and *GHCi* REPL, alongside a text editor that provides features like syntax highlighting and auto-completion. Any editor that supports the Language Server Protocol\ \cite{LSP} will suffice to complete the programming system.

Haskell is mathematically rooted and relies on mathematical intuition for understanding many of its concepts. This background is also reflected in the notations it uses. In addition to the concrete language syntax for writing code, the ecosystem also uses an informal mathematical notation for writing about Haskell (e.g. in academic papers or on the whiteboard). This provides an additional tool for manipulating Haskell programs. Experiments on paper can provide a kind of rapid feedback that other systems may provide through live programming.

\paragraph{From REPLs to notebooks.}
A different kind of developer ecosystem that evolved around a programming language is the Jupyter notebook platform\ \cite{Jupyter}. In Jupyter, data scientists write scripts divided into notebook cells, execute them interactively and see the resulting data and visualisations directly in the notebook itself. This brings together the REPL, which dates back to conversational implementations of Lisp in the 1960s, with literate programming\ \cite{LiterateProg} used in the late 1980s in Mathematica 1.0\ \cite{Mathematica}.

As a programming system, Jupyter has a number of interesting characteristics. The primary outcome of programming is the notebook itself, rather than a separate application to be compiled and run. The code lives in a document format, interleaved with other notations. Code is written in small parts that are executed quickly, offering the user more rapid feedback than in conventional programming. A notebook can be seen as a trace of how the result has been obtained, yet one often problematic feature of notebooks is that some allow the user to run code blocks out-of-order. The code manipulates mutable state that exists in a "kernel" running in the background. Thus, retracing one's steps in a notebook is more subtle than in, say, Common Lisp\ \cite{CommonLisp}, where the `dribble` function would directly record the user's session to a file.

## OS-Like Programming Systems

"OS-likes" begin from the 1960s when it became possible to interact one-on-one with a computer. At first, time-sharing systems enabled interactive shared use of a computer via a teletype; smaller computers such as the PDP-1 and PDP-8 provided similar direct interaction, while 1970s workstations such as the Alto and Lisp Machines added graphical displays and mouse input. These *OS-like* systems stand out as having the totalising scope of *operating systems*, whether or not they are ordinarily seen as taking this role.

\paragraph{MacLisp and Interlisp.}
LISP 1.5\ \cite{LISP15} arrived before the rise of interactive computers, but the existence of an interpreter and the absence of declarations made it natural to use Lisp interactively, with the first such implementations appearing in the early 1960s. Two branches of the Lisp family\ \cite{LispEvolve}, MacLisp and the later Interlisp, embraced the interactive "conversational" way of working, first through a teletype and later using the screen and keyboard.

Both MacLisp and Interlisp adopted the idea of *persistent address space*. Both program code and program state were preserved when powering off the system, and could be accessed and modified interactively as well as programmatically using the *same means*. Lisp Machines embraced the idea that the machine runs continually and saves the state to disk when needed. Today, this is widely seen in cloud-based services like Google Docs and online IDEs. Another idea pioneered in MacLisp and Interlisp was the use of *structure editors*. These let programmers work with Lisp data structures not as sequences of characters, but as nested lists. In Interlisp, the programmer would use commands such as `*P` to print the current expression, or `*(2 (X Y))` to replace its second element with the argument `(X Y)`. The PILOT system\ \cite{Pilot} offered even more sophisticated conversational features. For typographical errors and other slips, it would offer an automatic fix for the user to interactively accept, modifying the program in memory and resuming execution.

\paragraph{Smalltalk.}
Smalltalk appeared in the 1970s with a distinct ambition of providing "dynamic media which can be used by human beings of all ages"\ \cite{PersonalDynMedia}. The authors saw computers as *meta-media* that could become a range of other media for education, discourse, creative arts, simulation and other applications not yet invented. Smalltalk was designed for single-user workstations with a graphical display, and pioneered this display not just for applications but also for programming itself. In Smalltalk 72, one wrote code in the bottom half of the screen using a structure editor controlled by a mouse, and menus to edit definitions. In Smalltalk-76 and later, this had switched to text editing embedded in a *class browser* for navigating through classes and their methods.

Similarly to Lisp systems, Smalltalk adopts the persistent address space model of programming where data remains in memory, but based on *objects* and *message passing* instead of *lists*. Any changes made to the system state by programming or execution are preserved when the computer is turned off. Lastly, the fact that much of the Smalltalk environment is implemented in itself makes it possible to extensively modify the system from within.

We include Lisp and Smalltalk in the OS-likes because they function as operating systems in many ways. On specialised machines, like the Xerox Alto and Lisp machines, the user started their machine directly in the Lisp or Smalltalk environment and was able to do everything they needed from *within* the system. Nowadays, however, this experience is associated with Unix and its descendants on a vast range of commodity machines.

\paragraph{Unix.}
Unix illustrates the fact that many aspects of programming systems are shaped by their intended target audience. Built for computer hackers\ \cite{Hackers}, its abstractions and interface are close to the machine. Although historically linked to the C language, Unix developed a language-agnostic set of abstractions that make it possible to use multiple programming languages in a single system. While everything is an object in Smalltalk, the ontology of the Unix system consists of files, memory, executable programs, and running processes. Note the explicit "stage" distinction here: Unix distinguishes between volatile *memory* structures, which are lost when the system is shut down, and non-volatile *disk* structures that are preserved. This distinction between types of memory is considered, by Lisp and Smalltalk, to be an implementation detail to be abstracted over by their persistent address space. Still, this did not prevent the Unix ontology from supporting a pluralistic ecosystem of different languages and tools.

\paragraph{Early and modern Web.}
The Web evolved\ \cite{DotCom} from a system for sharing and organising information to a *programming system*. Today, it consists of a wide range of server-side programming tools, JavaScript and languages that compile to it, and notations like HTML and CSS. As a programming system, the "modern 2020s web" is reasonably distinct from the "early 1990s web". In the early web, JavaScript code was distributed in a form that made it easy to copy and re-use existing scripts, which led to enthusiastic adoption by non-experts---recalling the birth of microcomputers like Commodore 64 with BASIC a decade earlier.

In the "modern web", multiple programming languages treat JavaScript as a compilation target, and JavaScript is also used as a language on the server-side. This web is no longer simple enough to encourage copy-and-paste remixing of code from different sites. However, it does come with advanced developer tools that provide functionality resembling early interactive programming systems like Lisp and Smalltalk. The *Document Object Model (DOM)* structure created by a web page is transparent, accessible to the user and modifiable through the built-in browser inspector tools. Third-party code to modify the DOM can be injected via extensions. The DOM almost resembles the tree/graph model of Smalltalk and Lisp images, lacking the key persistence property. This limitation, however, is being addressed by Webstrates\ \cite{Webstrates}.

\paragraph{COLAs.}
The one system that directly influenced our work is the Combined Object Lambda Architecture or COLA\ \cite{COLAs}: a small, expressive starting system designed to be self-improved into anything. It is described as a mutually self-implementing pair of abstractions: a structural object model (the "Object" in the acronym) and a behavioural Lisp-like language (the "Lambda"). COLA aims for maximal openness to modification, down to the basic semantics of object messaging and Lisp expressions.

The two remarkable features we see in the COLA idea are *self-sustainability* and a hint at *notational freedom*, which we will say more about in Section\ \ref{precursors-of-the-three-properties}. COLA inherits self-sustainability from the Smalltalk tradition and attempts to amplify it. This provides for what the authors refer to as *internal evolution* as a means for implementing *mood-specific languages:*

> Applying \[internal evolution\] locally provides scoped, domain-specific languages in which to express arbitrarily small parts of an application (these might be better called *mood-specific* languages). Implementing new syntax and semantics should be (and is) as simple as defining a new function or macro in a traditional language.

This ability for a programmer to express arbitrarily small parts of an application in a form they deem suitable is, in its fully general form, what we call Notational Freedom.

## Application-Focused Systems
The previously discussed programming systems were either universal, not focusing on any particular kind of application, or targeted at broad fields, such as Artificial Intelligence and symbolic data manipulation in Lisp's case. In contrast, the following examples focus on more narrow kinds of applications that need to be built. Many support programming based on rich interactions with specialised visual and textual notations.

\paragraph{Spreadsheets.}
The first spreadsheets became available in 1979 in VisiCalc\ \cite{VisiCalc, VisiCalc2} and helped analysts perform budget calculations. As programming systems, spreadsheets are notable for their two-dimensional grid substrate and their model of automatic re-evaluation. The programmability of spreadsheets developed over time, acquiring features that made them into powerful programming systems in a way VisiCalc was not. The final step was the 1993 inclusion of *macros* in Excel, later further extended with *Visual Basic for Applications*.

\paragraph{Graphical languages.}
Efforts to support programming without relying on textual code are "languages" in a more metaphorical sense. In these programming systems, programs are made out of graphical structures as in LabView\ \cite{LabView} or Programming-By-Example\ \cite{PBE}.

\paragraph{HyperCard.}
While spreadsheets were designed to solve problems in a specific application area, HyperCard\ \cite{HyperCard} was designed around a particular application format. Programs are "stacks of cards" containing multimedia components and controls such as buttons. These controls can be programmed with pre-defined operations like "navigate to another card", or via the HyperTalk scripting language for anything more sophisticated.

As a programming system, HyperCard is interesting for a couple of reasons. It effectively combines visual and textual notation. Programs appear the same way during editing as they do during execution. Most notably, HyperCard supports gradual progression from the "user" role to "developer": a user may first use stacks, then go on to edit the visual aspects or choose pre-defined logic until, eventually, they learn to program in HyperTalk.

# Precursors of the Three Properties
In the next chapter, we will go on to develop the Three Properties in detail. However, they do not leap out of a vacuum, but are rather developments of concepts that already exist in programming. Here, we will give a glossary of these existing concepts. In short:

* Self-Sustainability is foreshadowed by self-hosting compilers and reflection.
* Notational Freedom generalises domain-specific languages and polyglot programming.
* Explicit Structure already exists in the form of data structures and various editors.

## Precursors of Self-Sustainability
\paragraph{Self-hosting.} This describes a compiler that can compile its own source code into a functionally identical compiler program. We can then change the language understood by the compiler by changing the source code, compiling it with the current version, and discarding this version in favour of the new one. We can then rewrite the compiler's source code to make use of the new language feature. In this way, a programming language can be evolved using itself and we can call the language "self-hosting" (as a proxy for its compiler.)

\paragraph{Bootstrapping.} This is the process of getting a language into a self-hosting state \cite{Bootfrom0}. Suppose we design a novel language *NovLang* and we are happy to use C++ to build its compiler. Bootstrapping it consists of the following steps:

1. We write a NovLang compiler in NovLang, but we cannot run it yet.
2. We translate this by hand to C++ and build a temporary NovLang compiler.
3. We run this to compile the NovLang source code from step 1.
4. We obtain a runnable compiler for NovLang, which was written in NovLang and is now self-hosting.
5. We can now discard the C++ code.

\paragraph{Meta-circular.} This describes an interpreter that is written in its own language \cite{Metac}. This was first introduced in Lisp, in which one can write Lisp code to walk nodes in a data structure and treat them as Lisp expressions. If such code was compiled, it would result in an ordinary Lisp interpreter. Alternatively, if we feed such code into an existing Lisp interpreter, this new inner interpreter is now meta-circular. We could change the code to add a new language feature, in which case the inner interpreter would understand this slightly improved language. However, this approach does not scale, as each improvement would nest a further interpreter within the previous ones, multiplying the overhead to impractical levels.

\paragraph{Reflection.} This is the capacity for a system to display, explain or affect its own computational behaviour \cite{CompRefl}. It is sometimes explained with the word "aboutness": an ordinary program is "about" its domain (say, calculations), while a reflective program is also "about" its own computation. One test of this is the ability to make the tacit explicit \cite{ProcRefl}: entities that are normally implicit and unaddressable (such as the stack frame or variable binding environment) can be made so by an explicit command to reflect. An ordinary meta-circular interpreter cannot name its outer interpreter's data structures, but a reflective one can (and may be able to change how its outer interpreter works, and thus how it itself works.) This is developed exhaustively for Lisp-like languages in \cite{ProcRefl}. While reflection originates as a property of languages, the Self environment \cite{Self} provides an example in an interactive context. Any object on screen can call up an "outliner" object with a description of its prototype, private state and methods. This outliner is an object and can have the same operation applied to it.

\paragraph{Conclusion.} These concepts---self-hosting, bootstrapping, meta-circularity, and reflection---seem related but it is not obvious how. We interpret all of these as different manifestations of self-sustainability in special contexts, such as compilers or interpreters. In Chapter\ \ref{analysis} we will make this more precise by delving into the differences between compilers, interpreters, and interactive programming systems.

## Precursors of Notational Freedom
\paragraph{Use The Right Tool For The Job.} This is a widespread maxim in programming \cite{RightTool}. The ideal conditions capturing the spirit of this idea are as follows:

* What is "right" is subjectively determined by the programmer, even on a whim. When proposing a change to a language (e.g. Python), every user of the language is forced to confront the change, which invites debate about what is "right" for everyone. This could be sidestepped if each programmer could use what is right, for their own context, without this being forced on others.
* The "tool" might be an entire programming system or language, a design approach, or simply a notation in which to express a component.
* The "job" can be large (an entire project), small (a single expression) or anything in-between.

We do not expect to inhabit such ideal conditions. Even so, being aware of them lets us get a sense of how applicable this principle is and opens us to any low-hanging fruit in this area.

\paragraph{Polyglot Programming.} This is the practice of using multiple languages in a single project \cite{Polyglot}. Modules implemented in different languages need to share data and invoke each other's functions, for which there are several approaches:

* If the modules are separate programs, standard inter-process communication mechanisms like interchange file formats (JSON, XML), socket protocols, and Remote Procedure Calls are available.
* Polyglot programming *within* a shared process address space is trickier; the classic approach is to have different languages (e.g. C, Pascal) compile to a common *object file* format understood by the linker. This method is restricted to compile-time; for runtime sharing, languages use Foreign Function Interfaces (FFIs). This practice has been critiqued by Kell \cite{KellMMM} who advocates the use of "integration domains" instead.
* Polyglot programming within a single *file* is rare and restricted to fixed combinations. Perl and JavaScript support regexes written with a local syntax. HTML files include not only HTML but also JavaScript and CSS. C# supports Language INtegrated Queries (LINQ) which are a C# syntax adaptation of SQL queries. Unlike the freedom to choose any combination of existing languages for an entire project, these instances only permit use of a pre-approved set decided by the language designers.

\paragraph{Domain-Specific Languages (DSLs).} These go beyond Polyglot Programming by encouraging *custom* languages designed by the programmer for their problem domain. Where Polyglot Programming is about making the best use of *existing* languages designed by someone else, DSLs come closer to a *freedom* to use what one subjectively determines to be the best tool for one's job. JetBrain's MPS \cite{MPS} is an interactive programming system that encourages DSLs. The COLA design \cite{COLAs} supports "mood-specific languages" intended to span a range of scopes down to individual expressions, and the related OMeta \cite{OMeta} project is a platform for custom DSLs. "Reader Macros" in Lisp allow a programmer to use custom syntax for parts of the code.

\paragraph{Conclusion.} "Use The Right Tool For The Job" is the basic intuition behind Notational Freedom. In practice, we see a restricted version: use the right *pre-existing* tool for the job, as long as the job is no smaller than a single file. Even where DSLs permit custom "tools", we are restricted to *languages* rather than general *notations.* We will continue this discussion in more detail in Chapter\ \ref{analysis}.

## Precursors of Explicit Structure
\paragraph{Structure.} This is the relation of parts to wholes. An entire data structure is made up of smaller parts which reference each other. From a machine's point of view, a data structure is a graph of memory blocks connected by numerical pointers. For humans, it is a graph of dictionaries with named entries, some of which point at other dictionaries. Both of these models can be visualised as boxes with arrows.

\paragraph{Binary files.} These are *compacted* data structures. Unlike data structures in memory, which can be sparsely spread across large regions and intermingled with each other, a binary file contains the parts densely packed together without any unrelated data.

\paragraph{Syntax.} In programming, this refers to the "look and feel" of a language's textual source code. Formally, syntax is the set of rules defining legal and illegal symbol sequences. This idea can be metaphorically extended to non-sequential structures. For example, we can think of C `struct` definitions as setting out the valid "shape" of the parts of a data structure. The same applies to binary file formats.

\paragraph{Quantitative Syntax.} This is a term introduced by \cite{Infra} (page\ 13) for the pattern of prefixing a block with its length and using numerical pointers to link structures. A simple example is the Pascal String which begins with a length byte and continues for that many characters.

\paragraph{Qualitative Syntax.} This, in contrast to Quantitative Syntax, relies on special delimiters. For example, the C String begins right away with its characters, relying on a null byte to show up at some point and signal the end.

\paragraph{Text files, a.k.a. Strings.} These are lists of plain text characters. In programming, they are often containers for machine-readable structures as an alternative to binary files. Like the latter, they compact the structure, but in a way subject to the constraints of plain text and qualitative syntax. In practice, the structure in question is usually a tree, in which case the string is built as an *in-order* traversal. This means that special qualitative syntax (usually the matched bracket characters `(`, `[`, `{`, or `<`) is employed *around* substrings to encode the structure.

\paragraph{Parsing and serialising.} These convert between strings and structures. Parsing *recovers* structure implicit in a string, while serialising spins out the structure into a string.

\paragraph{Format error.} This is a part of a data structure that violates a decidable expectation of its consumer. For example, a syntactically valid file containing program source code might violate static typing rules or use a name that was not declared. Undecidable "semantic" rules like prohibiting division by zero are excluded from this term.

\paragraph{Syntax error.} This is a special case of format error where part of a text string violates a grammatical expectation of its consumer. 

\paragraph{Editors.} These are programs for creating various data structures in the form of files. Editors for 3D models, vector graphics, raster images, audio, and video understand the file formats and strive to save only valid files. It is usually not possible to even *express* a structure in the editor that contains a format error. Such cases are exceptional: for example, a 3D scene might open without errors in another 3D editor, but cause errors in a game engine according to the latter's additional requirements---perhaps it expects specific objects in the scene named `Player`, `Exit`, and so on. Nevertheless, for most editors and most use cases, the consumer-side validity rules are in harmony with the producer-side rules. 

\paragraph{Text Editors.} These are a type of editor for plain text files. However, they are widely used to write code in programming languages, which have extra syntax rules beyond the plain text format. Unlike most editors, text editors *can* save files that are invalid from the perspective of their consumers under realistic use-cases. These syntax errors are then discovered at the point of consumption.

\paragraph{Conclusion.} The basic intuition behind Explicit Structure is the *directness* experienced in creation and programming. Almost every data structure in computing has an editor with which one can manipulate the structure directly, and when programming we can act as if data structures have named parts that we can simply reference. This directness is interrupted by the standalone exception of text editors (on the creation side) and strings with machine-readable^[We have nothing to say about strings that contain natural language simply to be echoed out to the user (e.g. error messages.) However, our ideas about Explicit Structure may be applicable to cases where software must parse and interpret natural language too.] implicit content (on the programming side.)
