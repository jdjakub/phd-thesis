
# Interaction

\mybox{How do users manifest their ideas, evaluate the result, and generate new ideas in response?}

An essential aspect of programming systems is how the user interacts with them when creating programs. Take the standard form of statically typed, compiled languages with straightforward library linking: here, programmers write their code in a text editor, invoke the compiler, and read through error messages they get. After fixing the code to pass compilation, a similar process might happen with runtime errors.

Other forms are yet possible. On the one hand, some typical interactions like compilation or execution of a program  may not be perceptible at all. On the other hand, the system may provide various interfaces to support the plethora of other interactions that are often important in programming, such as looking up documentation, managing dependencies, refactoring or pair programming.

We focus on the situations where a programmer interacts with the system to construct a program with a desired behaviour. To analyse them, we use the concepts of *gulf of execution* and *gulf of evaluation* from *The Design of Everyday Things*\ \parencite{Norman}.

## Dimension: feedback loops
In using a system, one first has some idea and attempts to make it exist in the software; the gap between the user's goal and the means to execute the goal is known as the *gulf of execution*. Then, one compares the result actually achieved to the original goal in mind; this crosses the *gulf of evaluation*. These two activities comprise the *feedback loop* through which a user gradually realises their desires in the imagination, or refines those desires to find out "what they actually want".

A system must contain at least one such feedback loop, but may contain several at different levels or specialised to certain domains. For each of them, we can separate the gulf of execution and evaluation as independent legs of the journey, with possibly different manners and speeds of crossing them.

\begin{figure}
  \centering
  \includegraphics[width=0.5\linewidth]{feedback-loops.png}
  \caption[Feedback Loops in a statically-checked language]{The nested feedback loops of a statically-checked programming language.\label{fig:feedback-loops}}
\end{figure}

For example, we can analyse statically checked *programming languages* (e.g. Java, Haskell) into several feedback loops (Figure \ref{fig:feedback-loops}):

1. Programmers often think about design details and calculations on a whiteboard or notebook, even before writing code. This *supplementary medium* has its own feedback loop, even though this is often not automatic.
2. The code is written and is then put through the static checker. An error sends the user back to writing code. In the case of success, they are "allowed" to run the program, leading into cycle 3.

    - The execution gulf comprises multiple cycles of the supplementary medium, plus whatever overhead is needed to invoke the compiler (such as build systems).
    - The evaluation gulf is essentially the waiting period before static errors or a successful termination are observed. Hence this is bounded by some function of the length of the code (the same cannot be said for the following cycle 3).

3. With a runnable program, the user now evaluates the *runtime* behaviour. Runtime errors can send the user back to writing code to be checked, or to tweak dynamically loaded data files in a similar cycle.

    - The execution gulf here may include multiple iterations of cycle 2, each with its own nested cycle 1.
    - The *evaluation* gulf here is theoretically unbounded; one may have to wait a very long time, or create very specific conditions, to rule out certain bugs (like race conditions) or simply to consider the program as fit for purpose.
    - By imposing *static checks*, some bugs can be pushed earlier to the evaluation stage of cycle 2, reducing the likely size of the cycle 3 *evaluation* gulf.
    - On the other hand, this can make it harder to write statically valid code, which may increase the number of level-2 cycles, thus increasing the total *execution* gulf at level 3.
    - Depending on how these balance out, the total top-level feedback loop may grow longer or shorter.

## Example: immediate feedback
The specific case where the *evaluation* gulf is minimised to be imperceptible is known as *immediate feedback*. Once the user has caused some change to the system, its effects (including errors) are immediately visible. This is a key ingredient of *liveness*, though it is not sufficient on its own. (See *Relations.*)

The ease of achieving immediate feedback is obviously constrained by the computational load of the user's effects on the system, and the system's performance on such tasks. However, such "loading time" is not the only way feedback can be delayed: a common situation is where the user has to manually ask for (or "poll") the relevant state of the system after their actions, even if the system finished the task quickly. Here, the feedback could be described as *immediate upon demand* yet not *automatically demanded*. For convenience, we choose to include the latter criterion---automatic demand of result---in our definition of immediate feedback.

In a *REPL* or *shell*, there is a *main* cycle of typing commands and seeing their output, and a *secondary* cycle of typing and checking the command line itself. The output of commands can be immediate, but usually reflects only part of the total effects or even none at all. The user must manually issue further commands afterwards, to check the relevant state bit by bit. The secondary cycle, like all typing, provides immediate feedback in the form of character "echo", but things like syntax errors generally only get reported *after* the entire line is submitted. This evaluation gulf has been reduced in the JavaScript console of web browsers, where the line is "run" in a limited manner on every keystroke. Simple commands without side-effects,^[Of course, these are detected via some conservative over-approximation which excludes expressions that *might* side-effect.] such as calls to pure functions, can give instantly previewed results---though partially typed expressions and syntax errors will not trigger previews.

## Example: direct manipulation
Direct manipulation \parencite{DirectManip} is a special case of an immediate feedback loop. The user sees and interacts with an artefact in a way that is as similar as possible to real life; this typically includes dragging with a cursor or finger in order to physically move a visual item, and is limited by the particular haptic technology in use.

Naturally, because moving real things with one's hands does not involve any waiting for the object to "catch up",^[In some situations, such as steering a boat with a rudder, there is a delay between input and effect. But on closer inspection, this delay is between the rudder and the boat; we do not see the hand pass through the wheel like a hologram, followed by the wheel turning a second later. In real life, objects touched directly give immediate feedback; objects controlled further down the line might not!] direct manipulation is necessarily an immediate-feedback cycle. If, on the other hand, one were to move a figure on screen by typing new co-ordinates in a text box, then this could still give *immediate feedback* (if the update appears instant and automatic) but would *not* be an example of direct manipulation.

*Spreadsheets* contain a feedback loop for direct manipulation of values and formatting, as in any other WYSIWYG application. Here, there is feedback for every character typed and every change of style. This is not the case in the other loop for formula editing and formula invocation. There, we see a larger execution gulf for designing and typing formulas, where feedback is only given upon committing the formula by pressing enter. This makes it an "immediate feedback" loop only *on-demand*, as defined above.

## Dimension: modes of interaction
The possible interactions in a programming system are typically structured so that interactions, and the associated feedback loops, are only available in certain *modes*. For example, when creating a new project, the user may be able to configure the project through a conversational interface like `npm init` in modern JavaScript. Such interactions are no longer available once the project is created. This idea of interaction modes goes beyond just programming systems, appearing in software engineering methodologies. In particular, having a separate *implementation* and *maintenance* phase would be an example of two modes.

*Editing vs debugging.* A good example is the distinction between _editing_ and _debugging_ mode. When debugging a program, the user can modify the program state and get (more) immediate feedback on what individual operations do. In some systems, one can even modify the program itself during debugging. Such feedback loops are not available outside of debugging mode.

*Lisp systems* sometimes distinguish between _interpreted_ and _compiled_ mode. The two modes do not differ just in the efficiency of code execution, but also in the interactions they enable. In the interpreted mode, code can be tested interactively and errors may be corrected during code execution (see _Error response_). In compiled mode, the program can only be tested as a whole. The same two modes also exist, for example, in some Haskell systems where the REPL uses an interpreter (GHCi) distinct from the compiler (GHC).

*Jupyter notebooks.* A programming system may also unify modes that are typically distinct. The Jupyter notebook environment does not have a distinct debugging mode; the user runs blocks of code and receives the result. The single mode can be used to quickly try things out, and to generate the final result, partly playing the role of both debugging and editing modes. However, even Jupyter notebooks distinguish between editing a document and running code.

## Dimension: abstraction construction
A necessary activity in programming is going between abstract schemas and concrete instances. Abstractions can be constructed from concrete examples, first principles or through other methods. A part of the process may happen in the programmer's mind: they think of concrete cases and come up with an abstract concept, which they then directly encode in the system. Alternatively, a system can support these different methods directly.

One option is to construct abstractions *from first principles*. Here, the programmer starts by defining an abstract entity such as an interface in object-oriented programming languages. To do this, they have to think what the required abstraction will be (in the mind) and then encode it (in the system).

Another option is to construct abstractions *from concrete cases*. Here, the programmer uses the system to solve one or more concrete problems and, when they are satisfied, the system guides them in creating an abstraction based on their concrete case(s). In a programming language IDE this manifests as the "extract function" refactor, whereas in other systems we see approaches like macro recording.

*Pygmalion.* In Pygmalion \parencite{Pygmalion}, all programming is done by manipulating concrete icons that represent concrete things. To create an abstraction, one can use "Remember mode", which records the operations done on icons and makes it possible to bind this recording to a new icon.

*Jupyter notebook.* In Jupyter notebooks, one is inclined to work with concrete things, because one sees previews after individual cells. This discourages creating abstractions, because then one would not be able to look inside at such a fine grained level.

*Spreadsheets.* Up until the recent introduction of lambda expressions into Excel\ \parencite{Lambda}, spreadsheets have been relentlessly concrete, without any way to abstract and reuse patterns of computation other than copy-and-paste.

## Concept: implicit vs. explicit Structure
Explicit Structure refers to the sense of working with data *directly* rather than through some other medium. It is a property of the interaction between users, interfaces, and underlying data structures. We split the life-cycle of a data structure into two halves:

* On the *producer side*, the data structure is created or edited using some interface.
* On the *consumer side*, a programmer is writing code that uses the data structure.

Explicit Structure is hard to define positively because it is the default state of affairs across much of computing, with programming being the notable exception. On the producer side, explicit structure is exhibited by a vector graphics editor like Inkscape: one simply draws a diagram with shapes and saves it as a file. On the consumer side, Explicit Structure looks like a programmer navigating through named parts of the diagram structure:

\begin{lstlisting}[language=JavaScript]
svg.root_nodes[1].children[2].fill_color = '#ff00ff';
\end{lstlisting}

The best way we have found to model Explicit Structure is as a lack of Implicit Structure, where we follow the split between *producer* and *consumer* concerns. On the producer side, we have an editor with an interface creating and changing a data structure. This is saved and passed onto consumers, which can be collaborators using editors or a programmer writing code to use the data structure. The definition of Implicit Structure we are interested in is about how much users or programmers must be *aware* of it and devote cognitive resources to working with it. On the producer side, this manifests as which types of syntax errors or more general *format* errors they are able to save and pass on to consumers. On the consumer side, Implicit Structure is revealed by the amount of code we have to write to deal with parsing, serialising, escaping, loading and saving, and so on. Therefore, we suggest the following two dimensions which add up to Implicit Structure.

## Dimension: format errors
How many different types of format errors can be introduced, saved as invalid structures, and passed to consumers, such that they will halt with an error? For example, text editors allow all possible syntax errors to be saved and several format errors (\eg{} type mismatches and use of undeclared names). However, a text editor interface that refused to save invalid files could form part of a system with Explicit Structure.

Block or structure editors may prevent all format errors from being saved, which would constitute the minimal value of this dimension. Arguably, the maximal value would be found in a hex editor: any data at all may be saved and passed on. This can violate even the basic rules of text files through non-printable characters, while any and all internal rules of a binary file can also be easily broken.

## Dimension: string wrangling effort
How much code has to be written to convert between Implicit and Explicit Structure? Explicit Structure implies a minimal value for this and would look something like the following:

\begin{lstlisting}[language=JavaScript]
data = load('filename')
data.foo.bar = 'baz';
\end{lstlisting}

Here, there are zero lines of string wrangling. Only one line, translating between the filesystem and the internal system namespaces, is required to prepare the data structure for use.

If such a `load` function is already present, then users experience no string wrangling effort for the use cases of this function, \ie{} the file formats it supports. If the function does not exist, and a user must write string wrangling code on an ad-hoc basis, this dimension is correspondingly high relative to that format. Suppose the user factors this ad-hoc string wrangling into their *own* implementation of the `load` function; this implementation effort would count towards the dimension, but would pay for itself in the reduced string wrangling effort thereafter; this situation would lie somewhere between the previous two.

These considerations all establish scores for this dimension *relative* to a particular file format or string syntax. These could be aggregated to form a score for a particular program which uses several such formats. However, if we are trying to assess the *programming system* along this dimension, we would have to somehow aggregate across all possible programs one could create with the system, including the various different formats they are likely to include.^[There is a large variety of *existing* data storage formats (\eg{} JSON and XML) and an infinite variety of potential *custom* formats that could be created on an ad-hoc basis (\eg{} chat messages containing special escape sequences).]

Recognising that different programming systems are targeted at different goals and have differing strengths and weaknesses, the possibility space could be refined into all *likely* programs or use cases of the programming system, weighted by the probability of a user of the system wanting to create such a program. This opens up further decisions about this user and whether we should additionally aggregate across possible (or likely) users of the system. We defer such complications to future work.

## Relations
- _Errors_ (Section \ref{errors}) A longer evaluation gulf delays the detection of errors. A longer execution gulf can increase the *likelihood* of errors (e.g. writing a lot of code or taking a long time to write it). By turning runtime bugs into statically detected bugs, the combined evaluation gulfs can be reduced.
- _Adoptability_ (Section \ref{adoptability}): The *execution* gulf is concerned with software using and programming in general. The time taken to realise an idea in software is affected by the user's familiarity and the system's *learnability*.
- _Notation_ (Section \ref{notation}): Feedback loops are related to *notational structures*. In a system with multiple notations, each notation may have different associated feedback loops. The motto "The thing on the screen is supposed to be the actual thing" \parencite{NakedObjects}, adopted in the context of live programming, relates _liveness_ to a direct connection between surface notation and internal data. The idea is that interactable objects should be equipped with faithful behaviour, instead of being intangible shadows cast by the hidden *real* object.

# Notation

\mybox{How are the different textual / visual programming notations related?}

Programming is always done through some form of notation. We consider notations in the most general sense and include any structured gesture using textual or visual notation. Textual notations primarily include  programming languages, but also things like configuration files. Visual notations include graphical programming languages. Other kinds of structured gestures include user interfaces for constructing visual elements used in the system.

## Dimension: notational structure

In practice, most programming systems use multiple notations. Different notations can play different roles in the system. On the one hand, multiple _overlapping notations_ can be provided as different ways of programming the same aspects of the system. In this case, each notation may be more suitable to certain kinds of users, but may have limitations (for example, a visual notation may have a limited expressive power). On the other hand, multiple _complementing notations_ may be used as the means for programming different aspects of the system. In this case, programming the system requires using multiple notations, but the notations may be specialised for the task at hand; think of how HTML describes document structure while JavaScript specifies its behaviour.

## Example: overlapping notations
A programming system may provide multiple notations for programming the same aspect of the system. This is typically motivated by an attempt to offer easy ways of completing different tasks: say, a textual notation for defining abstractions and a visual notation for specifying concrete structures. The crucial issue in this kind of arrangement is *synchronising* the different notations; if they have different characteristics, this may not be a straightforward mapping. For example, source code may allow more elaborate abstraction mechanisms like  loops, which might appear as visible repetition in the visual notation. What should such a system do when the user edits a single object that resulted from such repetition? Similarly, textual notation may allow incomplete expressions that do not have an equivalent in the visual notation. For programming systems that use *overlapping notations*, we need to describe how the notations are synchronised.

*Sketch-n-Sketch* \parencite{SnS} employs overlapping notations for creating and editing SVG and HTML documents. The user edits documents in an interface with a split-screen structure that shows source code on the left and displayed visual output on the right. They can edit both of these and changes are propagated to the other view. The code can use abstraction mechanisms (such as functions) which are not completely visible in the visual editor (an issue we return to in *expression geography* below). Sketch-n-Sketch can be seen as an example of a *projectional editor*.^[Technically, traditional projectional editors usually work more directly with the abstract syntax tree of a programming language.\note{TODO: Insert some more references to research on "projectional editors"}]

*UML Round-tripping.* Another example of a programming system that utilises the *overlapping notations* structure are UML design tools that display the program both as source code and as a UML diagram. Edits in one result in automatic update of the other. An example is the Together/J^[\url{https://www.mindprod.com/jgloss/togetherj.html}] system. To solve the issue of notation synchronisation, such systems often need to store additional information in the textual notation, typically using a special kind of code comment. In this example, after the user re-arranges classes in UML diagrams, the new locations need to be updated in the code.

## Example: complementing notations
A programming system may also provide multiple complementing notations for programming different aspects of its world. Again, this is typically  motivated by the aim to make specifying certain aspects of programming easier, but it is more suitable when the different aspects can be more clearly separated. The key issue for systems with complementing notations is how the different notations are connected. The user may need to use both notations at the same time, or they may need to progress from one to the next level when solving increasingly complex problems. In the latter case, the learnability of progressing from one level to the next is a major concern.

*Spreadsheets and HyperCard.* In Excel, there are three different complementing notations that allow users to specify aspects of increasing complexity: (i) the visual grid, (ii) formula language and (iii) a macro language such as Visual Basic for Applications. The notations are largely independent and have different degrees of expressive power. Entering values in a grid cannot be used for specifying new computations, but it can be used to adapt or run a computation, for example when entering different alternatives in What-If Scenario Analysis. More complex tasks can be achieved using formulas and macros. A user gradually learns more advanced notations, but experience with a previous notation does not help with mastering the next one. The approach optimises for easy learnability at one level, but introduces a hurdle for users to surmount in order to get to the second level. The notational structure of *HyperCard* is similar and consists of (i) visual design of cards, (ii) visual programming (via the GUI) with a limited number of operations, and (iii) HyperTalk for arbitrary scripting.

*Boxer and Jupyter.* Boxer \parencite{Boxer} uses *complementing notations* in that it combines a visual notation (the layout of the document and the boxes of which it consists) with textual notation (the code in the boxes). Here, the textual notation is always nested within the visual. The case of Jupyter notebooks is similar. The document structure is graphical; code and visual outputs are nested as editable cells in the document. This arrangement is common in many other systems such as Flash or Visual Basic, which both combine visual notation with textual code, although one is not nested in the other.

## Dimension: primary and secondary notations
In practice, most programming systems use multiple notations. Even in systems based on traditional programming languages, the *primary notation* of the language is often supported by *secondary notations* such as annotations encoded in comments and build tool configuration files. However, it is possible for multiple notations to be primary, especially if they are *overlapping* as defined earlier.

*Programming languages.* Programming systems built around traditional programming languages typically have further notations or structured gestures associated with them. The primary notation in Unix is the C programming language. Yet this is enclosed in a programming *system* providing a multi-step mechanism for running C code via the terminal, assisted by secondary notations such as shell scripts. Some programming systems attempt to integrate tools that normally rely on secondary notations into the system itself, reducing the number of secondary notations that the programmer needs to master. For example, in the Smalltalk descendant Pharo, versioning and package management is done from within Pharo, removing the need for secondary notation such as `git` commands and dependency configuration files.^[The tool for versioning and package management in Pharo can still be seen as an *internal* domain-specific language and thus as a secondary notation, but its basic structure is *shared* with other notations in the Pharo system.]

*Haskell.* In Haskell, the primary notation is the programming language, but there are also a number of secondary notations. Those include package managers (e.g. the `cabal.project` file) or configuration files for Haskell build tools. More interestingly, there is also an informal mathematical notation associated with Haskell that is used when programmers discuss programs on a whiteboard or in academic publications\ \parencite{AlgebraProg}. The idea of having such a mathematical notation dates back to the *Report on Algol 58* \parencite{Alg58}, which explicitly defined a "publication language" for "stating and communicating problems" using Greek letters and subscripts.

## Dimension: expression geography
A crucial feature of a notation is the relationship between the structure of the notation and the structure of the behaviour it encodes. Most importantly, do *similar expressions* in a particular notation represent *similar behaviour*?^[See the similar discussion of "density" in\ \parencite{NotYetCraft}.] Visual notations may provide a mapping that is more or less direct. On the one hand, similar-looking code in a block language may mean very different things. On the other hand, the similar looking design of two HyperCard cards will result in similar looking cards---the mapping between the notation and the logic is much more direct.

*C/C++ expression language.* In textual notations, this may easily not be the case. Consider the two C conditionals:

- `if (x==1) { ... }` evaluates the Boolean expression `x==1` to determine whether `x` equals `1`, running the code block if the condition holds.
- `if (x=1) { ... }` *assigns* `1` to the variable `x`. In C, assignment is an expression *returning* the assigned value, so the result `1` is interpreted as `true` and the block of code is *always* executed.

A notation can be designed to map better to the logic behind it, for example, by requiring the user to write `1==x`. This solves the above problem as `1` is a literal rather than a variable, so it cannot be assigned to (`1=x` is a compile error).

## Dimension: uniformity of notations
One common concern with notations is the extent to which they are uniform. A uniform notation can express a wide range of things using just a small number of concepts. The primary example here is S-expressions from Lisp.

_Lisp systems._ In Lisp, source code is represented in memory as S-expressions, which can be manipulated by Lisp primitives. An S-expression is either an atom, or a pair of S-expressions written `(s1 . s2)`. By convention, an S-expression `(s1 . (s2 . (s3 . nil)))` represents a list, written as `(s1 s2 s3)`. Notational uniformity in Lisp is closely linked to uniformity of representation.^[Notations generally are closely linked to representation in that the notation may mirror the structures used for program representation. \textcite{Externalize} refer to this as a distinction between "dead" notation and "live" representation forms).] In the idealised model of LISP 1.5, the data structures represented by an S-expression are what exists in memory. In real-world Lisp systems, the representation in memory is more complex. A programming system can also take a very different approach and fully separate the notation from the in-memory representation.

The S-expression basis of Lisp gives such systems robust macro processing as part of their semantics: expanding a macro revises the list structure of the code that uses the macro. Combining these makes it possible to define extensions to the system in Lisp, with syntax indistinguishable from Lisp. Moreover, it is possible to write a program that constructs another Lisp program and not only run it interpretively (using the \texttt{eval} function) but compile it at runtime (using the \texttt{compile} function) and execute it. Many domain-specific languages, as well as prototypes of new programming languages (such as Scheme), were implemented this way. Lisp the language is, in this sense, a "programmable programming language" \parencite{LispIntro,ProgProgLang}.

## Concept: notational freedom
Notational Freedom is the full realisation of the maxim "Use The Right Tool For The Job". The ideal is where a component at any scale can be expressed in a *notation* that is particularly suited to it. There are several related concepts of which Notational Freedom is the full generalisation: *syntactic* freedom, *linguistic* freedom, and syntactic/linguistic/notational *plurality.*

*Syntactic Freedom* is where custom *syntaxes,* or languages restricted to the conventions of code (linear display, plain text without formatting), may be defined and used as desired by the programmer. An established instance of this is Domain-Specific Languages. Syntactic Freedom allows a programmer, if dissatisfied with the following:

```
vec_a.mul(cos(ang_b/2))
     .add(vec_b.mul(cos(ang_a/2)))
     .add(vec_a.cross(vec_b))
```

to define a local syntax that allows him to rewrite it as the following (note the Unicode infix operators and juxtaposition-as-multiplication):

```
cos(ang_b/2) vec_a + cos(ang_a/2) vec_b + vec_a × vec_b
```

*Linguistic Freedom* is a relaxation on the constraints of syntactic freedom to support language-like notations with non-linear display and different text styles. Standard mathematical notation is an example of such a notation; Linguistic Freedom would permit the following development of the previous example:

$$\cos\left(\frac{b}{2}\right)\mathbf{a} + \cos\left(\frac{a}{2}\right)\mathbf{b} + \mathbf{a} \times \mathbf{b}$$

Syntactic/linguistic/notational *plurality* is where different notations of the respective class are available to the programmer, but only as a *pre-determined* set of options; the programmer does not have *freedom* to define his own and use them wherever he wants. Examples are the inclusion of *regex* syntax in Perl and JavaScript; the three-way collaboration of HTML, JavaScript and CSS in web development; LINQ in C#.

Full *Notational* Freedom includes syntactic and linguistic freedom (which themselves include plurality) but goes beyond, allowing arbitrary graphical notations. An example of what one could do with this freedom is replace a hex string with a colour picker interface.

The way we measure Notational Freedom is as the minimisation of several *penalty* dimensions which we turn to presently. The work involved in creating the syntax, language or notation is excluded; the dimensions only measure the amount of *extra* work it takes to get the programming system to accept them in the contexts they are intended for. This is minimised if the custom syntax, language, or notation can be "slotted in" once it exists, with no resistance from the system. 

## Dimension: custom syntax effort
The work required to use a custom syntax, not counting that required to specify the syntax itself (\eg{} as a grammar). \ac{COLA}\ \parencite{COLAs} and OMeta\ \parencite{OMeta} score low on this, since they are specifically designed for this purpose. Most programming languages have *infinite* custom syntax effort, because their parsers are separate programs that adhere to a fixed grammar that cannot be changed by statements in the language. This includes \ac{JS} despite its inclusion of a regex sub-syntax, HTML despite its inclusion of \ac{JS} and CSS, and C# despite its LINQ sub-language for queries; these examples may exhibit syntactic *plurality*, but there is no way to include a user-supplied syntax for use in the source code.

## Dimension: custom language effort
The work required to use custom language-like notation beyond syntax, not counting that required to implement the rendering and interaction. Most programming languages, \ac{COLA}, and OMeta get an infinite score here, while MPS\ \parencite{MPS} and Eco\ \parencite{Eco} score low.

## Dimension: custom notation effort
The work required to use custom graphical notation beyond what we called "language-like" notations, not counting that required to implement the rendering and interaction. Only Eco, owing to a screenshot showing inclusion of a picture, scores non-infinite on this dimension. From their discussion in Section\ 9.2 of \ \textcite{Eco}, it is likely to score High or Moderate rather than Low because arbitrary graphical notations are a novel unexplored use case for the system for which it has not been optimised.

## References
*Cognitive Dimensions of Notations* \parencite{CogDims} provide a comprehensive framework for analysing individual notations, while our focus here is on how multiple notations are related and how they are structured. It is worth noting that the Cognitive Dimensions also define _secondary notation_, but in a different sense to ours. For them, secondary notation refers to whether a notation allows including redundant information such as colour or comments for readability purposes.

The importance of notations in the practice of science, more generally, has been studied by \parencite{PaperTools} as "paper tools". These are formula-like entities which can be manipulated by humans in lieu of experimentation, such as the aforementioned mathematical notation in Haskell: a "paper tool" for experimentation on a whiteboard. Programming notations are similar, but they are a way of communicating with a machine; the experimentation does not happen on paper alone.

## Relations
- *Interaction* (Section \ref{interaction}): The feedback loops that exist in a programming system are typically associated with individual notations. Different notations may also have different feedback loops.
- *Adoptability* (Section \ref{adoptability}): Notational structure can affect learnability. In particular, complementing notations may require (possibly different) users to master multiple notations. Overlapping notations may improve learnability by allowing the user to edit the program in one way (perhaps visually) and see the effect in the other notation (such as code.)
- *Errors* (Section \ref{errors}). A process that merely records user actions in a sequence (such as text editing) will, in particular, record any *errors* the user makes and defer their handling to later use of the data, keeping the errors *latent*. A process which instead treats user actions as edits to a structure, with constraints and correctness rules, will be able to catch errors at the moment they are introduced and ensure the data coming out is error-free.

# Conceptual Structure
\mybox{How is meaning constructed? How are internal and external incentives balanced?}

## Dimension: conceptual integrity vs. openness

The evolution of programming systems has led away from *conceptual integrity* towards an intricate ecosystem of specialised technologies and industry standards. Any attempt to unify parts of this ecosystem into a coherent whole will create *incompatibility* with the remaining parts, which becomes a major barrier to adoption. Designers seeking adoption are pushed to focus on localised incremental improvements that stay within the boundaries established by existing practice. This creates a tension between how highly they can afford to value conceptual elegance, and how open they are to the pressures imposed by society. We will turn to both of these opposite ends---*integrity* and *openness*---in more detail.

## Example: conceptual integrity

> I will contend that Conceptual Integrity is the most important consideration in system design. It is better to have a system omit certain anomalous features and improvements, but to reflect one set of design ideas, than to have one that contains many good but independent and uncoordinated ideas. (Fred\ Brooks, *Aristocracy, Democracy and System Design;* \textcite{brooks95aristo}

\note{The essence of this style can be captured by the phrase “the right thing”. To such a designer it is important to get all of the following characteristics right: Simplicity … Correctness … Consistency … Completeness --- Richard Gabriel}

> Conceptual integrity arises not (simply) from one mind or from a small number of agreeing resonant minds, but from sometimes hidden co-authors and the thing designed itself. (Richard \ Gabriel, *Designed As Designer;* \textcite{DesignedAsDesigner}

Conceptual integrity strives to reduce complexity at the source; it employs *unified concepts* that may *compose orthogonally* to generate diversity. Perhaps the apotheosis of this approach can be found in early Smalltalk and Lisp environments, which were complete programming systems built around a single language. They incorporated capabilities commonly provided *outside* the programming language by operating systems and databases. Everything was done in one language, and so everything was represented with the datatypes of that language. Likewise, the libraries and idioms of the language were applicable in all contexts. Having a *lingua franca* avoided much of the friction and impedance mismatches inherent to multi-language systems. A similar drive exists in the Python programming language, which follows the principle that “There should be one---and preferably only one---obvious way to do it” in order to promote community consensus on a single coherent style.

In addition to Smalltalk and Lisp, many programming languages focus on one kind of data structure \parencite{Mem-mods}:

- In COBOL, data consists of nested records as in a business form.
- In Fortran, data consists of parallel arrays.
- In SQL, data is a set of relations with key constraints.
- In scripting languages like Python, Ruby, Lua, and JavaScript, much data takes the form of string-indexed hash tables.

Finally, many languages are _imperative_, staying close to the hardware model of addressable memory, lightly abstracted into primitive values and references into mutable arrays and structures. On the other hand, _functional_ languages hide references and treat everything as immutable structured values. This conceptual simplification benefits certain kinds of programming, but can be counterproductive when an imperative approach is more natural, such as in external input/output.

## Example: conceptual openness

*Perl, contra Python*. In contrast to Python's outlook, Perl proclaims “There is more than one way to do it” and considers itself “the first postmodern programming language” \parencite{Perl}. “Perl doesn't have any agenda at all, other than to be maximally useful to the maximal number of people. To be the duct tape of the Internet, and of everything else.” The Perl way is to accept the status quo of evolved chaos and build upon it using duct tape and ingenuity. Taken to the extreme, a programming system becomes no longer a *system*, properly speaking, but rather a *toolkit for improvising* assemblages of *found* software. Perl can be seen as championing the values of *pluralism*, *compatibility*, or *conceptual openness* over conceptual integrity. This philosophy has been called _Postmodern Programming_ \parencite{PoMoProNotes}.

*C\++, contra Smalltalk*. Another case is that of C\++, which added to C the Object-Oriented concepts developed by Smalltalk while remaining 100% compatible with C, down to the level of ABI and performance. This strategy was enormously successful for adoption, but came with the tradeoff of enormous complexity compared to languages designed from scratch for OO, like Smalltalk, Ruby, and Java.

*Worse, contra Better*. Richard Gabriel first described this dilemma in his influential 1991 essay *Worse is Better* \parencite{WIB} analysing the defeat of Lisp by Unix and C. Because Unix and C were so easy to port to new hardware, they were “the ultimate computer viruses” despite providing only “about 50%--80% of what you want from an operating system and programming language”. Their conceptual openness meant that they adapted easily to the evolving conditions of the external world. The tradeoff was decreased conceptual integrity, such as the undefined behaviours of C, the junkyard of working directories, and the proliferation of special purpose programming languages to provide a complete development environment.

\tp{Smalltalk objects are "all levels of granularity"; Unix files are large-scale; Haskell data structures small-scale; very big virtual machines; there is also the web / distributed file system?}

*Unix and Files*. Many programming languages and systems impose structure at a "fine granularity": that of individual variables and other data and code structures. Conversely, systems like Unix and the Web impose fewer restrictions on how programmers represent things. Unix insists only on a basic infrastructure of "large objects" \parencite{Kell-OS}, delegating all fine-grained structure to client programs. This scores many points for conceptual openness. *Files* provide a universal API for reading and writing byte streams, a low-level construct containing so many degrees of freedom that it can support a wide variety of formats and ecosystems. *Processes* similarly provide a thin abstraction over machine-level memory and processors.

Concepual integrity is necessarily sacrificed for such openness; while "everything is a file" gestures at integrity, in the vein of Smalltalk's "everything is an object", exceptions proliferate. Directories are special kinds of files with special operations, hardware device files require special `ioctl` operations^[The `ioctl` interface is a way for userspace programs to communicate device-specific request codes to device drivers; for example, to eject a CD.], and many commands expect files containing newline separators. Additionally, because client programs must supply their *own* structure for fine-grained data and code, they are given little in the way of mutual compatibility. As a result, they tend to evolve into competing silos of duplicated infrastructure \parencite{Kell-OS,KellMMM}.

*The Web*. Web HTTP endpoints, meanwhile, have proven to be an even more adaptable and viral abstraction than Unix files. They operate at a similar level of abstraction as files, but support richer content and encompass internet-wide interactions between autonomous systems. In a sense, HTTP GET and PUT have become the "subroutine calls" of an internet-scale programming system. Perhaps the most salient thing about the Web is that its usefulness came as such a surprise to everyone involved in designing or competing with it. It is likely that, by staying close to the existing practice of transferring files, the Web gained a competitive edge over more ambitious and less familiar hypertext projects like Xanadu \parencite{TedNelson}.

The choice between compatibility and integrity correlates with the personality traits of *pragmatism* and *idealism*. It is pragmatic to accept the status quo of technology and make the best of it. Conversely, idealists are willing to fight convention and risk rejection in order to attain higher goals. We can wonder which came first: the design decision or the personality trait? Do Lisp and Haskell teach people to think more abstractly and coherently, or do they filter for those with a pre-existing condition? Likewise, we can provocatively speculate whether introverted developers prefer the cloisters of Smalltalk or Lisp to the adventurous "Wild West" of the Web.

## Dimension: composability
In short, *you can get anywhere by putting together a number of smaller steps.* There exist building blocks which span a range of useful combinations.
\note{JE. LEGOs might be a more friendly example than Linear Algebra.
Such a property can be analogised to *linear independence* in mathematical vector spaces: a number of primitives (basis vectors) whose possible combinations span a meaningful space.}
Composability is, in a sense, key to the notion of "programmability" and every programmable system will have some level of composability (e.g. in the scripting language.)

*Unix* shell commands are a standard example of composability. The base set of primitive commands can be augmented by programming command executables in other languages. Given some primitives, one can "pipe" one's output to another's input (`|`), sequence (`;` or `&&`), select via conditions, and repeat with loop constructs, enabling full imperative programming. Furthermore, command compositions can be packaged into a named "script" which follows the same interface as primitive commands, and named subprograms within a script can also be defined.

In *HyperCard*, the *Authoring Environment* is *non*-composable for programming buttons: there is simply a set of predefined behaviours to choose from. Full scriptability is available only in the *Programming Environment*.

The *Haskell type system*, as well as that of other functional programming languages, exhibits high composability. New types can be defined in terms of existing ones in several ways. These include records, discriminated unions, function types and recursive constructs (e.g. to define a `List` as either a `Nil` or a combination of element plus other list). The C programming language also has some means of composing types that are analogous in some ways, such as structs, unions, enums and indeed even function pointers. For every type, there is also a corresponding "pointer" type. It lacks, however, the recursive constructs permitted in Haskell types.

\note{*Web* Mashups in Web 2.0? Yahoo Pipes?}

## Dimension: convenience
In short, *you can get to X, Y or Z via one single step.* There are ready-made solutions to specific problems, not necessarily generalisable or composable. Convenience often manifests as "canonical" solutions and utilities in the form of an expansive standard library.

Composability without convenience is a set of atoms or gears; theoretically, anything one wants could be built out of them, but one must do that work. This situation has been criticised as the *Lisp Curse* \parencite{LispCurse}.

Composability *with* convenience is a set of convenient specific tools *along with* enough components to construct new ones. The specific tools themselves could be transparently composed of these building blocks, but this is not essential. They save users the time and effort it would take to "roll their own" solutions to common tasks.

For example, let us turn to a convenience factor of *Unix* shell commands, having already discussed their composability above. Observe that it would be possible, in principle, to pass all information to a program via standard input. Yet in actual practice, for convenience, there is a standard interface of *command-line arguments* instead, separate from anything the program takes through standard input. Most programming systems similarly exhibit both composability and convenience, providing templates, standard libraries, or otherwise pre-packaged solutions, which can nevertheless be used programmatically as part of larger operations.

\note{ex: something in the UI world? one click vs. long winded "principled" way of doing the thing? (macros? applescript?)}

## Dimension: commonality
\note{JE This seems like data modelling issues that happen during application design, not programming system design.}

Humans can see Arrays, Strings, Dicts and Sets all have a “size”, but the software needs to be *told* that they are the “same”. Commonality like this can be factored out into an explicit structure (a “Collection” class), analogous to database *normalisation*. This way, an entity's size can be queried without reference to its particular details: if `c` is declared to be a Collection, then one can straightforwardly access `c.size`.

Alternatively, it can be left implicit. This is less upfront work, but permits instances to *diverge*, analogous to *redundancy* in databases. For example, Arrays and Strings might end up with “length”, while Dict and Set call it “size”. This means that, to query the size of an entity, it is necessary to perform a case split according to its concrete type, solely to funnel the diverging paths back to the commonality they represent:

```
if (entity is Array or String)  size := entity.length
else if (entity is Dict or Set) size := entity.size
```

\joel{Web APIs e.g. onmousedown/onmouseup, imperative onmousemove instead of reified mouse pointer observable (this belongs more in a Factoring of Structure / Complexity dimension...)
   - tbh this is also a "machine legibility" issue; a human can recognise onmousemove and onmousedown as having something in common -- "mouse" -- but the computer just sees two non-equal strings as different as zQx6= and omlette.
   - onmousedown further makes the *mouse* part explicit, but the sub-device -- the button -- is passed as a numerical argument. ...
   - What really annoyed me, and seems most relevant, is that mouse buttons and keyboard keys the same in a very significant way -- they're binary-state buttons -- which means they ought to "implement the same interface", so the system will let me treat them the same insofar as they have commonalities like this. It should be trivial to rebind keyboard keys to the mouse buttons or vice versa, but this "poor factoring" obstructs this.
   - viz. OOP interfaces and abstraction, this factoring is forcing you to rely on irrelevant concrete details of the object. Instead of `if (isMouseButton) listenMouseButton(fn) else if (isKey) listenKey(fn)`, it should just be `listen(fn)`.}

\note{
EXAMPLES: interfaces/base classes; structural vs nominal typing
EXAMPLES: non-programming language world?
HyperCard - had shared backgrounds, which arose from the need of writing the help files.
}

## Examples: flattening and factoring of commonality
Data structures usually have several "moving parts" that can vary independently. For example, a simple pair of “vehicle type” and “colour” might have all combinations of (Car, Van, Train) and (Red, Blue). In this *factored* representation, we can programmatically change the colour directly: `pair.second = Red` or `vehicle.colour = Red`.

In some contexts, such as class names, a system might only permit such multi-dimensional structure as an *exhaustive enumeration*: RedCar, BlueCar, RedVan, BlueVan, RedTrain, BlueTrain, etc. The system sees a flat list of atoms, even though a human can see the sub-structure encoded in the string. In this world, we cannot simply “change the colour to Red” programmatically; we would need to case-split as follows:

```
if (type is BlueCar) type := RedCar
else if (type is BlueVan) type := RedVan
else if (type is BlueTrain) type := RedTrain
...
```

The *commonality* between RedCar, RedVan, BlueCar, and so on has been *flattened*. There is implicit structure here that remains *un-factored*, similar to how numbers can be expressed as singular expressions (16) or as factor products (2,2,2,2). *Factoring* this commonality gives us the original design, where there is a pair of values from different sets.

In *relational databases*, there is an opposition between *normalisation* and *redundancy*. In order to fit multi-table data into a *flat* table structure, data needs to be duplicated into redundant copies. When data is *factored* into small tables as much as possible, such that there is only one place each piece of data "lives", the database is in *normal form* or *normalised*. Redundancy is useful for read-only processes, because there is no need to join different tables together based on common keys. Writing, however, becomes risky; in order to modify one thing, it must be synchronised to the multiple places it is stored. This makes highly normalised databases optimised for writes over reads.

## Remark: the end of history?

Today we live in a highly developed world of software technology. It is estimated that 41,000 person years have been invested into Linux. We describe software development technologies in terms of *stacks* of specialised tools, each of which might capitalise over 100 person-years of development. Programming systems have become programming *eco*systems: not designed, but evolved. How can we noticeably improve programming in the face of the overwhelming edifice of existing technology? There are strong incentives to focus on localised incremental improvements that don’t cross the established boundaries.

The history of computing is one of cycles of evolution and revolution. Successive cycles were dominated in turn by mainframes, minicomputers, workstations, personal computers, and the Web. Each transition built a whole new technology ecosystem replacing or on top of the previous. The last revolution, the Web, was 25 years ago, with the result that many people have never experienced a disruptive platform transition. Has history stopped, or are we just stuck in a long cycle, with increasingly pent-up pressures for change? If it is the latter, then incompatible ideas now spurned may yet flourish.

## References
- How to Design a Good API and Why it Matters \parencite{APIdesign}

# Customizability

\mybox{Once a program exists in the system, how can it be extended and modified?}

Programming is a gradual process. We start either from nothing, or from an existing program, and gradually extend and refine it until it serves a given purpose. Programs created using different programming systems can be refined to different extents, in different ways, at different stages of their existence.

Consider three examples. First, a program in a conventional programming language like Java can be refined only by modifying its source code. However, you may be able to do so by just *adding* new code, such as a new interface implementation. Second, a spreadsheet can be modified at any time by modifying the formulas or data it contains. There is no separate programming phase. However, you have to modify the formulas directly in the cell---there is no way of modifying it by specifying a change in a way that is external to the cell. Third, a *self-sustaining* programming system, such as Smalltalk, does not make an explicit distinction between "programming" and "using" phases, and it can be modified and extended via itself. It gives developers the power to experiment with the system and, in principle, replace it with a better system from within.

## Dimension: staging of customisation
For systems that distinguish between different stages, such as writing source code versus running a program, customisation methods may be different for each stage. In traditional programming languages, customisation is done by modifying or adding source code at the programming stage, but there is no (automatically provided) way of customising the created programs once they are running.

There are a number of interesting questions related to staging of customisation. First, what is the notation used for customisation? This may be the notation in which a program was initially created, but a system may also use a secondary notation for customisation (consider Emacs using Emacs Lisp). For systems with a stage distinction, an important question is whether such changes are *persistent*.

*Smalltalk, Interlisp and similar.* In image-based programming systems, there is generally no strict distinction between stages and so a program can be customised during execution in the same way as during development. The program image includes the programming environment. Users of a program can open this, navigate to a suitable object or a class (which serve as the *addressable extension points*) and modify that. Lisp-based systems such as *Interlisp* follow a similar model. Changes made directly to the image are persistent. The PILOT system for Lisp \parencite{Pilot} offers an interactive way of correcting errors when a program fails during execution. Such corrections are then applied to the image and are thus persistent.

*Document Object Model (DOM) and Webstrates*: In the context of Web programming, there is traditionally a stage distinction between programming (writing the code and markup) and running (displaying a page). However, the DOM can also be modified by browser Developer Tools---either manually, by running scripts in a console, or by using a userscript manager such as Greasemonkey. Such changes are not persistent in the default browser state, but are made so by Webstrates \parencite{Webstrates} which synchronise the DOM between the server and the client. This makes the DOM collaborative, but not (automatically) _live_ because of the complexities this implies for event handling.


## Dimension: addressing and externalisability
Programs in all programming systems have a representation that may be exposed through notation such as source code. When customising a program, an interesting question is whether a customisation needs to be done by modifying the original representation, or whether it can be done by *adding* something alongside the original structure.

In order to support customisation through addition, a programming system needs a number of characteristics introduced by \textcite{Externalize,OAP}. First, the system needs to support *addressing*: the ability to refer to a part of the program representation from the outside. Next, *externalisability* means that a piece of addressed state can be exhaustively transferred between the system and the outside world. Finally, *additive authoring* requires that system behaviours can be *changed* by simply *adding* a new expression containing addresses---in other words, anything can be *overridden* without having to touch its specification. Of particular importance is how addresses are specified and what extension points in the program they can refer to. The system may offer an automatic mechanism that makes certain parts of a program addressable, or this task may be delegated to the programmer.

*Cascading Style Sheets (CSS)*: CSS is a prime example of additive authoring within the Web programming system. It provides rich addressability mechanisms that are partly automatic (when referring to tag names) and partly manual (when using element IDs and class names). Given a web page, it is possible to modify almost any aspect of its appearance by simply *adding* additional rules to a CSS file. The Infusion project \parencite{Infusion} offers similar customisability mechanisms, but for behaviour rather than just styling. There is also the recent programming system Varv\ \parencite{Varv}, which embodies additive authoring as a core principle.

*Object Oriented Programming (OOP) and Aspect Oriented Programming (AOP)*: in conventional programming languages, customisation is done by modifying the code itself. OOP and AOP make it possible to do so by adding code independently of existing program code. In OOP, this requires manual definition of extension points, i.e. interfaces and abstract methods. Functionality can then be added to a system by defining a new class (although injecting the new class into existing code without modification requires some form of configuration such as a dependency injection container). AOP systems such as AspectJ \parencite{AspectJ} provides a richer addressing mechanism. In particular, it makes it possible to add functionality to the invocation of a specific method (among other options) by using the *method call pointcut:* a specification for running code before, after, or around all calls to a given method, no matter how scattered around the code. This functionality is similar to *advising* in Pilot \parencite{Pilot}.

## Concept: self-sustainability
For most programming languages, programming systems, and ordinary software applications, if one wants to customise beyond a certain point, one must go beyond the facilities provided in the system itself. Most programming systems maintain a clear distinction between the *user level*, where the system is used, and *implementation level*, where the source code of the system itself resides. If the user level does not expose control over some property or feature, then one is forced to go to the implementation level. In the common case this will be a completely different language or system, with an associated learning cost. It is also likely to be lower-level---lacking expressive functions, features or abstractions of the user level---which makes for a more tedious programming experience.

It is possible, however, to carefully design systems to expose deeper aspects of their implementation *at the user level*, relaxing the formerly strict division between these levels. For example, in the research system *3-LISP*\ \parencite{ProcRefl}, ordinarily built-in functions like the conditional `if` and error handling `catch` are implemented in 3-LISP code at the user level.

The degree to which a system's inner workings are accessible to the user level, we call *self-sustainability*. At the maximal degree of this dimension would reside "stem cell"-like systems in a state of *zero commitment:* they could be progressively evolved to arbitrary behaviour without having to "step outside" of the system to a lower implementation level. In a sense, any difference between these systems would be merely a difference in initial state, since any could be turned into any other. Furthermore, such a system could be turned into any *existing* programming system by making commitments and thus *subtracting* flexibility.

The other end, of minimal self-sustainability, corresponds to minimal customisability: beyond the transient run-time state changes that make up the user level of any piece of software, the user cannot change anything without dropping down to the means of implementation of the system. This would resemble a traditional end-user "application" focused on a narrow domain with no means to do anything else.

The examples that we see of high self-sustainability all tend to be *Operating System-like*. Unix is widely established as an operating system, while Smalltalk and Lisp have been branded differently. Nevertheless, all three have shipped as the operating systems of custom hardware, and have similar responsibilities. Specifically: they support the execution of "programs"; they define an interface for accessing and modifying state; they provide standard libraries of common functionality; they define how programs can communicate with each other; they provide a user interface.

*Unix*: Self-sustainability of Unix is owed to the combination of two factors. First, the system is implemented in binary files (via ELF^[Executable and Linkable Format.]) and text files (for configuration). Second, these files are part of the user-facing filesystem, so users can replace and modify parts of the system using Unix file interfaces.

*Smalltalk and \acp{COLA}*: Self-sustainability in Smalltalk is similar to Unix, but at a finer granularity and with less emphasis on whether things reside in volatile (process) or non-volatile (file) storage. The analogous points are that (1) the system is implemented as objects with methods containing Smalltalk code, and (2) these are modifiable using the class browser and code editor. Combined Object Lambda Architectures, or COLAs\ \parencite{COLAs}, are a theoretical system design to improve on the self-sustainability of Smalltalk. This is achieved by generalising the object model to support relationships beyond classes.

We will illustrate the key dimensions of self-sustainability with the help of an existing programming system that is not self-sustainable. We cast the *Web browser* as the *product system* (\ie{} that which we hypothetically wish to make self-sustainable) and C++ as the *platform* (\ie{} the system used to implement it). What would it take to make the browser self-sustainable?

## Dimension: substrate size
The *substrate* is the portion of a system not accessible from its user level. In the case of the web browser, it is the C++ code constituting its implementation. To get a self-sustainable system, the substrate must be minimised by shifting implementation out of it and into the programming capabilities of the product system. In this case, most of the named entities in the C++ code are stuck at the implementation level, inaccessible at the user level of \ac{JS}, so we must move the former into the latter.

To sketch how this process could be carried out systematically, we can begin with the graphical surface of the product system. For each graphical element, we inquire into the causes of its display; this will include graphical rendering code, but also the data that is being rendered and the code that generated it. By tracing backwards in this way we discover the web of causes that produced the shape on the screen. This will often go through the user level (\ac{JS}), but if we keep tracing back, we will hit the implementation level. Each time this happens, we port the code from the implementation level to the user level.

We continue this until it is no longer feasible; for example, there will ultimately have to be some native machine-code interpreter for \ac{JS} in the running system. In practice, there would need to be the usual investments in JIT compilation and optimisation technology as seen in VMs for Smalltalk and other languages.

These ideas suggest a dimension of *substrate size* as a penalty for self-sustainability. In other words, a self-sustainable system *minimises* this dimension. A reasonable measure of substrate size could be the number of lines of code that implement it.

## Dimension: persistence effort
In order for a system to be self-sustainable, it has to be able to preserve developments of its state through process termination. The standard VM solution is to have most of the system state saved in an "image" file and concentrate the substrate in a runnable binary that need not be changed. However this is accomplished, *persistence* of run-time changes is necessary to encourage indefinite evolution of the system. In the case of the Web browser, this applies to the whole browser, but could also be a concern for individual tabs or web pages that can be closed or refreshed.

This suggests another penalty dimension of *persistence effort.* To illustrate the range of values, we offer the following examples:

* Any system which automatically persists to an "image" (Lisp, Smalltalk) or otherwise (Webstrates;\ \cite{Webstrates}) causes *minimal* persistence effort on the part of the user.^[Arguably, this effort is zero, since the user does not have to think about it.]
* A system with a manual "save" button that persists all state would have almost-minimal persistence effort. This comprises both the need to remember to save and the act of pressing the button.
* A system where one must repeat a manual procedure over different parts of the state to persist all of it would have *moderate* persistence effort.
* A typical programming language in a "vanilla" state (\eg{} excluding third-party libraries) has *high* persistence effort for its runtime data structures. With the use of a specific third-party library or framework (such as an Object-Relational Mapper) this persistence effort may be reduced. In the absence of such a framework, the programmer would have a lot of work to do in order to persist all state (wrap every variable and stack frame in code for loading and saving its value).
* We could ascribe *infinite*^[An infinite score can be interpreted as saying: it would take less effort to duplicate the source code of the system and add persistence at its implementation level, than it would to persist state using user-level functionality.] persistence effort where it is impossible to persist state. This is easier to imagine in the case of an end-user application with no scripting capability; if the developers failed to persist something (\eg{} the position or sizing of a window) then the user cannot do anything about it. In the case of a programming system, hard barriers to persistence include inaccessible state (\eg{} in \ac{JS}, one cannot refer to stack frames or read their state) or a lack of enumerability (\eg{} there is no way to traverse all objects in the system and thereby persist them).

It may be objected that this measure should be considered on a piecemeal basis *per piece of state* instead of on the system as a whole. For example, a system could have infinite persistence effort with respect to some state (\eg{} stack frames;\ \cite{Externalize}) but low persistence effort with respect to everything else (this being the effort invested to set up an Object-Relational Mapper for the rest of the state). Given such a fine-grained application of this measure and a method of weighting each contribution, one could derive a convenient aggregate measure of persistence effort for the whole system. However, this is too complicated for the scope of our work here, so we defer it to further development of the framework.

## Dimensions: code viewing and editing
The browser's \ac{JS} console makes it possible to make some changes expressible as \ac{JS} commands, but some caveats would need mitigating. For example, the source code can be viewed but not edited; we would need to make a small change so that the source code viewer could also be used to make persistent edits to code. These points suggest *boolean* dimensions of *code viewing* and *code editing.* An example of a system that has both is Smalltalk with its class browser.

## Dimension: data execution
Once we can type text inside the system, we will be able to write code. However, this code will be inert unless the system can interpret data structures as programs and actually execute them. This is the case whether these data structures were created manually or by a program. If this is not possible, re-programming the system will not be possible (beyond selecting from a predefined list of behaviours). The browser does already satisfy this criterion since \ac{JS} has an `eval()` function that can execute a string of \ac{JS} code. This suggests a boolean dimension of *data execution* (or Code As Data).

Any system with an `eval` function has this property, such as Lisp. In the low-level binary layer of programming, the fact that the *instruction pointer* can be pointed at bytes in memory and interpret them as instructions also qualifies. A negative example exists in a language like C, where there is no `eval` function. In such a case, one may employ the workaround of defining a mini-language (whether textual, or a binary bytecode) and an interpreter C function. It is important to be clear on which level the property would be thus established: what we called the *product system* (the program being implemented by the C code) would have data execution but the *platform* (the C language itself) would remain without it.

## References
In addition to the examples discussed above, the proceedings of self-sustaining systems workshops \parencite{SSS-08,SSS-10} provide numerous examples of systems and languages that are able to bootstrap, implement, modify, and maintain themselves; Gabriel's analysis of programming language revolutions \parencite{PLrev} uses _advising_ in PILOT, related Lisp mechanisms, and "mixins" in OOP to illustrate the difference between the "languages" and "systems" paradigms.

## Relations
- *Flattening and factoring* (Section \ref{examples-flattening-and-factoring}): related in that "customizability" is a form of creating new programs from existing ones; factoring repetitive aspects into a reusable standard component library facilitates the same thing.
- *Interaction* (Section \ref{interaction}): this determines whether there are separate stages for running and writing programs and may thus influence what kind of customisation is possible.

# Complexity

\mybox{How does the system structure complexity and what level of detail is required?}

There is a massive gap between the level of detail required by a computer, which executes a sequence of low-level instructions, and the human description of a program in higher-level terms. To bridge this gap, a programming system needs to deal with the complexity inherent in going from a high-level description to low-level instructions.

Ever since the 1940s, programmers have envisioned that "automatic programming" would allow higher-level programming. This did not necessarily mean full automation. In fact, the first "automatic programming" systems referred to higher-level programming languages with a compiler (or an interpreter) that expanded the high-level code into detailed instructions.

Most programming systems use _factoring of complexity_ and encapsulate some of the details that need to be specified into components that can be reused by the programmer. The details may be encapsulated in a library, or filled in by a compiler or interpreter. Such factoring may also be reflected in the conceptual structure of the system (Section \ref{examples-flattening-and-factoring}). However, a system may also fully _automate_ some aspects of programming. In those cases, a general-purpose algorithm solves a whole class of problems, which then do not need to be coded explicitly. Think of planning the execution of SQL queries, or of the inference engine supporting a logic programming language like Prolog.

## Remark: notations
Even when working at a high level, programming involves manipulating some program notation. In high-level functional or imperative programming languages, the programmer writes code that typically has clear operational meaning, even when some of the complexity is relegated to a library implementation or a runtime. When using declarative programming systems like SQL, Prolog or Datalog, the meaning of a program is still unambiguous, but it is not defined operationally---there is a (more or less deterministic) inference engine that solves the problem based on the provided description. Finally, systems based on *programming by example* step even further away from having clear operational meaning---the program may be simply a collection of sample inputs and outputs, from which a (possibly non-deterministic) engine infers the concrete steps of execution.

## Dimension: factoring of complexity
The basic mechanism for dealing with complexity is _factoring_ it. Given a program, the more domain-specific aspects of the logic are specified explicitly, whereas the more mundane and technical aspects of the logic are left to a reusable component. Often, this reusable component is just a library. Yet in the case of higher-level programming languages, the reusable component may include a part of a language runtime such as a memory allocator or a garbage collector. In the case of declarative languages or programming by example, the reusable component is a general purpose inference engine.

## Dimension: level of automation
Factoring of complexity shields the programmer from some details, but those details still need to be explicitly programmed. Depending on the customizability of the system, this programming may or may not be accessible, but it is always there. For example, a function used in a spreadsheet formula is implemented in the spreadsheet system.

A programming system with higher _level of automation_ requires more than simply factoring code into reusable components. It uses a mechanism where some details of the operational meaning of a program are never explicitly specified, but are inferred automatically by the system. This is the approach of _programming by example_ and _machine learning_, where behaviour is specified through examples. In some cases, deciding whether a feature is _automation_ or merely _factoring of complexity_ is less clear: garbage collection can be seen as either a simple case of automation, or a sophisticated case of factoring complexity.

There is also an interesting (and perhaps inevitable) trade-off. The higher the level of automation, the less explicit the operational meaning of a program. This has a wide range of implications. \textcite{NextGen} notes, for example, that this means the implementation can significantly change the performance of a program.

## Example: domain-specific languages

Domain-specific languages \parencite{DSLs} provide an example of factoring of complexity that does not involve automation. In this case, programming is done at two levels. At the lower level, an (often more experienced) programmer develops a domain-specific language, which lets a (typically less experienced) programmer easily solve problems in a particular domain: say, modelling of financial contracts, or specifying interactive user interfaces.

The domain-specific language provides primitives that can be composed, but each primitive and each form of composition has explicitly programmed and unambiguous operational meaning. The user of the domain-specific language can think in the higher-level concepts it provides, and this conceptual structure can be analysed using the dimensions in Section\ \ref{conceptual-structure}. As long as these concepts are clear, the user does not need to be concerned with the details of how exactly the resulting programs run.

## Example: programming by example

An interesting case of automation is _programming by example_ \parencite{YWIMC}. In this case, the user does not provide even a declarative specification of the program behaviour, but instead specifies sample inputs and outputs. A more or less sophisticated algorithm then attempts to infer the relationship between the inputs and the outputs. This may, for example, be done through program synthesis where an algorithm composes a transformation using a (small) number of pre-defined operations. Programming by example is often very accessible and has been used in spreadsheet applications \parencite{PBEExcel}.

\joel{ Sad to get rid of this, but we should find a better way to include Kell's fragmentation.
## Remark: fragmentation
An interesting issue is that reusable components that enable higher levels of automation are often specific to each system. This, arguably, limits what we can achieve as components that enable higher-levels of automation are increasingly complex to implement. For example, the resolution algorithm that is at the core of a Prolog system is typically tightly bound to the particular system and cannot be easily reused by another programming system.

As noted in \parencite{KellOS,Mythical}, incompatible reusable components that exist for multiple systems also limit compositionality. One possible exception from the rule is the Z3 theorem prover, which is used as an implementation mechanism by multiple programming systems including Dafny and F*, as well as by numerous program verification tools.
}

## Example: next-level automation
Throughout history, programmers have always hoped for the next level of "automatic programming". As observed by\ \textcite{Euphemism}, "automatic programming has always been a euphemism for programming in a higher-level language than was then available to the programmer".

We may speculate whether Deep Learning will enable the next step of automation. However, this would not be different in principle from existing developments. We can see any level of automation as using *artificial intelligence* methods. This is the case for declarative languages or constraint-based languages---where the inference engine implements a traditional AI method (GOFAI, i.e., Good Old Fashioned AI).

\joel{include a definition and discussion of "boilerplate" code!}
\note{Tomas: I removed references here, because I could not think of anything else to add here. I guess it makes sense to keep those optional..}

## Relations
- *Conceptual structure* (Section\ \ref{conceptual-structure}): In many cases, the factoring of complexity follows the conceptual structure of the programming system.
- *Flattening and factoring* (Section\ \ref{examples-flattening-and-factoring}): One typically automates the thing at the lowest level in one's factoring (by making the lowest level a thing that exists outside of the program---in a system or a library)

# Errors

\mybox{What does the system consider to be an \emph{error}? How are they prevented and handled?}

A computer system is not aware of human intentions. There will always be human mistakes that the system cannot recognise as errors. Despite this, there are many that it *can* recognise, and its design will determine *which* human mistakes can become detectable program errors. This revolves around several questions: What can cause an error? Which ones can be prevented from happening? How should the system react to errors?

Following the standard literature on errors \parencite{HumanError}, we distinguish four kinds of errors: slips, lapses, mistakes and failures. A *slip* is an error caused by transient human attention failure, such as a typo in the source code. A *lapse* is similar but caused by memory failure, such as an incorrectly remembered method name. A *mistake* is a logical error such as bad design of an algorithm. Finally, a *failure* is a system error caused by the system itself that the programmer has no control over, e.g. a hardware or a virtual machine failure.

## Dimensions: error detection

Errors can be identified in any of the *feedback loops* that the system implements. This can be done either by a human or the system itself, depending on the nature of the feedback loop.

Consider three examples. First, in live programming systems, the programmer immediately sees the result of their code changes. Error detection is done by a human and the system can assist this by visualising as many consequences of a code change as possible. Second, in a system with a static checking feedback loop (such as syntax checks or static type systems), potential errors are reported as the result of the analysis. Third, errors can be detected when the developed software is run, either when it is tested by the programmer (manually or through automated testing) or when it is run by a user.

Error detection in different feedback loops is suitable for detecting different kinds of errors. Many slips and lapses can be detected by the static checking feedback loop, although this is not always the case. For example, consider a "compact" *expression geography* where small changes in code may result in large changes of behaviour.^[For example, the one-character difference between the Unix command `rm -rf ./*`, which recursively deletes all items in the current directory, and `rm -rf /*`, which does the same thing from the *root of the filesystem*.] This makes it easier for slips and lapses to produce hard to detect errors; for example, a typo might coincidentally overlap with an existing name in scope, which would not trigger an expected "undeclared name" error. Mistakes are easier to detect through a live feedback loop, but they can also be partly detected by more advanced static checking.

## Example: static typing

In statically typed programming languages like Haskell and Java, types are used to capture some information about the intent of the programmer. The type checker ensures code matches the lightweight specification given using types. In such systems, types and implementation serve as two descriptions of programmer's intent that need to align; what varies is the extent to which types can capture intent and the way in which the two are constructed; that is, which of the two comes first.

## Examples: TDD, REPL and live coding

Whereas static typing aims to detect errors without executing code, approaches based on immediate feedback typically aim to execute (a portion of) the code and let the programmer see the error immediately. This can be done in a variety of ways.

In case of *test-driven development*, tests play the role of a specification (much like types) against which the implementation is checked. Such systems may provide more or less immediate feedback, depending on when tests are executed (automatically in the background, or manually). Systems equipped with a read-eval-print loop (REPL) let programmers run code on-the-fly and inspect results. For successful error detection, the results need to be easily observable: a printed output is more helpful than a hidden change of system state. Finally, in live coding systems, code is executed immediately and the programmer's ability to recognise errors depends on the extent to which the system state is observable. For example, in live-coded music,\ \parencite{SonicPi} you _hear_ that your code is not what you wanted, providing an easy-to-use immediate error detection mechanism.


## Remark: eliminating latent errors

A common aim of error detection is to prevent *latent errors*, i.e. errors that occurred at some *earlier* point during execution, but only manifest themselves through an unexpected behaviour later on. For example, we might dereference the wrong memory address and store a junk value to a database; we will only find out upon accessing the database. Latent errors can be prevented differently in different feedback loops. In a live feedback loop, this can be done by visualising effects that would normally remain hidden. When running software, latent errors can be prevented through a mechanism that detects errors as early as possible (e.g. initialising pointers to `null` and stopping if they are dereferenced.)

*Elm and time-travel debugging.* One notable mechanism for identifying latent errors is the concept of *time-travel debugging* popularised by the Elm programming language. In time-travel debugging, the programmer is able to step back through time and see what execution steps were taken prior to a certain point. This makes it possible to break execution when a latent error manifests, but then retrace the execution back to the actual source of the error.

## Dimension: error response

When an error is detected, there are a number of typical ways in which the system can respond. The following applies to systems that provide some kind of error detection during execution.

- It may attempt to automatically recover from the error as best as possible. This may be feasible for simpler errors (slips and lapses), but also for certain mistakes (a mistake in an algorithm's concurrency logic may often be resolved by restarting the code).
- It may proceed as if the error did not happen. This can eliminate expensive checks, but may lead to latent errors later.
- It may ask a human how to resolve the issue. This can be done interactively, by entering into a mode where the code can be corrected, or non-interactively by stopping the system.

Orthogonally to the above options, a system may also have a way to recover from latent errors by tracing back through the execution in order to find the root cause. It may also have a mechanism for undoing all actions that occurred in the meantime, e.g. through transactional processing.

*Interlisp and Do What I Mean (DWIM).* Interlisp's DWIM facility attempts to automatically correct slips and lapses, especially misspellings and unbalanced parentheses. When Interlisp encounters an error, such as a reference to an undefined symbol, it invokes DWIM. In this case, DWIM then searches for similarly named symbols frequently used by the current user. If it finds one, it invokes the symbol automatically, corrects the source code and notifies the user. In more complex cases where DWIM cannot correct the error automatically, it starts an interaction with the user and lets them correct it manually.

*\LaTeX{}.* When a run of \LaTeX{} encounters an error, it prompts the user with several options. One of them is to ignore the error and proceed, in the hopes that it is inconsequential. Another is to edit the input stream at the location of the error and resume. If all else fails, the run can be halted. These options echo the "Abort, Retry, Ignore" dialog from MS-DOS.

## Relations
- _Feedback loops_: Error detection always happens as part of an individual feedback loop. The feedback loops thus determine the structure in which error detection can happen.
\note{- _Information loss_: Certain mechanism for error detection can only work if sufficient amount of information is available. For example, traveling debugger facility requires at least a form of execution log (but could also be easily implemented in a system based on bi-directional evaluation).}
- _Automation:_ A semi-automatic error recovery system (such as DWIM) implements a form of automation. The concept of antifragile software \parencite{Antifragile} is a more sophisticated example of error recovery through automation.
- _Expression geography:_ In an expression geography where small changes in notation lead to valid but differently behaved programs, a slip or lapse is more likely to lead to an error that is difficult to detect through standard mechanisms.

## References
The most common error handling mechanism in conventional programming languages is exception handling. The modern form of exception handling has been described by\ \textcite{ExceptionHandling}, while\ \textcite{SweImpact} documents the history and influences of Software Engineering on exception handling. The concept of _antifragile software_ \parencite{Antifragile} goes further by suggesting that software could improve in response to errors. Work on Chaos Engineering \parencite{ChaosMonkey} is a step in this direction.

\textcite{HumanError} analyses errors in the context of human errors and develops a classification of errors that we adopt. In the context of computing, errors and _miscomputation_ have been analysed from a philosophical perspective \parencite{Miscomputation,MalfunctioningSW}. Notably, attitudes and approaches to errors also differ for different programming subcultures \parencite{LivingWithErrors}.

# Adoptability

\mybox{How does the system facilitate or obstruct adoption by both individuals and communities?}

We consider adoption by individuals as the dimension of *Learnability*, and adoption by communities as the dimension of *Sociability*.

## Dimension: learnability
Mainstream software development technologies require substantial effort to learn. Systems can be made easier to learn in several ways:

- Specialising to a specific application domain.
- Specialising to simple small-scale needs.
- Leveraging the background knowledge, skills, and terminologies of specific communities.
- Supporting learning with staged levels of complexity and assistive development tools \parencite{FullBrain}. Better *Feedback Loops* can help (Section\ \ref{interaction}).
- Collapsing heterogeneous technology stacks into simpler unified systems. This relates to the dimensions under *Conceptual Structure* (Section\ \ref{conceptual-structure}).

FORTRAN was a breakthrough in programming because it specialised to scientific computing and leveraged the background knowledge of scientists about mathematical formulas. COBOL instead specialised to business data processing and embraced the business community by eschewing mathematics in favor of plain English.

LOGO was the first language explicitly designed for teaching children. Later BASIC and Pascal were designed for teaching then-standard programming concepts at the University level. BASIC and Pascal had second careers on micropocessors in the 90's. These microprocessor programming systems were notable for being complete solutions integrating everything necessary, and so became home schools for a generation of programmers. More recently languages like Racket\ \parencite{Racket}, Pyret\ \parencite{Pyret}, and Grace\ \parencite{Grace} have supported learning by revealing progressive levels of complexity in stages. Scratch returned to Logo's vision of teaching children with a graphical programming environment emphasising playfulness rather than generality.

Some programming languages have consciously prioritised the programmer's experience of learning and using them. Ruby calls itself _a programmer's best friend_ by focusing on simplicity and elegance. Elm\ \parencite{Elm} targets the more specialised but still fairly broad domain of web applications while focusing on simplicity and programmer-friendliness. It forgoes capabilities that would lead to run-time crashes. It also tries hard to make error messages clear and actionable.

If we look beyond programming languages *per se*, we find programmable systems with better learnability. The best example is spreadsheets, which offer a specialised computing environment that is simpler and more intuitive. The visual metaphor of a grid leverages human perceptual skills. Moving all programming into declarative formulas and attributes greatly simplifies both creation and understanding. Research on Live Programming \parencite{Hancock2003,BretVictor} has sought to incorporate these benefits into general purpose programming, but with limited success to date.

HyperCard and Flash were both programming systems that found widespread adoption by non-experts. Like spreadsheets they had an organising visual metaphor (cards and timelines respectively). They both made it easy for beginners to get started. Hypercard had layers of complexity intended to facilitate gradual mastery.

Smalltalk and Lisp machines were complex but unified. After overcoming the initial learning curve, their environments provided a complete solution for building entire application systems of arbitrary complexity without having to learn other technologies. Boxer \parencite{Boxer-design} is notable for providing a general-purpose programming environment---albeit for small-scale applications---along with an organising visual metaphor like that of spreadsheets.

## Dimension: sociability
Over time, especially in the internet era, social issues have come to dominate programming. Much programming technology is now developed by open-source communities, and all programming technologies are now embedded in social media communities of their users. Therefore, technical decisions that impact socialibilty can be decisive \parencite{SocioPLT}. These include:

- Compatibility: easy integration into standard technology stacks, allowing incremental adoption, and also easy exit if needed. This dynamic was discussed in the classic essay _Worse is Better_ \parencite{WIB} about how Unix beat Lisp.
- Developing with an open source methodology reaps volunteer labor and fosters a user community of enthusiasts. The technical advantages of open source development were first popularised in the essay _The Cathedral and the Bazaar_ \parencite{Cathedral}, which observed that "given enough eyeballs, all bugs are shallow". Open source has become the standard for software development tools, even those developed within large corporations.
- Easy sharing of code via package repositories or open exchanges. Prior to the open-source era, commercial marketplaces were important, like VBX components for VisualBasic. Sharing is impeded when languages lack standard libraries, leading to competing dialects like Scheme \parencite{LispCurse}.
- Dedicated social media communities can be fostered by using them to provide technical support. Volunteer technical support, like volunteer code contributions, can multiply the impact of core developers. In some cases, social media like Stack Exchange has even come to replace documentation.

One could argue that sociabilty is not purely a *technical* dimension, as it includes aspects of product management. Rather, we believe that sociability is a pervasive cross-cutting concern that cannot be *separated* from the technical.

The tenor of the online community around a programming system can be its most public attribute. Even before social media, Flash developed a vibrant community of amateurs sharing code and tips. The Elm language invested much effort in creating a welcoming community from the outset \parencite{WhatIsSuccess}. Attempts to reform older communities have introduced Codes of Conduct, but not without controversy.

On the other hand, a cloistered community that turns its back on the wider world can give its members strong feelings of belonging and purpose. Examples are Smalltalk, Racket, Clojure, and Haskell. These cohesive communities believe in the philosophy of their leaders and strive to follow it in the code they write.

The economic sustainability of a programming system can be even more important than strictly social and technical issues. Adopting a technology is a costly investment in terms of time, money, and foregone opportunities. Everyone feels safer investing in a technology backed by large corporations that are not going away, or in technologies that have such widespread adoption that they are guaranteed to persist. A vibrant and mature open-source community backing a technology also makes it safer.

Unfortunately, sociability is often in conflict with learnability. Compatibility leads to ever increasing historical baggage for new learners to master. Large internet corporations have invested mainly in technologies relevant to their expert staff and high-end needs. Open-source communities have mainly flourished around technologies for expert programmers "scratching their own itch". While there has been a flow of venture funding into "no-code" and "low-code" programming systems, it is not clear how they can become economically and socially sustainable. By and large, the internet era has seen the ascendancy of expert programmers and the eclipsing of programming systems for "the rest of us".
