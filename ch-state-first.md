\hypertarget{year1}{%
\chapter{\OROM{}/SVG: Self-Sustainability via Custom Notation}\label{year1}}

In Section\ \ref{the-three-properties} we mentioned our desire to have Explicit Structure as a base, in which case our priorities can either be (a) using Notational Freedom to pursue Self-Sustainability, or (b) using Self-Sustainability to pursue Notational Freedom. In this chapter^[Adapted from our 2020 Convivial Computing Salon paper entitled "What Does It Take To Create With Domain-Appropriate Tools?" \parencite{CCS20}.] we report on the first approach. We follow the process of implementing part of the Self-Sustainable COLA architecture, namely the Id object model covering the "state" or "structural" half \parencite{OROM}. We do this using a notation better suited to it than that provided by our default programming tools. This comes from applying the following heuristic:

\begin{heuristic}[Ideal Expression]
\label{ideal-expr}
Always be asking the question: ``how would I ideally prefer to express the (sub-)program I am creating or problem I am solving?''
\end{heuristic}

An important aspect is that Heuristic\ \ref{ideal-expr} is about what is best for a *local context* rather than in general. Also important is that it is *subjective* based on the preferences of the programmer, as we mentioned in Section\ \ref{full-notational-freedom}. This lets us sidestep unproductive debates about which notations are simply "better" or "worse", even for local contexts. The worthy goal is not that anyone should be forced to use a wonderful notation that we have come up with; we have done no such thing. Instead it is simply that, *if* a programmer would *prefer* to express their problem in a certain notation, they should not be *prevented* from doing so (Section \ \ref{what-do-we-mean-by-support}).

Section\ \ref{the-object-model} gives an overview of Id and explains how we applied Heuristic\ \ref{ideal-expr} to it. We implement our notation on top of our chosen platform of the Web browser. This can be seen as "assuming" Notational Freedom or acting *as if* we had it in our platform, so as to discover the accidental complexity involved in brute-forcing a custom notation there. Section\ \ref{typical-requirements-of-common-software} reports these accidental complexities, and Section\ \ref{patterns-and-polyfilling} proposes the necessary infrastructure for dealing with them. In Section\ \ref{the-id-system-as-a-part-of-the-solution}, we return to the Id system and its role in supporting flexible software. It concludes with an evaluation of \OROM/SVG in terms of the dimensions we defined in Section\ \ref{the-three-properties-as-dimensions}. We conclude by summarising this "notation-first" approach and what lessons we can take from it.

The contributions of this chapter are as follows:

* A concrete application of Heuristic\ \ref{ideal-expr} which is enabled by Notational Freedom.
* A list of essential infrastructure that one should expect to need when implementing a custom notation to address the accidental complexities that will emerge.
* A prototype of the Id object model as a dynamic version of the diagrams in its source paper\ \parencite{OROM}.

# The \OROM{} Object Model

The "structural" half of a COLA is described in \parencite{OROM}. It describes a late-bound, Smalltalk-style objects and messaging environment that the authors call "Id". We will summarise the important points here.

An \OROM{} *object* is a block of state which can change as a result of messages received by it. Messaging (analogous to *method invocation* in mainstream OOP) works as follows:

* A message is sent by first *bind*-ing its name to its *method implementation*, which is specific code that gets run in the context of the receiver $R$. This binding is a dynamic operation that can use runtime conditions to make its decision---even create a method implementation on the fly. This *late binding* contrasts with *early binding* where the name is bound to the implementation statically and thus holds for the entire running lifetime of the system.
* This "bind" step is accomplished by sending a further message; this time, to the receiver's *vtable* $V(R)$. A vtable is another object that maps "message name" to "implementation code"---it's analogous to a "class" in mainstream OOP.
* Because this initial "bind" message is itself a message send, it triggers a similar "bind" to *its* vtable $V(V(R))$, and so on: recursing up the vtable-chain, and terminating at a base case.
* The higher levels of the vtable-chain mean that different kinds of vtables can be supported,^[As well as different kinds of "kinds of vtables", and so on.] each implementing the "bind" operation in its own way. For example, one kind of vtable could employ a simple dictionary mapping names to implementations. A different kind might build the code for the implementation just-in-time, without looking anything up in a data structure.

## Ideal Expression for Id

joel{
\vspace{-0.1em}
\joel{ ^ Without this command, LaTeX absent-mindedly inserts a giant vertical gap at this point.
       This gives it a prod, focusing its attention and making it realise its mistake.
       (As usual, LaTeX defies comprehension in non-anthromorphic terms.) }}
       
*Note: this section is written in the first person as this best fits with the subjectivity of Heuristic\ \ref{ideal-expr}. It provides the motivation for the notation we use to implement \OROM{}.*

In-keeping with its aims of minimality and self-sustainability, Id as a whole is "bootstrapped" into existence by its initialisation code. Each step of this code makes use of any parts of the system set up by the previous steps. The paper itself consists of mostly prose, several code listings, and full C sources for a sample implementation at the end. It also provides several diagrams. But to understand it, I repeatedly found myself drawing *extra* diagrams.

For example, the first acts of the running system boil down to initialising the three or so objects. This consists of allocating memory, interpreting it as a C struct and then filling in fields in a mundane manner. I had great difficulty following the specifics in my head, but when I drew tables in the style of their diagrams I readily saw what what going on. I personally would have preferred these to have been in the paper in the first place, but I am not necessarily representative of those who read it.

\joel{Other areas like messaging semantics could still be quite confusing. After all, should we expect to be able to predict the entire future evolution of a dynamical system from a static description of its initial state, such as source code? Is this not why debuggers exist?}

At any rate, in order to solidify my understanding, there was no better way than to run the thing and explore it. And yet, having already "de-compiled" English text and C source code into object diagrams, it is a shame to have to compile it all back to struct member assignments. Worse, the reference system does not even have text I/O when it is run, let alone some sort of GUI.^[That is, the (un)intended user interface for Id ends up being a C debugger!] Faced with the necessity of adding *some* UI, it seemed a waste of effort to end up with a system that must be continually polled for its current state at a terminal prompt.

If I naturally think of this system as 2D tables, why can't that be how the running system looks? I do not have to keep polling my eyes for what state my diagrams are in. But further than that---why can't the system be *built* out of tables in the first place? Shouldn't this be the main takeaway from the amount of time we spend prototyping, explaining and designing software as diagrams on paper? Why must the "natural representation" be restricted to the finished product?

Thus was my natural representation decided. My first attempt to make it a reality was a partial success: a webpage made of HTML tables, evolved via JavaScript (JS).

In summary, the logic went as follows:

a) I wish to create a piece of software (in this case, the Id system)
b) I have in mind a natural way to represent it as it's being built.

## Id as HTML Tables

\begin{figure}
  \centering
  \includegraphics[width=\linewidth]{orom-html.png}
  \caption{Part of \OROM{}/HTML: Obj-dicts are rendered as resizable tables, which reference
           each other through numerical IDs. \label{fig:orom-html}}
\end{figure}

To emphasise the tendency of \OROM{} objects to be visualised as key-value mappings, we refer to them as *obj-dicts*. Figure\ \ref{fig:orom-html} shows the \OROM{}/HTML^[This is read "Id over HTML", to emphasise the specific substrate on which it sits.] implementation \parencite{Orom-html}, in which they take the form of HTML tables.

Here, we were grateful for the browser's management of graphical layout, resizable text fields, and keeping the DOM tree synchronised with what one sees. This last property enabled us to make the decision to *directly* encode much of the system state in the DOM, achieving basic liveness ("The thing on the screen is the actual thing") for the keys and values of obj-dicts.

We used a two-column `<table>` within a `<div>` for each obj-dict, matching the diagrams. We gave the rows CSS class names, matching the keys, for easy lookup. Sending messages relied on the JS console, but existing values in text boxes (such as method implementations) could be edited directly.

This choice of ordinary HTML as a substrate, however, proved rather two-edged. The browser requires many features to do its job of rendering complex web pages. And sadly, as its client, we could only make use of those capabilities which the W3C had decided, at the time of authorship, were worth the effort exposing in JavaScript. For anything else, the browser is a black box, and this was very frustrating in the following case.

### Trouble Achieving Arrows that Stay on the Shapes

A key aspect of the \OROM{} system is that there is an object *graph*. That is, obj-dicts can have entries pointing to other obj-dicts, without restriction to a tree structure. Drawing arrows to denote this is the obvious notation, and easy on paper, so we wanted it in our substrate for \OROM{}.

Scalable Vector Graphics (SVG) was at our disposal, which could be persuaded to display \svgel{line}s over the \svgel{div}s. But another key feature of our intended substrate was to be able to rearrange and resize the boxes. So we would also need to detect changes to the position and size of an element. Surprisingly, there is no such facility provided for HTML elements. This is in spite of the fact the browser *needs* this functionality, hence it must reside *somewhere* inside the black box.

Reluctantly, we stuck with our plan B: each object has a numerical ID and pointers are just fields containing a number, followed using a `deref()` function.

At this point it was starting to look like a mistake not to have done the whole thing in SVG. We would gain full graphical freedom, though also lose some benefits of the browser's managing it on our behalf. We already knew from experience the surprising complexity of a DIY approach to layout, model-view updates, and interactivity. But such an exercise would be an opportunity to carefully observe this tedium, and crystallise some intuitions about why it is so consistently frustrating.

\joel{
 OROM as files and directories

There's a way to take arbitrary domain and give it self-programmability. boundary between

> I would probably put this after the SVG part or perhaps even later once you discuss more
> general principles (like the idea of polyfilling to make a domain substrate into a programmable
> system). Then you can give this as another example.
}

## Id as SVG Trees

\begin{figure}
  \centering
  \includegraphics[width=\linewidth]{orom-svg.png}
  \caption{Part of \OROM{}/SVG: Obj-dicts are moveable and resizable nested boxes,
           referencing each other via \emph{real arrows}.\label{fig:orom-svg}}
\end{figure}

In \OROM{}/SVG, shown in Figure\ \ref{fig:orom-svg}, obj-dicts are encoded as nested SVG \svgel{rect}s and other elements, reminiscent of Boxer \parencite{Boxer}. This was a significant departure from the table representation, and even though SVG supports (some) nested HTML via `<foreignObject>`, we actually preferred the possibility of multiple levels of nesting.

Still, this version was far more challenging and took much longer to reach a satisfactory state. Yet it is precisely this drudgery that brings us to a better understanding of the work that it takes to simulate Notational Freedom. We shall discuss this in the form of broad patterns or themes that stand out to me from our development experience, hammered home by these \OROM{} implementations.

\joel{
> Why is OROM/SVG more challenging? I think it would be useful to sketch here, because it
> reveals some useful principles. There is more polyfilling (arrows and resizing is not in the
> SVG substrate itself), but at least it's possible to add those (unlike in HTML)
}

# Typical Requirements of Common Software
The "common case" of software present in our daily lives shares certain properties, such as being graphical and interactive. In this section, we will present these and other seemingly *inevitable* demands of normal software as they appear in \OROM{}/SVG. We will comment on how our programming platforms measure up to the task, including my chosen platform of JavaScript and Web technologies.

\joel{
Clear for \eg{} PowerPoint, but eg microservices: diagrams basic classes, dirs
These are what consumers expect, but we want to being prog **closer to user domain**, not each time special case, but part of substrate
dom-spec substrates
OROM/SVG supposed to exemplify this consistency
use devel homogeneity
remember there's this idea of consistency. here's what ppl actually do with computers.
look at what ppl use, fit OROM to it
we want programming to be like using, here is what using looks like, hence supporting these will also be necessary for domain-appropriate development.
}

## Retained-Mode Vector Graphics
Most software is designed for the subset of people who have a colour display they can perceive. So right away it is going to require ways to draw coloured shapes. There are usually libraries for this, but some only provide *immediate mode*: commands to instantaneously rasterise pixels to a buffer. This is not enough for modern software, as we often expect animation, or at least to see things change as we interact. Most often we wish to see *small changes* to the *same* shapes, rather than completely different shapes altogether; the reification that this requires to persist between frames is known as *retained mode.*

On this requirement, SVG fits the bill very well. Although it is not part of the JavaScript language *per se*, it is a standard and widely supported technology of the Web *platform*. We can observe that anyone with a browser *in principle* has access to a powerful vector graphics editor---just one with no GUI.

The SVG tree has the nice properties of the DOM, such as updating the display when shape parameters are changed. This is well-adapted to "I/O-bound" software like ours, where things change only in response to user input. If we wanted animation, this would boil down to a regular "advance simulation" signal, and would require setting up some rendering loop. Alternatively, there is the W3C's chosen ontology of CSS animations, but see Section\ \ref{context-appropriate-ontologies}.

## Basic Assumptions about Physical Objects
In any software making use of vector graphics, there is usually some level of "physics" expected by users. This need not be nearly as exhaustive as the word "physics" might imply, as in \eg{} physics engines for games; we feel it is important to recognise it for what it is instead of conceiving physics as an inherently complex thing to be found only in specialised simulations. For example, pretty much all mobile apps have what could be called "phone touch physics" where menus and screens slide in and out.

All humans learn a basic set of expectations about the things they see around them. Some of these, such as "things fall down", are not generally appropriate to software UIs---perhaps because the screen has a role in our lives a more of a table work surface rather than a vertical wall, even if it is vertical in real life. The level of physics in software tends to not involve force, or mass, or very much at all, merely position and space; we could call it "geometric physics".

One thing that all usable software must do, for example, is avoid crushing many visually complex shapes, such as lines of text, into the same (unreadable) region. Such concepts of "solid objects do not intersect" or "only things at different layers may overlap" are basic rules inherited from the real world of graphical presentation.

We feel the need to point this out because by default the computer does not know even the most obvious things about how space works, so we must laboriously program this intuitive concept. This is not only true in the case of 2-dimensional visual domains, but even in the 1-dimensional case of memory allocation. The physics of 1-dimensional memory are something like this:

* This number range `0000`--`FFFF` is a discrete space, where addresses are the points.
* Every point has at most one owner block (there is no overlap).
* These blocks are contiguous, finite ranges (1-dimensional boxes).

\joel{Hence the boilerplate involved to realise this in any domain with something resembling space.} Far from being a niche topic in games and graphics, spatial partitioning algorithms and data structures have surprising relevance to more ordinary software. Both memory allocation and graphical layout are essential to today's; shame that only one of those has been recognised as such---and made part of the standard libraries of programming languages.

### Translationally Rigid Bodies
When we have both a screen and a pointing device (\eg{} touch or mouse), immediately it becomes worth having ways to move things around in at least a minimally realistic way. We can debate the appropriateness of Direct Manipulation for various situations. But it does make a lot of sense in simple cases, such moving around subdivisions of space (\eg{} windows) or elements of a graphical design.

In \OROM{}, the obvious candidate for this is the obj-dicts, plus all nested boxes in \OROM{}/SVG. If we move the top-level rect, then we expect its children to move with it. This is simply the translational physics of *rigid bodies*: "this set of points all move together". Of course, proper rigid bodies might also rotate and have mass, but this is usually undesirable for UI elements.

Translational rigidity can be expressed as the points X and Y always having the same displacement from each other. Or, when one point is moved, the rest also move by the same delta. This is a problem of *preserving the relationship over time*, which was a significant area of \OROM{}/SVG.

## Maintaining Relationships over Time
The model of state-mutation present in most imperative languages is what we call "dumb" state. The language provides an affordance to change any part of the state to a new value, but nothing else.

As opposed to what? In every software system there are certain rules, or "invariants" of *internal consistency*, such as "translational rigidity" above. Often, changes to any part of the system are permissible, but only if connected or dependent parts of the state change in response.

The job of keeping track of who depends on whom can fall either on the programmer or the computer. If the programmer has to do this, they can only go so far managing and simulating in their head. As systems grow more complex, it is only natural to try and make the computer more intelligent to do this work. What this means is that whenever we are tempted to consider constraints, "reactive" programming, or the "Observer" pattern as things we only wheel out *on special occasions*, we only deceive ourselves into doing the *same work* less explicitly. It seems that such "live state" should be the expected common case for software development. A full treatment of this theme appears in \parencite{PolyConstraints}.

If a platform does not provide a means to causally link and unlink bits of live state, then this must form part of the standard boilerplate. Such was the case in \OROM{}/SVG: the polyfilled "Observable" class is the most widely used. It wraps a current value and a list of subscribers, notifying them when it changes.

Getting an object to follow the mouse pointer (\eg{} when dragging) is *conceptually* very simple: an "always equal" relation. In \OROM{}/SVG founded on live-state, this can be expressed in much the same way:

\begin{lstlisting}[language=JavaScript, numbers=none]
subscribe(object.position, pointer.position);
\end{lstlisting}

By default, an Observable A responds to a change from Observable B by adopting B's new value. So by subscribing the object's position to the pointer's, pointer movements copy the new position to the object.

## Nut-Cracking with Sledgehammers
Speaking about vector graphics, physics, layout and constraint maintenance might give the impression of high *conceptual* complexity at the heart of even simple software. This is not quite true, which makes it all the worse that there is yet still immense *implementation* complexity.

We are conditioned to only think of these in their most general forms. But the "vector graphics" we use in \OROM{}/SVG are just rects, lines, circles and text; a fraction of the full capability of SVG. The "geometric physics" we use is dwarfed by fully general 2D or 3D physics engines. The only layout algorithm we had the patience to implement was a simple way to expand a list of boxes to fit in a new child at the bottom. The affordance to place and size boxes *manually* is a convenient substitute, when required infrequently. Yet search for material on layout algorithms, and it can seem like fully general linear inequality solvers like Cassowary \parencite{Cassowary} are all there is.

\joel{I was able to sidestep some layout work by offloading it to the user. While building the system I tended to want to draw boxes into existence, and for that my brain's existing aesthetic algorithms can decide where they go. There are areas where the machine must be able to figure this out itself---I don't want to be constantly bothered by prompts to place boxes as they are allocated by some running code---but for one-offs it is a very convenient way to do a little less yak-shearing.}

The inevitable requirements we suggest here do not necessitate fully general engines for anything. In fact, such generality might make it *more* cumbersome to express what we wanted in \OROM{}. The relevant wisdom from computer science is that there is no point expressing a simple regex search as an arbitrary Turing machine. Similarly, our boxes do not have a moment of inertia or a mass and we don't need a linear optimisation solver for our space management. Under the theme of domain-appropriate tools, we think it is worth designing interfaces for *smaller-scale* instances of these areas and exploring what they are still capable of expressing.

# Patterns and Polyfilling
We have seen that an existing platform like the web browser is at the wrong level of abstraction for many requirements of common software. We recognise that they are reasonably well-adapted to batch mode file I/O tasks, but that they fail for the *common case* is a problem. There is largely the same setup per project just to get basic functionality:

* Here is how we shall describe shapes.
* Here is how we make these shapes move together.
* Here is how we maintain internal consistency as the user unpredictably changes things.

This burden either falls on the author, or on the wider community to build and maintain higher-level frameworks, syntax extensions, etc. In this paper, we refer to this process as "polyfilling", with an emphasis on the do-it-yourself, individual level.

It is true, we already have a term for bringing into existence some software feature that isn't already there: programming. The difference is that polyfilling is about filling in *boilerplate*, \ie{} functionality *that should have been there in the first place, but isn't*. This injects some subjectivity and value judgement into the term. But ultimately, all platforms are designed with certain features "out of the box" and leave other features for us to implement "if needed".

When the *normal* problem domain "requires batteries", yet the tools available say "batteries not included", it is quite reasonable to want the batteries to be part of the platform. Section\ \ref{what-did-it-take-and-why} will expand on this further, but here we will list the "batteries not included" we found necessary to polyfill in \OROM{}/SVG.

\joel{
making something programmable / raising substrate
}

\joel{
Stupid lack of XYZ in JS or SVG due to come out in the next spec, etc
Syntax sugar (SVG tree ops, vector ops)
objects in JS user data
}

## Wrapping and "User Data"
The classic example of polyfilling is finding that JavaScript arrays do not support some familiar operation. For example, this used to be the case with the `map` function. Yet JavaScript lets us *directly augment* the `Array` class with the new operation, even though `Array` is "part of the language":

\begin{lstlisting}
Array.prototype.map = function() { ... }
\end{lstlisting}

One can do this to all objects, even those of Web APIs. For example, we use it to connect SVG nodes to the \OROM{} system: when a \svgel{rect} is created, it is "annotated" with a `userData` pointer to the box that it is part of. Then, when clicked, the box can be found immediately.

This could be described as the box "wrapping" the \svgel{rect}, but risks implying that the \svgel{rect} merely needs to *live inside*, or *be referenced by*, the box. In this case, the *reverse* association is also needed (\ie{} mutual pointers). Clicks can only enter the system *through* SVG in the first place. What is significant here is that JavaScript lets us add such a link to an entity that *we did not design*.

This term, "user data", often crops up in libraries with the same idea: to associate arbitrary data with an API object. But in languages that do not permit such unanticipated "annotation", this has to be explicitly *designed in* ahead of time. If it was not, then it has to be done in a roundabout way---for example, a separate `userData` lookup table, indexed by the memory address of the API object.

## Positioning and Sizing
The simple desire to move and resize boxes with the mouse motivated a lot of the concepts in Sections \ref{basic-assumptions-about-physical-objects} and \ref{maintaining-relationships-over-time}. This problem could be considered a smaller instance of Heuristic\ \ref{ideal-expr}: what is a natural way we *conceive* of this behaviour, and could we implement it that way?

\joel{
> The above point (moving motivated live state and physics ideas) would be a lot more obvious
> if you followed the structure with motivating example first followed by an analysis...
}

Our ideal notation here starts with a consideration of translational rigidity. In its most primitive form, this is a relation between two points. Thus it is natural to draw a line or "rod" between them. It seems that this rod transmits changes in one of its endpoints directly to the other endpoint. Since they both feel the same deltas, the displacement vector between them is preserved (Figure\ \ref{fig:delta-transmission}).

\begin{figure*}
  \centering
  \includegraphics[width=\textwidth]{delta-transmission.png}
  \caption{Fully rigid rods transmit changes in one end to the other end. \label{fig:delta-transmission}}
\end{figure*}

We prefer to see what we're doing, so we wanted these rods to be visible and thus somehow present in the SVG. For this, we have a Point class that is used wherever manipulable points (SVG circles) are needed, and a Rod class that draws a \svgel{line} and transmits endpoint deltas across its length.

Resizing of boxes could be achieved through rods that stay horizontal or vertical. In the language of "small differences" spoken by the live-state infrastructure, this is expressed as a rod "transmitting deltas" in the vertical and horizontal, "absorbing" the other component into itself (Figure\ \ref{fig:rods}). Mirroring a DOM rect to these rods is as simple as subscribing its width and height to horizontal and vertical rods' `length` Observables. This way, boxes can be resized from whatever corner is convenient.

\begin{figure*}
  \centering
  \includegraphics[width=0.85\textwidth]{rods.png}
  \caption{The yellow rods on the box border are ``half-rigid'': they transmit horizontal or vertical changes, but not both. The green rod in the top-left absorbs all changes from an endpoint into itself, visualising the displacement vector of the child box from its parent. The remaining red rods are fully rigid. A line of CSS can reveal this ``scaffolding'' which is normally hidden for users. \label{fig:rods}}
\end{figure*}

Unfortunately, with these rods came possibly the most frustrating technical challenges of the entire system. Initially we hoped to move boxes as rigid bodies by temporarily making their border rods rigid. However, the four border rods form a cyclic graph, as rods are not directed. This, coupled with the unintended depth-first semantics of Observable notification (a result of JavaScript function calls in a loop), led to duplicate deltas applied twice and other nightmares. This is the tip of an entire research iceberg stretching from Functional Reactive Programming to internet routing and distributed algorithms.

We had to shelve this investigation in the interest of continuing with the rest of the system, but we did manage to surmount this through kludging and compromise. So we do not know whether these problems are merely a consequence of some design decision we could change to escape it, or if they are intrinsic to our modest UI goal.

\joel{
> This section reads more as an implementation detail - even though it makes some interesting
> points about the physics & rods system. However, it might better to have it in a section
> that's more about implementation than about general principles. Or perhaps you could restructure
> Section 3 to have (i) example (ii) general lessons (like physics etc.) and (iii) specific
> points about implementation (\eg{} your concrete physics system with rods, what you had to
> 'polyfill')
}

## Visible Coordinate Systems
Rigidity in a flat world of sibling shapes is somewhat straightforward. However, rigidity in the SVG world is more involved.

First of all, SVG shapes (\eg{} \svgel{rect}) are strictly *leaf* nodes of the DOM. So if we wish to nest boxes within boxes, the visible box \svgel{rect} must be a mere *accessory* to the nestable element, in my case a \svgel{g} (group). This means that instead of resizing the `x`, `y`, `width`, `height` attributes of the \svgel{rect}, only its `width` and `height` change, along with the `transform` attribute of its *parent* \svgel{g}. This was not too bad; just subscribe this attribute, instead of the \svgel{rect} position, to the top-left Point handle.

All child elements of a node transform with it, so already SVG has baked in a basic facility for translational rigidity. This is only available as a tree hierarchy,^[This highlights the mismatch between the tree-based DOM and any system that is graph-structured.] but it is still useful. However, it conflicts with our early decision to have Point objects all share the global co-ordinate system (this was to ensure that simple relations, such as a point following the mouse pointer, are not infuriating to express). Still, it was necessary in the case of certain elements---especially those which must transcend the tree structure altogether, like arrows between boxes---to bite this bullet, one way or another.

Again, we return to how we tend to work things out in the freedom of paper. Co-ordinate systems, here merely positionally displaced, have their origins here and there and have vectors between them. The rods thus far let us visually express relations between global Points; now was a question of expressing one global Point as a displacement from another (the \svgel{g} transform). New rod Observables `p2_from_p1` and `p1_from_p2` do the vector subtraction, which can then be propagated as local co-ordinates to children. It is nice to express the relation (as well as see it!) this way (Figure\ \ref{fig:rods}).

## Context-Appropriate Ontologies
Each API has its own conventions, including a way of naming and structuring expressions: an *ontology* \parencite{Crit-semprola}. The \OSFA{} approach is exemplified in such interfaces.

In SVG, we express a rectangle by

\begin{lstlisting}
<rect x="10" y="10" width="600" height="400">
\end{lstlisting}

The SVG specification *defines* to the user that a rect simply *is* a top-left corner, a width, and a height---and that's it. However, this SVG-approved parametrisation of a rectangle is far from the only one, and thus is, unsurprisingly, ill-fitted to some contexts.

We find it natural to resize boxes by dragging any of their four corners, so we wanted this in \OROM{}/SVG. In this context, a "rectangle" is *seen as* four points: top-left, top-right, bottom-left, bottom-right. Obviously this is not a *minimal* description, since given \eg{} the top-left and bottom-right, the other two points can be inferred. But one way or another, to be able to drag any of them, all four points must be present at *some* level. This alternative ontology was polyfilled in the form of a "rect controls" class that can be attached manually to any SVG \svgel{rect}. The `x` and `y` are subscribed to the top-left Point;^[In the usual case where the \svgel{rect} is part of a box, the top-left instead controls the parent \svgel{g}'s `transform`---but the idea is the same.] `width` and `height` are subscribed to Rod lengths.

Another example is to be found in the DOM's event listener model. Conceptually, many "events" are in fact changes to the state of some physical device. And both keyboard keys and mouse buttons, for example, have two states---pressed, not pressed---which ought to make them *interchangeable* to some extent. Indeed, this is why there is such a thing as "key mapping"^[For example, the player can re-assign the action "shoot" from its default mouse button to a keyboard key, or another action from keyboard to mouse, as it suits them.] in PC games. But the official ontology of the Web makes it nontrivial to do this.

To start with, the situation is modelled not as the *changing* of some time-varying property, but instead as a sort of Cartesian product of subroutines. Rather than, say, a piece of live-state for the left mouse button (LMB), we get `onmousedown` and `onmouseup`. Thankfully we do not have `onAdown`/`onAup`, `onBdown/onBup`, ... all the way to `onZdown`/`onZup`, but only `onkeydown`/`onkeyup`. Yet this is just one of the many possible ways to slice this 3-dimensional^[Device (mouse, keyboard), sub-device (button, key), state (up, down)] space.

In \OROM{}/SVG we did not quite want to re-map keys, but we did often want to have things follow the mouse when dragged. In order to do this, we reified the mouse pointer and its position, letting me write `subscribe(point.position, pointer.position)`. The Point has an `is_considering_me` Observable wrapping `onmouseover` and `onmouseout`, while a `LMB_is_down` Observable reifies the LMB state. The aforementioned subscription is set up whenever `is_considering_me` and `LMB_is_down` become true, and torn down otherwise. We tended to think of this in the form "subscribe to pointer *only when* pointer is-considering-me *and* LMB is down", but we could live with this notation as a future polyfill in JavaScript.

The way these things are connected to the browser's event listeners could be called "device drivers"---an approach described in \parencite{Dadgum66}. It amounts to translating information from the Web's ontology into that of our substrate, as early as possible (Listing\ \ref{lst:drvs}).

\begin{lstlisting}[
  label={lst:drvs},float,captionpos=b,
  caption={``Device drivers'' in the code translate the Web's ontology into a more structured form.},
]
svg.onmousedown = e =>
  if (e.button === 0)
    change(left_mouse_button_is_down, true);

svg.onmouseup = e =>
  if (e.button === 0)
    change(left_mouse_button_is_down, false);

svg.onmousemove = e => {
  let client_pos = [e.clientX, e.clientY];
  let r = svg.getBoundingClientRect();
  let pos = vsub(client_pos, [r.left, r.top]);
  change(pointer.position, pos);
};
\end{lstlisting}

It takes some frustration and experience to get used to the idea that one has a right to polyfill in alternative representations. Before we came to this conclusion, we used to twist our heads around translating our intentions into the \xywh{} parameters, and un-translating when reading back the code we had produced. Once we get used to having to adapt our mental imagery to a single way of doing things (\ie{} learning to code), it makes sense to simply expect to see more of it---especially when we know we are new, surrounded by veterans who see no problem, and so on. Nowadays, we take the position that these are simply *widespread* failings of our way of doing things, instead of *our* failure to adapt to the way software is.

What would it look like to support multiple ontologies? Naïvely, there are two answers: anticipate the possibilities ahead of time, or support users adding their own. It is unclear if we can do better on the latter than simply "support polyfilling". As for the former, anticipating the diversity of ways someone might look at the world is generally doomed to fail. But in the special case of fairly *formalised* concepts such as geometrical shapes or mathematics, we could suggest that simple *under-specification* is the root of the problem in SVG. Instead of the specification explaining in English that "`x` and `y` are the co-ordinates of the top left-hand corner...", it might be better to make these relations machine-readable or *embodied* in the API. For the rect this might look like:

- A `rect` is...
- a `polygon` (to be defined elsewhere)
- defined by 4 *degrees of freedom* `x y width height` (internal representation)
- where there are 4 *vertices*, all `point`s
    + `[x,y]` called `top-left`
    + `[x+width,y]` called `top-right`
    + `[x,y+height]` called `bot-left`
    + `[x+width,y+height]` called `bot-right`

The hope is that if we then specify enough information---say, the bot-left and top-right---then the runtime has all it needs to derive its internal 4 degrees of freedom.

In a crude sense, we have successfully anticipated the most obvious ontologies of a rectangle here. However, we missed out the "centre plus half-width and half-height" formulation, among others. Is this a futile effort even for precise mathematical knowledge structures, or could enough formalisation of Euclidean geometry in the Web platform put an end to this sort of polyfilling? We must leave this as an open question.

There is, in fact, a part of the SVG specification \parencite{SVG-rect} that comes frustratingly close to the above. It spells out how the \svgel{rect} parameters "reduce" to more primitive line-drawing steps, including expressions for the four corners in terms of \xywh{}. The problem is, this reduction is still described in *natural language* for a human implementor, missing the potential in such a formulation. The point here is that a machine-readable description could easily remain human-readable, but a natural-language description is not easily machine-readable.

It is worth admitting that there is a significant shortcoming of our polyfill here, and perhaps throughout. It has taken the form of *overriding* the default ontology, *replacing* it with the one we preferred. From our perspective, this is fine. But if another person were to join in the development of \OROM{}/SVG, they would be stuck in the same situation we were in, unless they fully agreed with our ontologies.

The desideratum of "ontology co-existence" is very much an open research problem. \parencite{Crit-semprola} gives a taste of the thorny complications unaddressed by a "Semiotic Programming" solution \parencite{Semprola}, while \parencite{Kell-C} points out how the idea of "linking" is unconcerned with whatever language compiled to the object code; language-agnosticism is one salient example of ontology co-existence. This is especially clear if we agree with the latter author's view that "which language" ought to be an *implementation detail*, that languages ought to be merely *different views* onto the same thing.

These considerations did not occur to us because this project is very idiosyncratic to our interests, at least in its initial stage. We always worked on the assumption that we were the only developers. However, a future \OROM{} system in a mature state should by no means be bound to this fact. It is also worth considering *our future selves* as a different agent, with different ontology preferences. In that case, the method here will need to be re-examined.

## Extensional Functions
Time and time again we come across the same pattern of partitioning system state: trees or graphs of dictionaries, a.k.a. Maps, a.k.a. associative arrays. We are talking about the following pattern:

* Filesystem paths `/path/to/some/file`
* Python / Java modules `com.example.pkg.subpkg`
* JavaScript objects `window.my_obj.component`

The common case is an association of a textual name to an arbitrary value. We find it useful to see this as a mathematical "function" defined *extensionally* by listing its input/output mappings---this opposed to an *intensional* definition such as $x \mapsto 2x+3$, or a computer program.

Extensional functions are perhaps the most basic form of Knowledge Representation, and match natural language very well. "The bicycle's wheels' spokes are silver" straightforwardly translates to a function equation:
\begin{equation*}
\mathrm{root} (\mathrm{bicycle}) (\mathrm{wheels}) (\mathrm{spokes}) (\mathrm{colour}) = \mathrm{root} (\mathrm{silver})
\end{equation*}

That is, whatever object is the output of `silver` in the top-level `root` function, the output of `colour` (in the function on the left) points to the same object. The ability to partition a system in this way enables what \parencite{Externalize} calls a "natural co-ordinate system" for a piece of software, crucial for understanding and adaptability by others.

It seems that this way of expressing the "parts" of a system is an inevitable requirement of any programming substrate. Some languages, such as C, do have static, *compile-time* associative arrays (`struct`s). In our experience this is usually not enough, and it's necessary to bring in a library or clutter the code with a home-grown approximation to dynamic ones. Some parts of the \OROM{} authors' C code were confusing until we realised they were just the guts of a basic associative-array implementation; when we switched to JavaScript, these lines vanished.

Perhaps another strength of JavaScript is its low *concrete syntax cost* for *instances* or *literals* of associative arrays. Writing or reading

\begin{lstlisting}
a = {
  b1: { c1: z, c2: y },
  b2: { c3: x, c4: w }
};
\end{lstlisting}

is more "What You See Is What You Get" than the imperative-style

\begin{lstlisting}
a = new Map();
a.set('b1', new Map());
a.get('b1').set('c1', z);
a.get('b1').set('c2', y);
a.set('b2', new Map());
a.get('b2').set('c3', x);
a.get('b2').set('c4', w);
\end{lstlisting}

The latter style is unfortunately still required in JavaScript for extensional functions with *non-string* inputs.

Our original flat, tabular representation in \OROM{}/HTML did support a single level of such "extensional functions", but no more. In other words, the output of such a mapping could not be a further mapping itself. This was problematic because vtables, conceptually, have two levels of such nesting: the top-level fields, and the "methods dictionary" which contains its own input-output pairs. To distinguish between these, we actually stored method mappings with an ad-hoc "-" character (Figure\ \ref{fig:orom-html}).

By contrast, after switching to the fully nestable box substrate in \OROM{}/SVG, we could directly express the model we were thinking in. Quite simply, the \texttt{vtable} box has a \texttt{methods} box, and that's where the method boxes go (Figure\ \ref{fig:orom-svg}). If we had stayed in HTML, there is a good risk we would have had to polyfill in extensional functions---whether through nested \svgel{table} elements, additional "-" characters, or something else.

## Persistence
This refers to exposing structured program state to the user. This data can then be saved and used to restore the system at a later date, but it can also be tweaked with corresponding changes reflected in the system.

Persistence was absolutely necessary to continue \OROM{}/SVG development past a certain point. This is because, upon discovering a bug and fixing it in the source code, the web page must be refreshed and started anew. In the beginning, when verifying that box drawing with the mouse is working correctly, this is not much of a problem: upon refresh, the blank initial state is restored and we could draw again. But as the substrate matured, and we began to implement parts of the target system (\OROM{})---the cycle of finding a bug, tearing down the system, refreshing and losing work, and manually building it up again, proved frustrating. Because the system could only be patched externally and restarted, there needed to be some way to persist changes which live in the DOM, rather than the source files.

Normally, this can be as simple as autosave to the filesystem. But the Web platform is very wary of this,^[As anyone who learns WebGL can attest to, when they discover they must run a local Web server to provide image files for textures (since any filesystem requests will be rejected for security).] so the solution we turned to was manually copying the markup in the browser's inspector (Figure\ \ref{fig:html-inspector}).

\begin{figure}
  \centering
  \includegraphics[width=\linewidth]{html-inspector.png}
  \caption{To save the state of the system, the SVG group corresponding to the \texttt{root} box is copied and pasted inside the HTML file's \svgel{svg} element.\label{fig:html-inspector}}
\end{figure}

This required a slight change towards an architecture where the all the data required to reconstruct the system's current state is contained in the inspector HTML, as opposed to hidden JS properties. Where previously "boxes" were created first as invisible JS objects responsible for some SVG, now it was the other way round. When a rect is clicked, the system must look at some SVG and interpret it "on demand" as a box (although this helper object, once created, can be cached in the SVG nodes).

Persistence seems to be a weaker cousin of *externalisability*, defined in \parencite{Externalise}. As it stands, the system does not quite qualify as externalisable. When making changes to the HTML in the element inspector, the system's behaviour *ought* to adjust to match, but this is not currently guaranteed. Depending on the ability to listen for changes in node attributes or children, this may remain the case.

# The Id System as a Part of the Solution
The \OROM{} system was designed with a goal of eliminating the "artificial" distinction between implementation language and end-user language, by means of a mostly self-defining, or "meta-circular" object model. This general way of working on a piece of software "by means of itself" is easy to agree with, even if OOP is not to everyone's taste. It is an open question whether \OROM{}'s approach could be applied to other programming styles.

The allure of meta-circularity here is not merely its novelty, but that it paves the way to end-user empowerment. This is somewhat misleading because in \OROM{}, these "end-users" are actually programmers. However, its purpose is to free a programmer's dependence on some distant and busy language designer. It seems plausible that this is a step on the way to enabling *creation* of a piece of software for which non-programmers do not depend on programmers.

We emphasise that this is the *allure* of \OROM{}. However, there are some significant practical issues that must be overcome or clarified first.

## Minimal Descriptions
The \OROM{} paper was part of the STEPS project of Alan Kay's VPRI.^[Viewpoints Research Institute] This project argued that the immense level of "accidental complexity" present in software implementation could be reduced, and Kay dreams of an end result analogous to a "Maxwell's Equations" of software. That is: the behaviour of electromagnetic fields can be represented in four short equations that fit "on a T-shirt".

A meta-circular Lisp interpreter fits on a page. Could we aim at a similar "fundamental description of software" that fits on something less than millions of lines of code?

This argument as stated suffers by glossing over an important fact. Maxwell's equations certainly do fit onto a T-shirt, but most people will not be able to explain what they mean. What typically amounts to years of study is compressed into those mathematical symbols, and the learning material involved most certainly does *not* fit on a T-shirt. The obvious *reductio ad absurdum* is where we encapsulate these equations under a single symbol,^[In fact, this is almost achieved by the formalism of Geometric Calculus, which reduces them to only one equation.] $\mathcal{M}$. $\mathcal{M}$ is defined as "Maxwell's Equations are true". This fits on a coin, but good luck doing anything with it.

We say this not to dismiss the argument, but to highlight the *actually hard part* of getting a "concise description" of some system; defining complexity away into a symbol helps us no more than naming the solution to an equation "$x$". There is a connection with data compression: even if the data is successfully compressed into a smaller file, the size of the compression *program* should be added as well. What matters is to reduce the *combined* size of notation and substrate.

Further, there is perhaps a risk of optimising for *formal* rather than *practical* minimality (\eg{} the Turing machine). \OROM{}'s "minimality" does not necessarily translate into "simplicity". It suffers from the same *cognitive* complexity, or need for study, as Maxwell's Equations; we cannot stress enough the amount of effort we have put in to wrap our heads around the self-referential "vtable vtable" and the task of self-implementation. An overall better system might be one which is easier to pick up or understand, even if the number of formal objects is not as minimal.

In the conclusion of \parencite{OROM}, the authors note that "it is not necessarily a friendly model for hand-written code", suggesting its use as a compilation target. In a similar vein, it could function as the kernel of a much more familiar system (on the surface)---it is, after all, supposed to be a vehicle that other things sit on, rather than the final user interface itself. In fact, this is precisely the approach set out in the full paper on COLAs \parencite{COLAs}.

## Self-Implementation
The COLAs paper expands on \OROM{}, giving it a "structural" role complemented by a Lisp-like programming language. Section\ 6.1 sketches out the intended "bootstrapping" process, ending with a self-sustaining version of the system.

However, this task again suffers from the same cognitive complexity as any other self-referential circle. This is even more so for us, having made the complexity of graphics and interaction somehow still "part of the system". Figuring out exactly how the authors' bootstrapping process of their wholly *language-based* system maps on to our task is, as far as we know, uncharted territory.

Finally, in \parencite{Crit-semprola}, there is a warning against "obsession with completely homogenous systems" written in themselves. If this approach is doomed then obviously we want to take a different one, but the argument against it seems to hinge on what constitutes "failure". It considers Smalltalk and Lisp as "unsuitable", yet the authors of \OROM{} clearly think the opposite. The disagreement probably hinges on "unsuitable for *what*?" and it would be clearer if the following two questions were answered:

1. Is there a difference in (final, intermediate) goals between the two views?
2. Could systems like \OROM{} and COLAs plausibly succeed at their own stated goals?

# Evaluation

In this section we will evaluate \OROM{}/SVG according to the Technical Dimensions from Section\ \ref{the-three-properties-as-dimensions} as well as Olsen's criteria for User Interface Systems. It might seem appropriate to also perform an evaluation via the Cognitive Dimensions of Notation. However, this would not actually tell us anything interesting, because the novel contribution of this prototype is not its notation *per se.* Rather, we take it as a given that the notation is designed in a certain way that suits our purposes. The unknowns were: (a) the work involved in achieving this notation, which we have detailed, and (b) what sort of system we could build on top. It is the latter that we seek to evaluate here.

## Measures of Self-Sustainability
\criterion{Substrate Size: 1354 LoC.}
The system was used to implement the basic Id object model, in which low-level details like the definitions of `send()` and `bind()` are present as user-modifiable functions. However, it could be argued that this does not count as the substrate, since it is the *program* implemented via the programming system we are evaluating. Instead, we must consider the JavaScript source code files as the substrate proper.

There are two namespaces: the platform's JavaScript object graph, and the box tree. User code can add to both of these. Any global functions in the source code can be replaced by new definitions at runtime, while any local functions will be invisible to anything at the user level (even the JS console).

Can the user interface be arbitrarily changed from within the system? It might be thought that this is possible because JS boxes can use browser APIs to change the DOM styling. While this is true, there is still inaccessible code in functions defined in the source file. Some of these functions create boxes, and the style they use is confined to the source file and is not a runtime-accessible variable. Thus important aspects of the user interface remain as part of the substrate.

It seems like a fair first approximation to use the line count of the source files as a proxy for the substrate size. This is 1354 lines of code. It is likely that functionality could be moved in-system from here, reducing this figure, though the lower limit is hard to estimate. 

\criterion{Persistence Effort: High}
On the one hand, persistence is possible through the manual procedure described in Section\ \ref{persistence}. We built up various aspects of the system's current state this way, especially the box positioning and layout. On the other, this is a tedious, manual operation requiring attention to which parts of the DOM constitute the system state.

\criterion{Code Editing: Present.} Code in JS boxes can be edited via plain text operations.

\criterion{Data Execution: Present.}
JavaScript code in text boxes can be run with a key combination. This code can call functions defined in the source file, and browser APIs, to create new boxes and obj-dicts. These can contain text boxes which can then be filled with JavaScript code strings.

## Measures of Notational Freedom
\criterion{Custom syntax effort: moderate.} If we had implemented the "code" half of the COLA, instead of only the object model, we could take advantage of the Mood-Specific Language facilities. In the absence of these, however, the effort required to slot in an existing syntax definition at the user-level is moderate. It would require writing the relevant parsing and interpretation code in a JS box. In addition, the meaning of Ctrl+Enter as "execute" is embedded in the substrate and may not be possible to change in-system, necessitating a workaround for executing custom code. However, this would not take much work to move in-system.
 
\criterion{Custom language effort: high.} Where the previous dimension concerned operations compatible with text boxes and JS string processing, this one requires additional graphical freedoms. Access to the DOM APIs in JS boxes could allow one to add new interfaces and interactions, but these would not necessarily integrate well with our box substrate. As with the previous dimension, there may well be parts of the substrate that would have to be moved in-system in order to support custom languages at the user level.

\criterion{Custom notation effort: high.} This follows the same reasoning as the previous dimension.

\joel{
\criterion{Are there multiple syntaxes for textual notation? No.}
Only JavaScript syntax is available.

\criterion{Does the system make use of GUI elements? Yes.}
The nested boxes have draggable corners for resizing/repositioning. There are also arrows that transcend the tree structure and certainly would not fit in a purely textual model.

\criterion{Is it possible to view and edit data as tree structures? Yes.}
The nested boxes use spatial containment to represent explicit tree structure. They can be drawn into existence.

\criterion{Does the system allow freeform arrangement and sizing of data items? Yes.}
The boxes can be repositioned and resized arbitrarily and this preserves any arrow topology.

\criterion{Is there support for custom user-supplied grammars? No.}
The system only implements the "structural" half of a COLA, so it lacks support for mood-specific languages.

\criterion{Is there support for custom user-supplied notations? No.}
Ditto.
}

## Measures of Explicit Structure
\criterion{Format errors: moderate.}
On the one hand, as regards "data", the system allows boxes and arrows to be created only in valid places. (If not, this is a bug rather than the intended behaviour of the system.)

On the other, the JavaScript code is edited in HTML text boxes. No syntax checks are performed by the substrate; errors are only discovered when calling the platform's `eval()` function to try and run the code. Therefore, all syntax/format errors related to JavaScript can be passed on to consumers of code.

In total, the measure of Format Errors may be strictly less than if both code and data was specified in JavaScript, but remains fairly moderate.

\criterion{String wrangling effort: moderate.}
Code only exists in the system as JavaScript strings. Therefore, if user-level code ever wants to process or generate code, it needs to convert to/from these strings.

For data, there are user-level JavaScript functions for locating a box based on its path and accessing sub-structures inside it. It is interesting to note that internally, these functions do need to analyse the "shape" of SVG trees and recognise the patterns that identify boxes, arrows, and so on. This is recovery of higher-level structures implicit in a tree, rather than tree structures implicit in a sequence. Regardless, this takes place at the implementation level and the user level need not be aware of it.

By weighting code-processing and data-processing scenarios equally, we see no penalty from the latter and some penalty from the former. Thus we say the system's usage requires moderate string-wrangling effort.

## Situation, Task, User, Importance
\OROM{}/SVG helps this author (User) visually explore the Id object model (Task) for research (Situation). The design as presented in \parencite{OROM} showed promise, but was hard to understand and simulate on paper (Importance). The value lies in the *Expressive Match:* the static diagrams in the source work have been turned into the dynamic medium in which the object model is built. However, these diagrams were structural and included code as text strings, which was replicated in the dynamic versions.

# Conclusions and Future Work
## What Did It Take, and Why?
It takes a lot of work to create in a substrate that feels suited to the domain. Much of this work involves filling in the machinery necessary to an awful lot of software, rather than the ways one's domain *differs from* this common case.

In an ideal world, there would be no accidental complexity, no boilerplate, no polyfilling. But this can't be the same as saying there would be no *work* to do---that sounds like a world where *essential* complexity has also been removed.

Instead, we can note that software ecosystems typically centre around a "core", such as a language, with a surrounding "periphery", such as libraries not bundled with the core language. The core (language syntax, semantics, standard libraries) acts as a common platform uniting the community; the periphery consists of functionality that is not "built-in" to this core, both realised as software (\eg{} libraries), and that which is as-of-yet unrealised or nonexistent.

There is a constant process of negotiation determining what should be part of the shared core versus the responsibility of the periphery. But at any given time, every community draws the line somewhere.

To a first approximation, what is "built-in" encompasses the features and concerns that are *common* to the entire community. Features that are of minority or niche use belong outside; it is one's own responsibility to implement them, or to integrate with an existing implementation.

Perhaps in an ideal world, someone could realise their vision in software by focusing all their effort on what makes their idea *unique*, or what is *idiosyncratic* to it. In other words, they would only be responsible for that part of their idea which:

1. It would be unrealistic to expect the wider community to have *thought of already*, and
2. It would be unfair to expect the wider community to *build on his or her behalf.*

If the core Web platform (particularly JavaScript) is meant *for* supporting "web apps", and this is indeed how it is used, then it seems it has not yet fully absorbed the functionality common to this usage. At present, this is simply a surprise, or a puzzle, and it might simply be a matter of time before it catches up.

Nevertheless, we wished to explicitly highlight the obstacles one is likely to meet when starting from this platform. They include not only those specific to the Web, but also issues that occur far more widely (\eg{} monopoly ontologies). We showed how, in the short-term, we can overcome these with solutions intended to be domain-appropriate to their use (within the syntactic limits of JavaScript).

## What Next?
\OROM{}/SVG more or less realises our desired notation for implementing \OROM{}. However, there is one major area we failed to make domain-appropriate. Despite representing the "data" parts of the system as we wanted, the *computational* parts of \OROM{} were just transplanted into the text boxes as source code. This is not full Explicit Structure, and having user-modifiable JavaScript code also makes it hard to step through it in the browser's debugger. It could be worth exploring alternative ways to express the code in the box notation we have (as well as ways to make it gradually less tedious).

However, we will now leave the "notation-first" approach here and move on to putting self-sustainability first, making sure to have explicitly structured code as well as data. We will not seek to build the COLA design *per se* but will still draw inspiration from its approach. This is the topic of the next chapter.

\joel{
Still, in this mostly-suitable substrate for \OROM{}, I can continue with my project of seeing whether the *allure* of \parencite{COLAs} can be saved from the text-based "hidden world" limitation that pervades it. The obvious next step is attempting "self-implementation". This is desirable is because I still have not escaped my text editor. Any changes to my \OROM{}/SVG substrate (of nested box drawing) require going back to the script files; I still cannot take advantage of the system I have developed, to ease its own development. In the words of \parencite{COLAs}, I wish to make it so that the original JavaScript files can be "jettisoned without remorse".

In Section\ \ref{retained-mode-vector-graphics}, I touched on how everyone with a browser has access to a powerful vector graphics editor (SVG) locked behind a completely inappropriate UI (the JS Console). It is similar for 3D graphics (WebGL), and sound and music (Web Audio). Similar observations are made about "native" OS apps in \parencite{Prog21-dyn}. Unfortunately for that domain, the interface that unlocks your operating system's range of functionalities---batch-mode compilation---is quite far from the affordances of the (interactive) JS Console.

I intend to use \OROM{}/SVG to "tame" SVG and other JavaScript APIs, with some minimal on-demand visualisation in the large portion of the screen next to the JS console. This is an attempt to generalise the "rect controls", which allow the obvious geometric properties of a \svgel{rect} to be directly manipulated (Section\ \ref{context-appropriate-ontologies}). Giving \OROM{} access to Web technologies in this way is essential for evolving it into a *fully-featured* self-changeable software environment, which could allow as much domain-specifc representation as its user wishes.
}
