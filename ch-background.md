\hypertarget{background}{%
\chapter{Background}\label{background}}

The relevant background we will need to acquaint ourselves with falls into two halves: explaining the concept of a "programming system", and explaining how the Three Properties tie together existing concepts in programming. In Section\ \ref{programming-systems-vs-languages}, we define *programming systems* in contrast to programming languages and discuss why this is necessary. Then in Section\ \ref{examples-of-programming-systems}, we illustrate this with landmark examples of programming systems from the past. Finally, in Section\ \ref{precursors-of-the-three-properties}, we survey the existing patterns in programming that take us part of the way to the Three Properties.

# Programming Systems vs Languages
Many forms of software have been developed to enable programming. The classic form consists of a *programming language*, a text editor to enter source code, and a compiler to turn it into an executable program. Instances of this form are differentiated by the syntax and semantics of the language, along with the implementation techniques in the compiler or runtime environment. Since the advent of \acp{GUI}, programming languages can be found embedded within graphical environments that increasingly define how programmers work with the language---for instance, by directly supporting debugging or refactoring. Beyond this, the rise of \acp{GUI} also permits diverse visual forms of programming, including visual languages and \ac{GUI}-based end-user programming tools (we will survey these in\ Section\ \ref{novel-notations-versus-notational-freedom}).

The classic essay by \textcite{PLrev} distinguishes the *languages* and *systems* paradigms in programming research. *Languages* are formal mathematical models of syntax and semantics; researchers might ask what an expression *means* and include code samples in papers. *Systems,* in contrast, are running pieces of software whose current state changes according to the effects of program code. Researchers studying systems are likely to be more concerned with what code *does* to a running system in a specific state instead of the more abstract language properties.

The topic of this thesis, and many of the examples we will use to illustrate concepts, rely on understanding this distinction and only make sense within the systems paradigm.
Therefore we shift our attention from *programming languages* to the more general notion of "software that enables programming"---in other words, *programming systems*.

\pagebreak

\begin{defn}[Programming System]
\label{def:programming-system}
A \emph{programming system} is an integrated and complete set of tools sufficient for creating, modifying, and executing programs. These will include notations for structuring programs and data, facilities for running and debugging programs, and interfaces for performing all of these tasks. Facilities for testing, analysis, packaging, or version control may also be present. Notations include programming languages and interfaces include text editors, but are not limited to these.
\end{defn}

A word about terminology: if we view languages in the sense of Gabriel's "languages paradigm", then it is a "type error" to include languages in the above definition. Abstract mathematical models of syntax and semantics are not the same as software. However, language *implementations* are software. We will use the term "language" to abbreviate "language implementation" since we do not use the other meaning in this dissertation.

With that said, our above notion of programming system covers classic programming languages together with their editors, debuggers, compilers, and other tools. Yet it is intentionally broad enough to *also* accommodate image-based programming environments like Smalltalk, operating systems like Unix, and hypermedia authoring systems like Hypercard, in addition to various other examples we will mention next.

# Examples of Programming Systems
We illustrate the notion of a programming system through a number of example systems. We are not trying to exhaustively cover all possible systems, but simply give an impression based on major examples. We draw them from three broad reference classes:

* Software ecosystems built around a text-based programming *language*. They consist of a set of tools such as compilers, debuggers, and profilers. These tools may exist as separate command-line programs, or within an \ac{IDE}.
* Those that resemble an \ac{OS} in that they structure the execution environment and encompass the resources of an entire machine (physical or virtual). They provide a common interface for communication, both between the user and the computer, and between programs themselves.
* Programmable *applications*, typically optimised for a specific domain, offering a limited degree of programmability which may be increased with newer versions.

\joel{
We will proceed to detail some systems under this grouping. This will provide an intuition for the notion of a programming system and establish a collection of go-to examples for the rest of the paper.}

## Systems Based Around Languages

Text-based programming languages sit within programming systems whose boundaries are not explicitly defined. To speak of a programming system we must include a language with, at minimum, an editor and a compiler or interpreter.

There is wiggle room in how we choose to circumscribe these elements. Do we mean a specific compiler version? Do we include common plugins or extensions? Still, we would expect these choices to have enough of a common overlap that we can proceed in analysis without worrying too much about the variations. We will revisit this point in Section\ \ref{the-circumscription-problem-of-systems}.

\paragraph{Java with the Eclipse ecosystem.}
The Java language\ \parencite{Java} alone does not form a programming system, but it does if we consider it as embedded in an ecosystem of tools. A minimalistic delineation would consist of a text editor to write Java code and a command line compiler. A more realistic one is Java as embedded in the Eclipse \ac{IDE}\ \parencite{Eclipse}. The programming systems view permits us to see whatever there may be beyond the textual code. In the case of Eclipse, this includes the debugger, refactoring tools, testing and modelling tools, \ac{GUI} designers, and so on.

\paragraph{Haskell tools ecosystem.}
Haskell is another language-focused programming system. It is used through the command-line *GHC*  compiler\ \parencite{GHC} and *GHCi* REPL, alongside a text editor that provides features like syntax highlighting and auto-completion. Any editor that supports the Language Server Protocol\ \parencite{LSP} will suffice to complete the programming system.

Haskell is mathematically rooted and relies on mathematical intuition for understanding many of its concepts. This background is also reflected in the notations it uses. In addition to the concrete language syntax for writing code, the ecosystem also uses an informal mathematical notation for writing about Haskell (\eg{} in academic papers or on the whiteboard). This provides an additional tool for manipulating Haskell programs. Experiments on paper can provide a kind of rapid feedback that other systems may provide through live programming.

\paragraph{From REPLs to Computational Notebooks.}
A different kind of developer ecosystem that evolved around a programming language is the Jupyter notebook platform\ \parencite{Jupyter}. In Jupyter, data scientists write scripts divided into notebook cells, execute them interactively and see the resulting data and visualisations directly in the notebook itself. This brings together the \ac{REPL}, which dates back to conversational implementations of Lisp in the 1960s, with literate programming\ \parencite{LiterateProg} used in the late 1980s in Mathematica 1.0\ \parencite{Mathematica}.

As a programming system representative of Computational Notebooks\ \parencite{CompNotebooks}, Jupyter has several interesting characteristics. The primary outcome of programming is the notebook itself, rather than a separate application to be compiled and run. The code lives in a document format, interleaved with other notations. Code is written in small parts that are executed quickly, offering the user more rapid feedback than in conventional programming. A notebook can be seen as a trace of how the result has been obtained, yet one often problematic feature of notebooks is that some allow the user to run code blocks out-of-order. The code manipulates mutable state that exists in a "kernel" running in the background. Thus, retracing one's steps in a notebook is more subtle than in, say, Common Lisp\ \parencite{CommonLisp}, where the `dribble` function would directly record the user's session to a file.

## OS-Like Programming Systems

"\ac{OS}-likes" date from the 1960s when it became possible to interact one-on-one with a computer. At first, time-sharing systems enabled interactive shared use of a computer via a teletype; smaller computers such as the PDP-1 and PDP-8 provided similar direct interaction, while 1970s workstations such as the Alto and Lisp Machines added graphical displays and mouse input. These *\ac{OS}-like* systems stand out as having the totalising scope of *operating systems*, whether or not they are ordinarily seen as taking this role.

\paragraph{MacLisp and Interlisp.}
LISP 1.5\ \parencite{LISP15} arrived before the rise of interactive computers, but the existence of an interpreter and the absence of declarations made it natural to use Lisp interactively, with the first such implementations appearing in the early 1960s. Two branches of the Lisp family\ \parencite{LispEvolve}, MacLisp and the later Interlisp, embraced the interactive "conversational" way of working, first through a teletype and later using the screen and keyboard.

Both MacLisp and Interlisp adopted the idea of *persistent address space*. Both program code and program state were preserved when powering off the system, and could be accessed and modified interactively as well as programmatically using the *same means*. Lisp Machines embraced the idea that the machine runs continually and saves the state to disk when needed. Today, this *persistence* (Definition\ \ref{def:persistent}) is widely seen in cloud-based services like Google Docs and online \acp{IDE}. Another idea pioneered in MacLisp and Interlisp was the use of *structure editors*. These let programmers work with Lisp data structures not as sequences of characters, but as nested lists. In Interlisp, the programmer would use commands such as `*P` to print the current expression, or `*(2 (X Y))` to replace its second element with the argument `(X Y)`. The PILOT system\ \parencite{Pilot} offered even more sophisticated conversational features. For typographical errors and other slips, it would offer an automatic fix for the user to interactively accept, modifying the program in memory and resuming execution. This is something that is only possible with Lisp's *naïve pokeability* (Definition\ \ref{def:naive-pokeability}).

\paragraph{Smalltalk.}
Smalltalk appeared in the 1970s with a distinct ambition of providing "dynamic media which can be used by human beings of all ages"\ \parencite{PersonalDynMedia}. The authors saw computers as *meta-media* that could become a range of other media for education, discourse, creative arts, simulation and other applications not yet invented. Smalltalk was designed for single-user workstations with a graphical display, and pioneered this display not just for applications but also for programming itself. In Smalltalk 72, one wrote code in the bottom half of the screen using a structure editor controlled by a mouse, and menus to edit definitions. In Smalltalk-76 and later, this had switched to text editing embedded in a *class browser* for navigating through classes and their methods.

Similarly to Lisp, Smalltalk adopts the persistent address space model of programming where data remains in memory, but based on *objects* and *message passing* instead of *lists*. Any changes made to the system state by programming or execution are preserved when the computer is turned off (this is *persistence* again, Definition\ \ref{def:persistent}). Lastly, the fact that much of the Smalltalk environment is implemented in itself makes it possible to extensively modify the system from within: Smalltalk exhibits Self-Sustainability.

An important descendant of Smalltalk is *ThingLab*\ \parencite[Figure\ \ref{fig:thinglab}]{ThingLab}---later succeeded by ThingLab II\ \parencite{ThingLab2}---which augments the class browser with a *picture pane.* ThingLab was a *constraint-based* programming system in the vein of Sketchpad\ \parencite{Sketchpad}. ThingLab provided not only a library of pre-existing constraints and objects for use in electrical and geometric simulations, but was designed as a "kit-building kit" with the ability to create new types of objects and constraints via the Smalltalk language. The extent to which ThingLab and Sketchpad can define new graphical constructs, and give them semantics (as opposed to merely drawing a picture) qualifies for *Notational Freedom* as well as Self-Sustainability.

\begin{figure}
\centering
\includegraphics[width=\linewidth]{thinglab.png}
\caption[ThingLab screenshot.]{ThingLab could be used to explore constraint-based simulations such as electrical circuits, but could also leverage Smalltalk to create new objects and constraints that could be graphically specified and instantiated.}
\label{fig:thinglab}
\end{figure}

We include Lisp and Smalltalk in the OS-likes because they function as operating systems in many ways. On specialised machines, like the Xerox Alto and Lisp machines, the user started their machine directly in the Lisp or Smalltalk environment and was able to do everything they needed from *within* the system. Nowadays, however, this experience is associated with Unix and its descendants on a vast range of commodity machines.

\paragraph{Unix.}
Unix fits our Definition\ \ref{def:programming-system} for programming systems and illustrates the ways that a system is shaped for its intended target audience. Built for computer hackers\ \parencite{Hackers}, its abstractions and interface are close to the machine. Although historically linked to the C language, Unix developed a language-agnostic set of abstractions that make it possible to use multiple programming languages in a single system. While everything is an object in Smalltalk, the ontology of Unix consists of files, memory, executable programs, and running processes. Note the explicit "stage" distinction here: Unix distinguishes between volatile *memory* structures, which are lost when the system is shut down, and non-volatile *disk* structures that are preserved. This distinction between types of memory is considered, by Lisp and Smalltalk, to be an implementation detail to be abstracted over by their persistent address space. Still, this did not prevent the Unix ontology from supporting a pluralistic ecosystem of different languages and tools. Thus Unix is distinguished as a *meta-*programming system, supporting the creation and interaction of different programming systems within it. We will go into more detail on these points in Section\ \ref{the-unix-paradigm}.

\paragraph{Early and modern Web.}
The Web evolved\ \parencite{DotCom} from a system for sharing and organising information to a *programming system*. Today, it consists of a wide range of server-side programming tools, \ac{JS} and languages that compile to it, notations like HTML and CSS, and the sophisticated developer tools included in browsers. As a programming system, the "modern 2020s web" is reasonably distinct from the "early 1990s web". In the early web, \ac{JS} code was distributed in a form that made it easy to copy and re-use existing scripts, which led to enthusiastic adoption by non-experts---recalling the birth of microcomputers like Commodore 64 with BASIC a decade earlier.

In the "modern web", multiple programming languages treat \ac{JS} as a compilation target. \ac{JS} is also used as a language on the server side. This web is no longer simple enough to encourage copy-and-paste remixing of code from different sites. However, as we observed in Section\ \ref{web-pages-web-apps-and-browsers} it does come with advanced developer tools providing functionality resembling that of Lisp and Smalltalk. The \ac{DOM} almost resembles the tree/graph model of Smalltalk and Lisp images, lacking the key *persistence* property. Such a limitation is being addressed by efforts like Webstrates\ \parencite{Webstrates}, which synchronise the \ac{DOM} between the server and clients. Thus if a client changes an element, this can be mirrored on the server side and saved as the ground truth of the web page.

\paragraph{COLAs.}
The one system that directly influenced our work is the \Acl{COLA}, or \ac{COLA}\ \parencite{COLAs}: a small, expressive starting system designed for open evolution by its user. It is described as a mutually self-implementing pair of abstractions: a structural object model (the "Object" in the name) and a behavioural Lisp-like language (the "Lambda"). \ac{COLA} aims for maximal openness to modification, down to the basic semantics of object messaging and Lisp expressions.

The two remarkable features we see in the \ac{COLA} idea are *self-sustain\-ability* and a hint at *notational freedom*, which we will exhibit presently. \ac{COLA} inherits self-sustainability from the Smalltalk tradition and attempts to amplify it. This provides for what the authors refer to as *internal evolution* as a means for implementing *\acp{MSL}:*

> Applying \[internal evolution\] locally provides scoped, domain-specific languages in which to express arbitrarily small parts of an application (these might be better called *mood-specific* languages). Implementing new syntax and semantics should be (and is) as simple as defining a new function or macro in a traditional language.

An example of a \ac{MSL} is the one reported in\ \textcite[p.\ 4]{Steps08} for concisely specifying how TCP packets should be processed. The report also notes:

> The header formats are parsed from the diagrams in the original specification documents, converting "ascii art" into code to manipulate the packet headers.

A representative diagram and the grammar for parsing it (itself written in a grammar-definition DSL) can be found in\ \textcite[p.\ 44, Figures\ \ref{fig:packet-format} and\ \ref{fig:packet-grammar}]{Steps07}. They note:

> We can now define accessors for the fields of an IP packet header simply by drawing its structure. The following looks like documentation, but it's a valid *program.*

\begin{figure}
% tyvm https://tex.stackexchange.com/questions/220707/using-scalebox-to-decrease-the-size-of-listings-code
\begin{lrbox}{\mywidebox}%
\begin{lstlisting}[language=,basicstyle=\footnotesize\ttfamily,keepspaces=true,columns=flexible,breaklines=false]
+-------------+-------------+-------------------------+----------+----------------------------------------+
| 00 01 02 03 | 04 05 06 07 | 08 09 10 11 12 13 14 15 | 16 17 18 | 19 20 21 22 23 24 25 26 27 28 29 30 31 |
+-------------+-------------+-------------------------+----------+----------------------------------------+
|   version   |  headerSize |      typeOfService      |                     length                        |
+-------------+-------------+-------------------------+----------+----------------------------------------+
|                     identification                  |   flags  |                 offset                 |
+---------------------------+-------------------------+----------+----------------------------------------+
|        timeToLive         |         protocol        |                    checksum                       |
+---------------------------+-------------------------+---------------------------------------------------+
|                                               sourceAddress                                             |
+---------------------------------------------------------------------------------------------------------+
|                                             destinationAddress                                          |
+---------------------------------------------------------------------------------------------------------+
\end{lstlisting}
\end{lrbox}
\scalebox{0.7}{\usebox{\mywidebox}}
\caption[TCP packet \ac{MSL}.]{ASCII art diagram for an IP packet, in principle both human-readable and machine-parseable\ \parencite[p.\ 44]{Steps07}.}
\label{fig:packet-format}
\end{figure}

\lstdefinelanguage{packets}{
  morekeywords={self, let},
  morestring=[b]",
}

\begin{figure}
\begin{lstlisting}[language=packets,basicstyle=\footnotesize\ttfamily,keepspaces=true,columns=flexible,breaklines=false]
    structure :=
         error  = ->[self error: ['"structure syntax error near: " , [self contents]]]
           eol  = '\r''\n'* | '\n''\r'*
         space  = [ \t]
       comment  = [-+] (!eol .)* eol
            ws  = (space | comment | eol)*
             _  = space*
        letter  = [a-zA-Z]
         digit  = [0-9]
    identifier  = id:$(letter (letter | digit)*) _  -> [id asSymbol]
        number  =                    num:$digit+ _  -> [Integer fromString: num base: '10]
       columns  =       '|'                         -> (structure-begin self)
                         ( _ num:number             -> [bitmap at: column
                                                              put: (set bitpos num)]
                             (num:number)* '|'      -> (let ()
                                                         (set bitpos num)
                                                         (set column [
                                                            [self readPosition] - anchor
                                                          ]))
                         )+ eol ws                  -> [bitmap at: column
                                                              put: (set width [bitpos + '1])]
           row  =        ( n:number                 -> (set row n)
                           ) ? '|'                  -> (let ()
                                                         (set anchor [self readPosition])
                                                         (set column '0))
                         _ ( id:identifier '|'      -> (structure-field self id)
                             _ )+ eol ws            -> (set row [row + width])
          name  =      id:identifier (!eol .)* eol  -> (structure-end id)
       diagram  =     ws columns row+ name | error
\end{lstlisting}
\caption[ASCII Packet diagram parser]{Grammar for parsing the ASCII art into a data structure definition\ \parencite[p.\ 44]{Steps07}. Message sends in square brackets \texttt{[]} and Lisp-like expressions in parentheses \texttt{()} are visible; these respectively relate to the "Object" and "Lambda" halves of the underlying \ac{COLA}.}
\label{fig:packet-grammar}
\end{figure}

We see this as the extreme end of what a \ac{MSL} is capable of. The ability for a programmer to express arbitrarily small parts of an application in a form they deem suitable is, in its fully *general* form, what we call Notational Freedom. With such a capability, code could be synthesised from *real* tabular diagrams^[We are thinking of vector graphics formats here, but computer vision techiques would allow someone to use raster screenshots if they so desired.] of packet headers, not just those rendered with ASCII characters.

We mentioned how these \acp{MSL} were supported by \ac{COLA}'s *internal evolution,* which we interpret as another term for Self-Sustainability (or, more precisely, its practice). This is provided for by the \ac{COLA} architecture, composed of an object model for structure\ \parencite{OROM} and a Lisp-like language for behaviour\ \parencite{OECM}.

The object model is a late-bound, Smalltalk-style objects and messaging environment called "Id", and we will describe it briefly here. An Id *object* is a block of state which can change as a result of messages received by it. Messaging (analogous to *method invocation* in Java-style OOP) works as follows:

* A message is sent by first *bind*-ing its name to its *method implementation*, which is specific code that gets run in the context of the receiver $R$. This binding is a dynamic operation that can use runtime conditions to make its decision---even create a method implementation on the fly. This *late binding* contrasts with *early binding* where the name is bound to the implementation statically and thus holds for the entire running lifetime of the system.
* This "bind" step is accomplished by sending a further message; this time, to the receiver's *vtable* $V(R)$. A vtable is another object that maps "message name" to "implementation code"---analogous to a "class" in Java-style OOP.
* Because this initial "bind" message is itself a message send, it triggers a similar "bind" to *its* vtable $V(V(R))$, and so on: recursing up the vtable-chain, and terminating at a base case.
* The higher levels of the vtable chain mean that different kinds of vtables can be supported (as well as different kinds of "kinds of vtables", and so on). Each kind of vtable may implement the "bind" operation in its own way. For example, one kind of vtable could employ a simple dictionary mapping names to implementations. A different kind might build the code for the implementation just-in-time, without looking anything up in a data structure.

## Application-Focused Systems
The previously discussed programming systems were either universal, not focusing on any particular kind of application, or targeted at broad fields, such as Artificial Intelligence and symbolic data manipulation in Lisp's case. In contrast, the following examples focus on narrower application domains. Many support programming based on rich interactions with specialised visual and textual notations.

\paragraph{Spreadsheets.}
The first spreadsheets became available in 1979 in VisiCalc\ \parencite{VisiCalc, VisiCalc2} and helped analysts perform budget calculations. As programming systems, spreadsheets are notable for their two-dimensional grid substrate and their model of automatic re-evaluation. The programmability of spreadsheets developed over time, acquiring features that made them into powerful programming systems in a way VisiCalc was not. A major step was the 1993 inclusion of *macros* in Excel, later further extended with *Visual Basic for Applications* and more recently with *lambda functions*\ \parencite{Lambda}.

\paragraph{HyperCard.}
While spreadsheets were designed to solve problems in a specific application area, HyperCard\ \parencite{HyperCard} was designed around a particular application format. Programs are "stacks of cards" containing multimedia components and controls such as buttons. These controls can be programmed with pre-defined operations like "navigate to another card", or via the HyperTalk scripting language for anything more sophisticated.

As a programming system, HyperCard is interesting for a couple of reasons. It effectively combines visual and textual notation. Programs appear the same way during editing as they do during execution. Most notably, HyperCard supports gradual progression from the "user" role to "developer": a user may first use stacks, then go on to edit the visual aspects or choose pre-defined logic until, eventually, they learn to program in HyperTalk.

\paragraph{Graphical languages.}
Efforts to support programming without relying on textual code are "languages" in a more metaphorical sense. In the "boxes-and-wires" style of LabView\ \parencite{LabView} programs are made out of graphical structures. There are also the Programming-By-Example/Demonstration subgenres\ \parencite{YWIMC,WWID} in which a general program is automatically generated by the user supplying sample behaviours and hints.^[Although there is nothing inherently graphical about programming by example or demonstration, the two definitive books cited show a strong graphical orientation.]

# Precursors of the Three Properties
In the next chapter, we will go on to develop the Three Properties in detail. However, they do not leap out of a vacuum, but are rather developments of concepts that already exist in programming. For each of the Three Properties, we will give a "glossary" of these existing concepts and finish with a "Conclusion" entry. In short:

* Self-Sustainability is foreshadowed by self-hosting compilers and reflection.
* Notational Freedom generalises \acp{DSL} and polyglot programming.
* Explicit Structure already exists widely in the form of data structures and various editors; programming is the exception.

## Precursors of Self-Sustainability
### Self-Hosting
This describes a compiler that can compile its own source code into a functionally identical compiler program. We can then change the language understood by the compiler by changing the source code, compiling it with the current version, and discarding this version in favour of the new one. We can then rewrite the compiler's source code to make use of the new language feature. In this way, a programming language can be evolved using itself. We can call the language "self-hosting" as a proxy for its compiler.

### Bootstrapping
This is the process of getting a language into a self-hosting state\ \parencite{Bootfrom0}. Suppose we design a novel language *NovLang* and we are happy to use C++ to build its compiler. Bootstrapping it consists of the following steps; see Figure\ \ref{fig:bootstrap} for a *tombstone diagram*\ \parencite{Tombstone}:

1. We write a NovLang compiler in NovLang, but we cannot run it yet.
2. We translate this by hand to C++ and build a temporary NovLang compiler.
3. We run this to compile the NovLang source code from step 1.
4. We obtain a runnable compiler for NovLang, which was written in NovLang and is now self-hosting.
5. We can now discard the C++ code.

\begin{figure}
\centering
\includegraphics[width=\linewidth]{bootstrapping.png}
\caption[Bootstrapping Tombstone Diagrams.]{C++ is used to implement a temporary compiler for NovLang, which then compiles a self-hosted compiler to a runnable binary. This binary, plus its original NovLang source code, can then be mutually used to evolve NovLang in the manner shown.}
\label{fig:bootstrap}
\end{figure}

### Meta-Circular
This describes an interpreter that is written in its own language\ \parencite{Metac}. This was first introduced in Lisp, in which one can write Lisp code to walk nodes in a data structure and treat them as Lisp expressions. If such code is compiled, it results in an ordinary Lisp interpreter. Alternatively, if we feed such code into an existing Lisp interpreter, this new inner interpreter is now meta-circular. We could change the code to add a new language feature, in which case the inner interpreter would understand this slightly improved language. However, this approach does not scale, as each improvement would nest a further interpreter within the previous ones, multiplying the overhead to impractical levels.

### Reflection
This is the capacity for a system to display, explain or affect its own computational behaviour during run time\ \parencite{CompRefl}. It is sometimes explained with the word "aboutness": an ordinary program is "about" its domain (say, calculations), while a reflective program is also "about" its own computation. One test of this is the ability to make the tacit explicit\ \parencite{ProcRefl}: entities that are normally implicit and unaddressable (such as the stack frame or variable binding environment) can be made so by an explicit command to reflect. An ordinary meta-circular interpreter cannot name its outer interpreter's data structures, but a reflective one can (and may be able to change how its outer interpreter works, and thus how it itself works). This is developed exhaustively for Lisp-like languages in\ \textcite{ProcRefl}. While reflection originates as a property of languages, the Self environment\ \parencite{Self} provides an example in an interactive context. Any object on screen can call up an "outliner" object with a description of its prototype, private state and methods. This outliner is an object and can have the same operation applied to it\ \parencite{SelfOutliner}.

\hypertarget{ss-precursor-conclusion}{%
\subsubsection{Conclusion}\label{ss-precursor-conclusion}}
These concepts (self-hosting, bootstrapping, meta-circularity, and reflection) seem related but it is not obvious how. We interpret all of these as different manifestations of self-sustainability in special contexts, such as compilers or interpreters. In Chapter\ \ref{analysis} we will make this more precise by delving into the differences between compilers, interpreters, and interactive programming systems.

## Precursors of Notational Freedom
### \URTFJ
This is a widespread maxim in programming\ \parencite{RightTool} that we encountered in Definition\ \ref{def:right-tool}. The ideal conditions capturing the spirit of this idea are as follows.

* *Subjective Preference:* What is "right" is subjectively determined by the programmer, even on a whim. Ordinarily, when proposing a change to a language (\eg{} Python), every user of the language is forced to confront the change, which invites debate about what is "right" for everyone. This could be sidestepped if each programmer could use what is right, for their own context, without this being forced on others. This is meant even in a collaborative context: ideally, each collaborator may use their chosen tool while a common infrastructure or format allows their efforts to cohere.
* *Metaphorical Tool:* The "tool" might be an entire programming system or language, a design approach, or simply a notation in which to express a component.
* *Range of Scopes:* The "job" can be large (an entire project), small (a single expression) or anything in-between.

Even though these are ideal conditions that we do not inhabit, being aware of them lets us get a sense of how applicable this principle is and opens us to any low-hanging fruit in this area. We will now review the limited extent to which we can apply the principle in our actual environment of programming.

### Polyglot Programming
This is the practice of using multiple languages in a single project\ \parencite{Polyglot}. Modules implemented in different languages need to share data and invoke each other's functions, for which there are several approaches:

* If the modules are separate programs, standard inter-process communication mechanisms like interchange file formats (JSON, XML), socket protocols, and Remote Procedure Calls are available.
* Polyglot programming *within* a shared process address space is trickier; the classic approach is to have different languages (\eg{} C, Pascal) compile to a common *object file* format understood by the linker. This method is restricted to compile time; for run-time sharing, languages use Foreign Function Interfaces (FFIs). This practice has been critiqued by\ \textcite{KellMMM} who advocates the use of "integration domains" instead.
* Polyglot programming within a single *file* is rare and restricted to fixed combinations. Perl and \ac{JS} support regexes written with a local syntax. PHP embeds HTML, which in turn includes \ac{JS} and CSS. C# supports Language INtegrated Queries (LINQ) which are a C# syntax adaptation of SQL queries. Unlike the freedom to choose *any* combination of existing languages for an entire project, these instances only permit use of a *pre-approved* set decided by the language designers.

### \Aclp{DSL}
These go beyond Polyglot Programming by encouraging *custom* languages designed by the programmer for their problem domain. Where Polyglot Programming is about making the best use of *existing* languages designed by someone else, \acp{DSL} come closer to a *freedom* to use what one subjectively determines to be the best tool for one's job. JetBrains' MPS\ \parencite{MPS} is an interactive programming system that encourages \acp{DSL}. "Reader Macros" in Lisp allow a programmer to use custom syntax for parts of the code. The \ac{COLA} design\ \parencite{COLAs} supports "\aclp{MSL}" intended to span a range of scopes down to individual expressions, and the related OMeta\ \parencite{OMeta} project is a platform for custom \acp{DSL}. The Eco editor\ \parencite{Eco} also supports \acp{MSL}, as does JetBrains' MPS\ \parencite{MPS}.

\hypertarget{nf-precursor-conclusion}{%
\subsubsection{Conclusion}\label{nf-precursor-conclusion}}
"\URTFJ" is the basic intuition behind Notational Freedom. In practice, we see a restricted version: use the right *pre-existing* tool for the job, as long as the job is no smaller than a single file. Occasionally, a pre-approved set of different languages are available within a single file. In the rare cases that support the use of custom "tools" within a file, we risk being restricted to *languages* rather than general *notations.* We will list the rare examples that provide for the latter when we give a full definition of Notational Freedom in the next chapter (Section\ \ref{notational-freedom}). 

## Precursors of Explicit Structure
### Structure
This is the relation of parts to wholes. An entire data structure is made up of smaller parts which reference each other. From one perspective, a data structure is a graph of memory blocks connected by numerical pointers. At a more human-friendly level, it is a graph of dictionaries with named entries, some of which point at other dictionaries. Both of these models can be visualised as boxes with arrows.

### Binary files
This is a very general term referring to any file that cannot be treated as plain text. We see binary files as *compacted* data structures. Unlike data structures in memory, which can be sparsely spread across large regions and intermingled with each other, a binary file will often contain the parts densely packed together without any unrelated data. If not, the binary file serves as an uncompacted "image" of a region or regions of memory.

### Syntax
In programming, this refers to the "look and feel" of a language's textual source code. Formally, syntax is the set of rules defining legal and illegal symbol sequences. This idea can be metaphorically extended to non-sequential structures. For example, we can think of C `struct` definitions as setting out the valid "shape" of the parts of a data structure. The same applies to binary file formats.

### Quantitative Syntax
This is a term introduced by\ \textcite[p.\ 13]{Infra} for the pattern of prefixing a block with its length and using numerical pointers to link structures. A simple example is the Pascal String which begins with a length byte and continues for that many characters. Binary files rely primarily on quantitative syntax.

### Qualitative Syntax
This, in contrast to Quantitative Syntax, relies on special delimiters. For example, the C String begins right away with its characters, relying on a null byte to show up at some point and signal the end.

### Text files, \ie{} Strings
These are lists of plain text characters. In programming, they are often containers for machine-readable structures as an alternative to binary files. Like the latter, they compact the structure, but in a way subject to the constraints of plain text and qualitative syntax. In practice, the structure in question is a tree, in which case the string is built as an *in-order* traversal.^[The alternative pre-order and post-order traversals result in machine-friendly prefix and postfix serialisations, which can build the structure back up via a stack. However, these are not easily readable or writeable for humans.] This means that special qualitative syntax (usually the matched bracket characters `([{<`, occasionally `begin`/`end` or whitespace as indentation) is employed *around* substrings to encode the structure.

### Parsing and serialising
These convert between strings and structures. Parsing *recovers* structure implicit in a string, while serialising spins out the structure into a string.

### Format error
This is a part of a data structure that violates a decidable expectation of its consumer. For example, a syntactically valid file containing program source code might violate static typing rules or use a name that was not declared. Undecidable "semantic" rules like prohibiting division by zero are excluded from this term (see Figure\ \ref{fig:format-errors}).

\begin{figure}
\centering
\scalebox{0.85}{
  \input{../../fig/format-errors.tex}
}
\caption[Format errors, syntax errors, and type errors.]{Format errors include syntax errors, type errors and some ``semantic'' errors as long as they are decideable.}
\label{fig:format-errors}
\end{figure}

### Syntax error
This is a specific type of format error where part of a text string violates a grammatical expectation of its consumer.

### Editors
These are programs for creating various data structures in the form of files. Editors for 3D models, vector graphics, raster images, audio, and video understand the file formats and strive to save only valid files. It is usually not possible to even *express* a structure in the editor that contains a format error. Such cases are exceptional: for example, a 3D scene might open without errors in another 3D editor, but cause errors in a game engine according to the latter's additional requirements---perhaps it expects specific objects in the scene named `Player`, `Exit`, and so on. Nevertheless, for most editors and most use cases, the consumer-side validity rules are in harmony with the producer-side rules. 

### Text Editors
These are a type of editor for plain text files. However, they are widely used to write code in programming languages, which have extra syntax rules beyond the plain text format. Unlike most editors, text editors *can* save files that are invalid from the perspective of their consumers under realistic use-cases. These syntax errors are then discovered at the point of consumption.

\hypertarget{es-precursor-conclusion}{%
\subsubsection{Conclusion}\label{es-precursor-conclusion}}
The basic intuition behind Explicit Structure is the *directness* experienced in creation and programming. Almost every data structure in computing has an editor with which one can manipulate the structure directly, and when programming we can act as if data structures have named parts that we can simply reference. This directness is interrupted by the standalone exception of text editors (on the creation side) and strings with machine-readable^[We are unconcerned with strings that contain natural language simply to be echoed out to the user (\eg{} error messages). However, our ideas about Explicit Structure could be applicable to cases where software must parse and interpret natural language too.] implicit content (on the programming side).

# Review and Next Steps
We have established the frame of *programming systems* through Definition\ \ref{def:programming-system} and numerous examples. This prepared us to survey the various *precursors* of our Three Properties, which are more common than the Properties themselves, as aspects of different systems without limiting ourselves to languages. Now that we have this context, we will go deeper into our model of programming systems and finally give full definitions of the Three Properties, which will lay the groundwork for our contribution in Chapter\ \ref{bl}.