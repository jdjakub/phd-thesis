\hypertarget{appendix-all-dims}{%
\chapter{Technical Dimensions Catalogue}\label{appendix-all-dims}}

Here, we present our proposed technical dimensions in detail. While they do contain some novel ideas of our own, they also integrate a wide range of existing concepts under a common umbrella. Note that the material here represents an update of our publication\ \parencite{TechDims}, mainly in regards to our Three Properties. Their dimensions in Section\ \ref{the-three-properties-as-dimensions} represent an evolution of some of the concepts in the paper, specifically:

* Our definition of Explicit Structure (Section\ \ref{explicit-structure}) supersedes what was previously "Surface / internal notations".
* Notational Freedom (Section\ \ref{notational-freedom}) came from some aspects of the *Notation* dimensions (Section\ \ref{notation}), particularly *uniformity of notations*.
* Our description of Self-Sustainability (Section\ \ref{self-sustainability}) is a fully developed version of the old dimension of the same name.

Therefore, in order to make this reference catalogue self-contained, we have rewritten the relevant sections here with adaptations of the material in Chapters\ \ref{analysis} and\ \ref{tech-dims}.

The intention of this catalogue is to provide a *reference* to be looked up and *used* as needed, not something that should be read from start to finish. We recommend skimming through the catalogue for anything particularly interesting before proceeding to Section\ \ref{evaluating-the-dark-programming-system}. There, we will reference several dimensions in the context of a specific example, at which point it may be helpful to come back for more detail. For a quick overview, we include a concise reference sheet on the next page, though it may make more sense after reading the relevant sections.

We present the dimensions grouped under *clusters*. These may be regarded as "topics of interest" or "areas of inquiry" when studying a given system, grouping together related dimensions against which to measure it.

Each cluster is named and opens with an indented *summary*, followed by a longer *discussion*, and closes with a list of any *relations* to other clusters along with any *references* if applicable. Within the main discussion, individual *dimensions* are listed, although our Three Properties are first introduced as *concepts.* Sometimes, a particular value along a dimension (or a combination of values along several dimensions) can be recognised as a familiar pattern---perhaps with a name already established in the literature. These are marked as *examples*. Finally, interspersed discussion is introduced as a *remark*.

\includepdf[pages={2}]{../table.pdf}

\input{../all-tech-dims.tex}

# Evaluating the Dark Programming System
This section demonstrates using the framework to analyse the recent programming system *Dark*\ \parencite{DarkWeb}, explaining how it relates to past work and how it contributes to the state of the art.

Dark is a programming system for building "serverless backends", i.e. services that are used by web and mobile applications. It aims to make building such services easier by "removing accidental complexity"^[\url{https://roadmap.darklang.com/goals-of-dark-v2.html}] resulting from the large number of systems typically involved in their deployment and operation. This includes infrastructure for orchestration, scaling, logging, monitoring and versioning. Dark provides integrated tooling (Figure\ \ref{fig:dark}) for development and is described as _deployless_, meaning that deploying code to production is instantaneous.

\begin{figure}
  \centering
  \includegraphics[width=1\linewidth]{dark-annotated.png}
  \caption[A simple Web service in Dark.]{A simple web service in Dark consisting of two HTTP endpoints (1, 2), a database (3), and a worker (4).\label{fig:dark}}
  \note{FROM https://medium.com/@wilk/dark-lang-an-uncommon-step-towards-the-future-of-programming-921cf7f38baf}
\end{figure}

Dark illustrates the need for the broader perspective of programming systems. Of course, it contains a programming language, which is inspired by OCaml and F#. But Dark's distinguishing feature is that it eliminates the many secondary systems needed for deployment of modern cloud-based services. Those exist outside of a typical programming language, yet form a major part of the complexity of the overall development process.

With technical dimensions, we can go beyond the "sales pitch", look behind the scenes, and better understand the interesting technical aspects of Dark as a programming system. Tables\ \ref{tab:dark1} and\ \ref{tab:dark2} summarise the more detailed analysis that follows.

\newcommand{\wrap}[1]{\parbox[t]{5.6cm}{#1}}
\renewcommand{\arraystretch}{1.3}

\newcommand{\cluster}[1]{\emph{#1}}

\begin{table}
\centering
\caption[Dark dimensions summary.]{Summary of where Dark lies on some of the dimensions.}
\begin{tabular}{ >{\raggedleft\arraybackslash}p{3.6cm} p{5.6cm}}
\hline
Dimension \cluster{(CLUSTER)} & Summary \\ 
\hline%\hline
\cluster{INTERACTION}	\\
Modes of Interaction & 
\wrap{Single integrated mode comprises development, debugging and operation ("deployless")} \\
Feedback Loops & \wrap{Code editing is triggered either by user or by unsupported HTTP request and changes are deployed automatically, allowing for \emph{immediate feedback}}\\
%\hline
\cluster{ERRORS}	\\
Error Response & \wrap{When an unsupported HTTP request is received, programmer can write handler code using data from the request in the process}\\
%\hline
\cluster{CONCEPTUAL STRUCTURE}	\\
Conceptual Integrity vs. Openness & \wrap{Abstractions at the domain specific high-level and the functional low-level are both carefully designed for conceptual integrity.}\\
Composability & \wrap{
User applications are composed from high-level primitives; the low-level uses composable functional abstractions (records, pipelines).}\\
Convenience & \wrap{Powerful high-level domain-specific abstractions are provided (HTTP, database, workers); core functional libraries exist for the low-level.} \\
%\hline
\cluster{ADOPTABILITY} \\
Learnability & \wrap{High-level concepts will be immediately familiar to the target audience; low-level language has the usual learning curve of basic functional programming}\\
\end{tabular}
\label{tab:dark1}
\end{table}

\begin{table}
\centering
\caption[Dark dimensions summary.]{Summary of where Dark lies on some of the dimensions.}
\begin{tabular}{ >{\raggedleft\arraybackslash}p{3.6cm} p{5.6cm}}
\hline
Dimension \cluster{(CLUSTER)} & Summary \\ 
\hline%\hline
\cluster{NOTATION}	\\
Notational Structure & \wrap{Graphical notation for high-level concepts is complemented by structure editor for low-level code} \\
Uniformity & \wrap{Common notational structures are used for database and code, enabling the same editing construct for sequential structures (records, pipelines, tables)}\\
%\hline
\cluster{COMPLEXITY}	\\
Factoring of Complexity & \wrap{Cloud infrastructure (deployment, orchestration, etc.) is provided by the Dark platform that is invisible to the programmer, but also cannot be modified}\\
Level of Automation & \wrap{Current implementation provides basic infrastructure, but a higher degree of automation in the platform can be provided in the future, e.g. for scalability} \\
%\hline
\cluster{CUSTOMISABILITY}\\	
Staging of Customisation & \wrap{System can be modified while running and changes are persisted, but they have to be made in the Dark editor, which is distinct from the running service} \\
Self-Sustainability & \wrap{Clear separation between user and implementation levels; system is for making web services, not open-ended exploration of programming.}
\end{tabular}
\label{tab:dark2}
\end{table}

## Dimensional analysis of Dark

\paragraph{Modes of interaction and feedback loops.}
Conventional *modes of interaction* (\ref{dimension-modes-of-interaction}) include running, editing and debugging. For modern web services, running refers to operation in a cloud-based environment that typically comes with further kinds of feedback (logging and monitoring). The key design decision of Dark is to integrate all these different modes of interaction into a single one. This tight integration allows Dark to provide a more immediate *feedback loop* (\ref{dimension-feedback-loops}) where code changes become immediately available not just to the developer, but also to external users. The integrated mode of interaction is reminiscent of the image-based environment in Smalltalk; Dark advances the state of art by using this model in a multi-user, cloud-based context.

\paragraph{Feedback loops and error response.}
The integration of development and operation also makes it possible to use *errors* occurring during operation to drive development. Specifically, when a Dark service receives a request that is not supported, the user can build a handler\ \parencite{DarkErrors} to provide a response---taking advantage of the live data that was sent as part of the request. In terms of our dimensions, this is a kind of _error response_ (Section\ \ref{dimension-error-response}) that was pioneered by the PILOT system for Lisp\ \parencite{Pilot}. Dark does this not just to respond to errors, but also as the primary development mechanism, which we might call *Error-Driven Development.* This way, Dark users can construct programs with respect to sample input values.

\paragraph{Conceptual structure and learnability.} Dark programs are expressed using high-level concepts that are specific to the domain of server-side web programming: HTTP request handlers, databases, workers and scheduled jobs. These are designed to reduce accidental complexity and aim for high _conceptual integrity_ (Section\ \ref{dimension-conceptual-integrity-vs.-openness}). At the level of code, Dark uses a general-purpose functional language that emphasises certain concepts, especially records and pipelines. The high-level concepts contribute to _learnability_ (Section\ \ref{dimension-learnability}) of the system, because they are highly domain-specific and will already be familiar to its intended users.

\paragraph{Notational structure and uniformity.} Dark uses a combination of graphical editor and code. The two aspects of the notation follow the _complementing notations_ (Section\ \ref{dimension-notational-structure}) pattern. The windowed interface is used to work with the high-level concepts and code is used for working with low-level concepts. At the high level, code is structured in freely positionable boxes on a 2D surface. Unlike Boxer\ \parencite{Boxer}, these boxes do not nest and the space cannot be used for other content (say, for comments, architectural illustrations or other media). Code at the low level is manipulated using a syntax-aware structure editor (high *explicit structure,* Section\ \ref{concept-implicit-vs.-explicit-structure}) showing inferred types and computed live values for pure functions. It also provides special editing support for records and pipelines, allowing users to add fields and steps respectively.

\paragraph{Factoring of complexity and automation.} One of the advertised goals of Dark is to remove accidental complexity. This is achieved by collapsing the heterogeneous stack of technologies that are typically required for development, cloud deployment, orchestration and operation. Dark hides this via _factoring of complexity_ (Section\ \ref{dimension-factoring-of-complexity}). The advanced infrastructure is provided by the Dark platform and is hidden from the user. The infrastructure is programmed explicitly and there is no need for sophisticated automation (Section\ \ref{dimension-level-of-automation}). This factoring of functionality that was previously coded manually follows a similar pattern to the development of garbage collection in high-level programming languages.

\paragraph{Customisability.} The Dark platform makes a clear distinction between the platform itself and the user application, so _self-sustainability_ (Section\ \ref{concept-self-sustainability}) is not an objective. The strict division between the platform and user (related to its aforementioned _factoring of complexity_) means that changes to Dark require modifying the platform source code itself, which is available under a license that solely allows using it for the purpose of contributing. Similarly, applications themselves are developed by modifying and adding code, requiring destructive access to it---so _additive authoring_ (Section \ref{dimension-addressing-and-externalisability}) is not exhibited at either level. Thanks to the integration of execution and development, persistent changes may be made during execution (c.f. _staging of customisation_, Section\ \ref{dimension-staging-of-customisation}) but this is done through the Dark editor, which is separate from the running service.

## Technical Innovations of Dark

This analysis reveals a number of interesting aspects of the Dark programming system. The first is the tight integration of different _modes of interaction_ which collapses a heterogeneous stack of technologies, makes Dark _learnable_, and allows quick feedback from deployed services. The second is the use of _error response_ to guide the development of HTTP handlers. Thanks to the technical dimensions framework, each of these can be more precisely described. It is also possible to see how they may be supported in other programming systems. The framework also points to possible alternatives (and perhaps improvements) such as building a more self-sustainable system that has similar characteristics to Dark, but allows greater flexibility in modifying the platform from within itself.
