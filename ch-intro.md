\hypertarget{intro}{%
\chapter{Introduction}\label{intro}}

\note{in html/dom - you can open object browser and edit user interface, but this does not work for editing code in the same way - that is bulk JS with some substrings you would have to edit; what if everything was visible accessible (shareable) and editable, including structured code?

if I want to draw GUI, I can either write code or use GUI editor like VB or Hypercard - but the GUIs exist as separate to the programming system - could they be integrated like DSLs?

third - maybe COLA or Smalltalk - do self-sustainability but not quite.

Capabilities:
* Create (author) static content. static authoring. Hc, St
* Create (author) dynamic content. dynamic authoring. Hc, St
* Make a small change without having to restart the world. (self-sus) pokeability
* Change structural state without having to re-build the structure. (parsing bad) static pokeability. Hc St Web
* Change behavioural state without having to re-build the behaviour. dynamic pokeability Hc St
* Domain-specific notations. COLA

1. what is your quest - for a non-computer person
2. many pieces of the puzzle exist - web, hypercard, smalltalk
3. why hasn't anyone done this? PL paradigm is wrong - we need systems
4. systems allow better thinking! here is more specifically what I'm working on
}

When we have an idea for some computer software, and try to make this idea a reality, we are forced to confront two types of complexity: the *essential* and the *accidental*. We know there is "no such thing as a free lunch", so we are able to accept the burden of whatever complexity is actually intrinsic to our idea. If we have a simple idea, we are prepared to do a little work; if it is more ambitious, we will accept having to do more work. Unfortunately, this *essential* complexity is often swamped by unwelcome incursions of tedious busy-work. Concepts that appear simple must be spelled out in great detail for a computer. This is the *accidental complexity* that is widespread in programming \parencite{MMM}.

This is particularly egregious when the "idea" is merely to change or fix some small issue. Suppose we are using an app where the text is hard to read owing to a similar background colour. The designers have not included a feature for changing the colour of UI elements. A programmer would know that there must be some API being called to render the background. This API will receive the colour from a few numbers in the app's memory. If only we could find these numbers and change them, we would be able to read the text.

What, therefore, does it take to find and change a colour? The app itself provides no way to proceed through its surface interface to its internal mechanisms. Thus, the accidental complexity we must face includes working with some external tool that can open it up. We could attach an assembly-level debugger to the app process and stare at hex dumps for a long time, eventually figuring out which address holds the colour. Such an expert task would take an extremely long time even for someone with the relevant experience. It would only let us make a change to the *running* app; we would have to repeat the procedure every time we ran the app.

Alternatively, we could hope that the app is open-source, download the code, setup the build system, locate the relevant code, re-build the app, and re-install it. Each of these steps is also an expert task which would be incredibly lengthy on a novel codebase, even for experienced programmers. Furthermore, this approach entails destroying the running instance of the app and re-initialising it, possibly losing unsaved work.

In the worst case, both of these approaches could be blocked; run-time tampering could be prevented by the security policies of mobile devices, while re-building from source cannot work without access *to* that source. Suffice to say, none of this is suitable for an average user. Even a seasoned programmer would consider it not worth the trouble. Our task of changing a colour, while technically possible, has a severely *disproportionate* accidental complexity cost.

In specific situations, software authors do have good reasons to restrict access to internals. In a game, it is important to enforce the rules; access to internals would enable arbitrary cheating. However, this is a *special case* not representative of most types of software. Despite this, we are unable to simply *choose* to build software that is "open". Even if *we* wrote the app and desire to support adaptation beyond what we anticipated, we face the fact that our tools can only create software that is "closed". The task of "supporting unanticipated modification" is itself a *feature* that we must somehow figure out and implement on top, and it is unclear how to achieve such a feature. Nevertheless, it is worth striving for a world where this accidental complexity is as reduced as possible. We might expect this to involve a mix of "demolition" work---that of removing barriers that have been placed in the way---and "construction" work of building tools that help us work more effectively.

# A More Compelling Example
The reason we have considered changing a colour is that this is an extremely small change that nevertheless has a plausible user story behind it (as opposed to, say, flipping a single bit in the app's memory just to see what happens). What we have established is that *even a tiny change* involves egregious accidental complexity, which does not provide much hope for more practical changes we might actually want to make. What is an example of a more realistic adaptation of one's software? There are many ways an end-user might want to adapt some software they are using, but a clearer example is a frequent activity of programmers: debugging.

The chief way in which programmers "adapt" existing software is by fixing bugs. This involves two stages: discovering the cause of the bug, and applying the fix. In order to discover the cause, it is helpful to change values in the program while it is running. In the default state of a program, however, this is not possible. Instead, the programmer must return to the source code and make modifications there (either to change values directly, or by writing code do so in response to specific run-time conditions) and add logging commands so they may see diagnostics at run time.

Alternatively, the programmer may run the program in tandem with a separate tool, a dedicated *debugger,* which fills in for this "missing" functionality of viewing (and possibly changing) run-time values as well as slowly stepping through the code as it executes. However, not even this will help with the second half of the job, that of changing the program to fix the bug. Realistically, the debugger only permits changing run-time *data* values, but let us grant for the sake of argument that it can also edit code. Even in this case, the changes will be limited to the *running instance* of the program, and will be gone when it terminates. In order to fix the bug in a lasting way, the programmer must once again leave the *program* (the thing ostensibly being fixed) and go *outside* to the separate source code, and make the change there. In sum, this is a large amount of accidental complexity required for diagnosing and fixing a bug.

If only there were some way to have the program supply its *own* debugger, and provide access to its internal data and code through its running "surface", and finally persist any changes made that way, then the actual complexity of the enterprise could be brought closer to the essential complexity of the bug itself.

# How Should Things Work?

Imagine a world where the average computer user can patch or improve their software the same way they might change a lightbulb or perform DIY in their home. This clearly relies on the ability to make small *piecemeal* changes to their home, without having to demolish the place and re-build it anew. We will call this *naïve pokeability*:^[By analogy to the "Naïve Realism" principle found in Boxer\ \parencite{Boxer-design}.]

\begin{defn}[Naïve pokeability]
\label{def:naive-pokeability}
A change has \emph{naïve pokeability} if it is possible to make the change while the software is \emph{running} without having to consider the implications of restarting it.
\end{defn}

Furthermore, the common-sense expectation is that the changes *persist* into the future:

\begin{defn}[Persistent]
\label{def:persistent}
The result of a change is \emph{persistent} if it remains until a future change overrides its effect.
\end{defn}

\begin{defn}[Transient]
\label{def:transient}
A change is \emph{transient} if, in the absence of special measures, it will be undone within a short timeframe and its effects will not last.
\end{defn}

Additionally, the tools for making changes are of an appropriate scale and reside within the system. Changes are *self-supplied* and, if this covers all possible changes, we have a *self-sustainable* environment.

\begin{defn}[Self-supplied]
\label{def:self-supplied}
A change is \emph{self-supplied} by a piece of software if you can achieve the change by only using the software.
\end{defn}

\begin{defn}[Self-sustainable]
\label{def:self-sustainable}
A software system is \emph{self-sustainable} if arbitrary changes to it are self-supplied.
\end{defn}

The ideal system functions like a workshop where new tools can be fashioned using existing tools as needed. They can be big or small, and this ensures that we can use the "right tool for the job", no matter the scale.

\begin{defn}[\RTFJ]
\label{def:right-tool}
This is a principle in programming which acknowledges that tools have differing strengths and weaknesses for different tasks. To ``\URTFJ'' is an ideal that relies on either an \emph{existing} range from which to select the best tool, or the capacity to design and build it on-demand.
\end{defn}

\begin{defn}[One-Size-Fits-All]
\label{def:osfa}
The opposite of ``\URTFJ''. This refers to using a single tool to do a wide range of tasks even though it may not be suited for some of them.
\end{defn}

\begin{defn}[Domain-Specific Adaptation]
\label{def:dsa}
This is a small or large part of a software system which provides its own custom interface for change.
\end{defn}

In standard practice, a program is generated from *source code* and put into a running state. To change the program, one must change the source code, destroy the program and re-create it anew. These steps are accomplished with separate tools, meaning that changes tend not to be self-supplied (Definition\ \ref{def:self-supplied}).

There is a limited notion of "\URTFJ" in that there are different programming languages. However, languages tend to enforce their syntax and semantics without permitting *smaller-scale* adaptation, and variation in these respects is normally restricted to textual notations. Furthermore, some situations may call for more general *notations* or graphical interfaces that do not work like a language, but the fact that programming is optimised for languages makes using such notations more difficult.

Instead of the above, computer software should act as "Personal Dynamic Media" \parencite{PersonalDynMedia}. In this vision, a software system is *designed* to be adapted and modified by its users. By performing an explicit action (\eg{} switching to "edit mode") the user can inspect the visible surface of the application to find the causes of its appearance in the form of code and data. They can also inspect a map of the non-visible implementation of the software's functionality and navigate to the relevant parts. There may be a common programming notation as a default, but where possible, parts of the implementation are presented in local notations or interfaces that are more easily understood. These interfaces can also be traced to *their* implementations and modified if desired. The user can then change any aspect of the software while it is running, without having to edit an external specification and destroy the running instance.

# A Fragmented Vision
Several pieces of this vision do exist, but not in an integrated whole. We can see some of the different characteristics we desire in software by examining the Web browser, HyperCard, and Smalltalk.

## Web pages, web apps, and browsers
The *web browser* has a powerful set of *developer tools* (Figure\ \ref{fig:web-dev-tools}). This includes the "inspector" which can be used to edit the page's underlying elements in the \ac{DOM}. For example, an ad can be removed by locating and deleting its element. Some of this underlying "state" may have no visual effects (\eg{} an element's ID attribute) and is thus hidden from an ordinary user. However, such state *is* visible from the inspector in the developer tools. This means that all of the "structural" state of a page is *potentially* visible, not to mention editable, in the web browser.

\begin{figure}
\centering
\includegraphics[width=\linewidth]{web-dev-tools.png}
\caption[Web Developer Tools.]{The Developer Tools available for any Web page or app in a modern browser.}
\label{fig:web-dev-tools}
\end{figure}

The above is worth contrasting with the case of the "behavioural" side of a web page oriented around the \ac{JS} programming language. Alongside the "structural" state of the web page, there is also the hidden state of \ac{JS} objects. The \ac{JS} console accepts commands which may read or change this state, but there is nothing like the element inspector for it.^[This may be because the \ac{DOM} is a tree structure while the \ac{JS} state is a general graph, and it's harder to build an editor for the latter.] What *is* visible in the dev tools is the *source code* of the scripts loaded by the page.

Many changes to the "behavioural" state can be accomplished in the console; for example, updating part of the state to a new object. However, this cannot be relied upon in the same way as \ac{DOM} editing because \ac{JS} language features prohibit many changes. For example, a variable declared in the source as `const` will not be changeable in the console. Moreover, fine-grained changes to code cannot be performed there either. The main unit of code organisation, the `function`, is an opaque object in the run-time environment; one cannot simply replace a particular line or expression within it. Instead, a complete new definition must be entered into the console to replace it wholesale. Yet even this will fail if the source declares the function `const`. In such cases, we lack *naïve pokeability* (Definition\ \ref{def:naive-pokeability}) and we have no choice but to edit the source files somehow. If the browser does not provide for local edits to be made to these files, a separate text editor must be used. The changes made in this way will only take effect once the scripts are reloaded by refreshing the page and losing its \ac{JS} state.

Let us return to the "structural" state of the web page and note that we are free to make arbitrarily fine-grained changes using the element inspector. These changes take effect immediately without having to reset anything. There are, in fact, HTML text files backing the page structure, but they cannot restrict the inspector tool in the way \ac{JS} files restrict the console. In short, the *state* of a web page has naïve pokeability while its dynamic *behaviour* does not reliably have this property.

There is one caveat: the HTML and \ac{JS} files are the "ground truth", so any changes made via the inspector or the console will disappear when the page is closed or refreshed. Changes made in the browser are *transient* (Definition\ \ref{def:transient}); only changes to the underlying files are *persistent* (Definition\ \ref{def:persistent}), and websites typically do not allow unknown individuals to change the files on their servers. All this is sad news for our user deleting their ad, as they will have to repeat it each time they access the page (or more realistically, use sophisticated programmatic middleware like an ad-blocking extension to make this automatic).

## HyperCard
Before the Web, "hypertext" was regularly created and distributed by people in the form of HyperCard stacks. Alan Kay criticised the web for having a browser that neglects to include an *authoring* tool, immediately limiting the *creation* of web pages to people who could code in a text editor. By contrast, in HyperCard, the viewer and editor exist integrated together (Figure\ \ref{fig:hypercard}). Furthermore, there is an "edit" mode whereby a user can remix content from someone else, even re-programming the dynamic behaviour.

\begin{figure}
\centering
\includegraphics[width=\linewidth]{hypercard.jpg}
\caption[HyperCard screenshot.]{HyperCard, a pre-Web Hypertext system, included a direct authoring tool to complement the browser.}
\label{fig:hypercard}
\end{figure}

These aspects of HyperCard's design encouraged a community of producer-consumers for hypertext content. The web's higher cost of authoring led to a lower producer-to-consumer ratio, restricting the kind of medium that it would become. Note that the naïve pokeability of the element inspector does not amount to *authoring* a web page; such an interface is designed for fine-grained *change* rather than coarse-grained creation. It is also oriented towards programmers, being part of the "developer tools", compared to HyperCard's presentation of authoring as a primary use of the software.

\hypertarget{smalltalk-and-cola}{%
\subsection{\texorpdfstring{Smalltalk and \acs{COLA}}{Smalltalk and COLA}}\label{smalltalk-and-cola}}
Smalltalk provides for behaviour editing at a finer granularity than the Web developer tools. Behaviour is separated first by class and then by method; only then is a text editor presented for the code (Figures\ \ref{fig:st80} and\ \ref{fig:pharo}). More importantly, changes to this code take effect once committed, with no "restarting" of the system taking place. The state of the system is persisted by default to an "image" file. In short, Smalltalk provides persistent naïve pokeability for both code and data, and pioneers a large degree of *Self-Sustainability* (Definition\ \ref{def:self-sustainable}).

\begin{figure}
\centering
\includegraphics[width=\linewidth]{st80.jpg}
\caption[Smalltalk-80 screenshot.]{The interface of Smalltalk-80, which introduced the \emph{class browser} visible at the top.}
\label{fig:st80}
\end{figure}

\begin{figure}
\centering
\includegraphics[width=\linewidth]{pharo.png}
\caption[Pharo class browser.]{The class browser in the modern \emph{Pharo} distribution of Smalltalk.}
\label{fig:pharo}
\end{figure}

That being said, Smalltalk systems tend to run on VMs that are implemented in a separate lower-level language like C++. Fundamental infrastructure such as object layout and memory management is available only as opaque primitives from the point of view of Smalltalk. Thus, to change these aspects one must still switch to a different programming system and re-compile.

The \acl{COLA} or \acs{COLA}\ \parencite{COLAs} \acused{COLA} seeks to take self-sustainability to the extreme, past the degree found in Smalltalk. \acs{COLA}'s minimal design makes said basic infrastructure self-supplied (Definition\ \ref{def:self-supplied}) so as to approximate a truly self-sustainable system. It is also designed to encourage domain-specific adaptations (Definition\ \ref{def:dsa}) down to a small scale of "\aclp{MSL}" (\acsp{MSL}) beyond the coarse-grained variation found with ordinary programming languages. However, the architecture as described does not have much to say about the user interface or graphics, taking place instead in the world of batch-mode transformations of streams.

\joel{
## The Missing Synthesis

We have given the above examples because they demonstrate some of our ideals from Section\ \ref{how-should-things-work}. Web browsers and Smalltalk exhibit GUIs with the potential for domain-specific graphical adaptation (Web, Smalltalk)
2. Reliable, persistent naïve pokeability of state and behaviour (Smalltalk, COLA)
3. Full self-sustainability with domain-specific languages (COLA)

Why has a synthesis of all three not been achieved? Part of the difficulty is that programming is framed in terms of *languages* with a focus on parsed syntax and batch-mode transformations. This makes it an uphill battle to achieve even one of these three properties. 
}

# Accidental Complexity Beyond Languages
In our experience, the three most important sources of accidental complexity in programming are as follows:

1. In order to make even a small change to a program, we must go to the source code which may require an entirely different language and way of thinking. We lack *Self-Sustainability.*
2. We must describe graphical constructs with language in order to fit them into program code. This represents a lack of what we will soon define as *Notational Freedom.*
3. We have to avoid syntax errors, escape certain characters, and write code for parsing and serialising. This represents a lack of what we will soon define as *Explicit Structure.*

These are not quite observations about programming *languages.* Instead, they concern the wider environment of tools in which programming is performed, such as the editor interface and facilities for running the programs.

It is important not to conflate "coding" in a programming language with programming itself. In this dissertation, we see *programming* as the general act of making a computer do things by itself. By this definition, coding, visual programming, programming by example, and deep learning are some specific *means* by which to program. If we ignore this subtlety, we risk unwittingly limiting the scope of innovative ideas in the following ways:

\begin{figure}
\centering
\includegraphics[width=\linewidth]{spreadsheet.png}
\caption[Spreadsheet interface.]{A spreadsheet contains text, but is not a \emph{syntax} or a \emph{language}; the grid lines are intrinsically graphical.}
\label{fig:spreadsheet}
\end{figure}

* Instead of seeking the right notation, interface or representation for the job, we might seek the right *textual syntax* for the job. If we cannot find one, the real reason may simply be that text is not well-suited to the job (Figure\ \ref{fig:spreadsheet}). Yet if text is all we know, we will be under the false impression that it is an *intrinsically* hard job.
* Instead of being able to make changes to a *running* program, we are stuck changing its source code and re-creating the program. It is easy to make "closed" programs this way and hard to make programs open to "re-programming" while running.
* Instead of seeking a software *system* open to unanticipated changes as it runs, we might seek intricate *language* features that give flexibility only for *compiling* a program.

A key problem is that there is no established term for this scope of programming research, and hence no body of work in which we may situate it. This is the crux of the matter: we need a more general programming *systems* approach instead. We will discuss this further in Chapter\ \ref{analysis} and use it in Chapter\ \ref{tech-dims} to propose a systematic framework by which to analyse programming systems. This framework will include three properties that are central to the dissertation and develop them in detail. We will now familiarise the reader with the basic outline of these three properties.

# The Three Properties
The goal at the end of Section\ \ref{how-should-things-work} is much too ambitious a scope to achieve in this dissertation. However, from Definitions\ \ref{def:naive-pokeability}--\ref{def:dsa} and the above discussion, we distill three properties that help address the issues we identified. They are:

1. *Self-sustainability:* being able to evolve and re-program a system, using itself, while it is running. (This is a more intuitive definition that agrees with what we said earlier in Definition\ \ref{def:self-sustainable}.)
2. *Notational Freedom:* being free to use any notation as desired to create any part of a program, at no additional cost beyond that required to implement the notation itself.
3. *Explicit Structure:* being able to work with data structures directly, unencumbered by the complexities of parsing and serialising strings.

## Importance of the Three Properties
We are interested in exploring, developing, and achieving the Three Properties in programming systems. We will refine and expand these definitions in later chapters, but they are reasonable to start with. Each one brings its own advantages to a programming system:

1. Self-Sustainability reduces the accidental complexity of having to make changes using a separate, unfamiliar programming system. It also permits *innovation feedback:* anything helpful created using the system can benefit not only other programs sitting atop the system, but also the system's own development.
2. Notational Freedom makes it easier to use the "\RTFJ" (Definition\ \ref{def:right-tool}). Once a programmer has decided what the right tool is in their specific context, Notational Freedom means they can use such a tool more easily as a Domain-Specifc Adaptation (Definition\ \ref{def:dsa}). For example, if diagrams are desired, Notational Freedom removes the traditional limitation to use ASCII art. More generally, Notational Freedom removes the need to describe graphical constructs using language.
3. Explicit Structure avoids various pitfalls of strings, both in terms of correctness and convenience. Consumers of a structure benefit from an editor that can only save valid structures, and producers benefit by discovering errors early instead of later during consumption. Writing programs to use such structures is improved if one does not have to maintain code for parsing and serialising or think about escaping special characters.

These properties are exhibited occasionally in different systems, as we will mention in Chapters \ref{background} and\ \ref{analysis}. However, it is rare to see two or all three present in the same system. This rarity suggests they are probably under-explored and under-developed, so we could stand to learn a lot by studying them. We do not doubt that these properties have drawbacks in addition to the above advantages, but we stand to gain from these advantages taking us closer to the ideal at the end of Section\ \ref{how-should-things-work}.

## The Three Properties in Combination
It is worth exploring the Three Properties in *combination* because they complement each other in the following ways.

Suppose a system already has Notational Freedom. Self-Sustainability makes it easier to add new notations to it. In the converse case of a system lacking Notational Freedom, Self-Sustainability makes it easier to add Notational Freedom *itself* and lets the benefits flow into all aspects of the system's development; this is what we called *innovation feedback*.

Notational Freedom is impossible to achieve without Explicit Structure. In a world of parsed strings and text editors, we are limited to what we will term *syntactic* freedom in Section\ \ref{notational-freedom}. Thus, Notational Freedom needs Explicit Structure as a necessary foundation.

Self-Sustainability also suffers without Explicit Structure. Self-Sustain\-ability is vaguely understood by analogy to self-hosting compilers, as we will see in Section\ \ref{precursors-of-self-sustainability}. The \ac{COLA} work\ \parencite{COLAs} follows this approach, remaining unclear on how such a property can be achieved in interactive, graphical systems. Explicit Structure lets us study the other two Properties more purely, without getting confused by the accidental complexities of parsing and escaping (we will expand on this in Section\ \ref{we-study-the-spherical-cow}).

We can prioritise the Three Properties based on the above inter-dependencies. Our primary goal is to explore Notational Freedom in interactive, graphical programming systems. To support this, we should achieve Self-Sustainability. To do both of these with minimal distraction, we should make sure to build on a foundation of Explicit Structure. We will not follow this order strictly, but it shows a logic as to how each property fits into the bigger picture. We see that the only way to discover how to achieve these goals is by *doing,* so we work to build a prototype programming system called *BootstrapLab* that makes progress on the Three Properties simultaneously.

# Thesis Statement and Contributions
The statement of our thesis is as follows:

> It is possible to add Notational Freedom to a given existing programming system, by embedding a Self-Sustainable system built on Explicit Structure.

We prove this by construction in the form of a prototype programming system called *BootstrapLab*, which is the topic of Chapter\ \ref{bl}. Our main contribution is not BootstrapLab itself, but rather the necessary steps and principles that its construction led us to *discover.* We believe that it should be possible to build these Three Properties atop a wide variety of programming systems; the goal of Chapter\ \ref{bl} is to document enough of a generalisable technique to make this feasible for the average programmer. It is as if we have developed the study of sorting by coming up with a prototype sorting algorithm---the new clarity is the important part, while the concrete program was just the vehicle that got us there.

Additionally, in order to assess how well BootstrapLab achieves the Three Properties, we propose a *technical dimensions* framework in Chapter\ \ref{tech-dims} for analysing programming systems, which is our secondary contribution. BootstrapLab, being a programming system, is then evaluated in terms of dimensions constituting the Three Properties. We then review related work in Chapter\ \ref{ch-related-work}. In Chapter\ \ref{future-work-and-conclusions} we acknowledge the limitations revealed by our evaluation, suggest future work for both of our contributions, and conclude with what we have learned and achieved.

# Supporting Publications
The following essay was adapted into Chapter\ \ref{bl}:

\fullcite{Onward22}

The following paper was adapted into Chapter\ \ref{tech-dims} and part of Chapter\ \ref{background}:

\fullcite{TechDims}

\joel{
# Imported from Convivial Salon '20
As someone who can code, I have already passed the first and most important hurdle for making full use of the potential of my computer. However, even in this supposedly empowered state, I am still far away from feeling the relationship between myself and software as between artisan and material, free to shape it into any form with effort proportional to complexity.

One would have thought that software-creation acts like hypothetical super-intelligent Artificial Intelligence (AI). That is: even though we start from a primitive base in the 50s (or even today), there would surely be a recursive process of self-improvement, building better software-creation tools with the existing ones, until an "expressivity singularity" where software becomes a workable material as described.

However, this didn't happen. Or at least, it is happening glacially slowly. The brute fact is that whenever you want to create software, you go to a text editor and figure out how to translate your design into that. The text editors, being software, were written with the help of previous text editors, and so on. It's undeniable that text editors have improved, even if you think it peaked with Emacs. We just don't seem able to go beyond them where it matters, such as visual domains ill-fitted to monospaced ASCII.

Amdahl's Law generalises the following idea: even when you spend hours of effort doubling the performance of a component used 1% of the time, your reward is a system overall improved by a mere 0.47%. Now, text coding is certainly ubiquitous, the 99% case in programming. A small improvement to text editing, if adopted by everyone, certainly does have a massive *intermediate* effect---but this only *matters* to the extent that text was helping us in the first place. If my goal is to draw or animate pictures, or create a digital synth from a frequency spectrogram, then giving me the ability to auto-indent my SVG markup is rather underwhelming as a productivity increase, as it doesn't target the core of the enterprise that makes it so hard.

\joel{My experience of coding, most of the projects requiring shapes (such as GUIs), leads me to conclude that no matter how much I improve my skill at a particular language, knowledge of libraries or even general coding ability, my predicament stays the same. Our basic method of creating software is optimised for an ever-diminishing proportion of the software we actually want to make; ill-optimised for the graphics, layout, interactivity and and basic physics---more on this later---that we usually require.}

As a programmer, I often feel stuck in a box I know I can never escape from: that box is the text editor, a fixed conduit through which all *fundamental* changes to my program must pass. It's not a part of the system I am building, so I can't even make use of features of the thing I'm developing, to make its own development easier.

Surely the trick is to *use* coding to build something *better than it*. And then use that, to build something even better. But there is an enormous breadth and depth of philosophies here, along with all sorts of concrete systems that failed to catch on. Even worse than this, is that in my very *language* here I am making the same mistake as the text editor---speaking in unqualified terms of "better" and "worse" as if there really is a \OSFA{} solution to software creation!

Of course what we *really* want is the ability for people to create *in the way that they think is best*^[To be clear: if someone *wants* to type out pictures in ASCII, let them---whether they do it for a challenge, or even if they find that more natural for themselves. But equally, if I want to do it another way, I should have that affordance.] in their particular context---to equip them to feasibly create the tools that suit them for the thing they want to make. And second-order tools that suit them for making the first-order tools, and so on. It would do no good to replace text-imperialism with anything-*else*-imperialism, which is one interpretation of calls for alternatives.

This dream goes beyond the familiar sense of what constitutes a "craft", as far as a strong melding of tool and material. Parallels can be drawn with industrialisation and a strong division of labour: the community as a whole produces its higher-order tools, but currently no single person can have the same autonomy. A (future) software craft could be expected to give this power to *individuals*, instead of the community alone. Whenever there are many small specialities (\eg{} languages, tools, or subject areas) each serving many clients, the \OSFA{} style is the best one can hope for. Adaptation to individual preferences and idiosyncrasies is only feasible when those individuals can do it themselves.

What we need is some system that not only lets us create software in a way that is "close to the problem domain" as decided by the user-developer, but also can augment or change itself to adapt to a different "way of creating". Existing systems seem to only have one of these properties without the other: Smalltalk and Lisp try to minimise arbitrary commitments of language *semantics* to this end, but their being textual languages is a fairly tough commitment to break out of. And it is not so hard to make a specific, *hard-baked* visual or alternative programming tool---but it is hard to make it re-programmable *without* having to go back to *its* textual source code.

# Imported from Onward '22

In the ordinary lifecycle of software, there is a hard separation between the _product_ and its _source_. The product may be any end-user application such as a game, and is created from the source by a _producer_, which is a compiler or other similar tool built for programmers. In this arrangement, the product's user has little ability to re-program it, beyond setting configuration parameters anticipated ahead of time. The user's only option is to modify the source (if it is available) and use the producer to create a new version of the product.

Curiously, this arrangement isn't limited to end-user products but also applies to most *programmer*-oriented products! In the ordinary programming experience, tools like the compiler or editor are themselves products with a separate source and producer. If the user of a language wants to re-program it beyond a customisation anticipated by its designer, they have to go and modify the compiler source code. If they are lucky, the compiler is also written in the same language. In this case, the user is already familiar with the language's notation and capabilities, making the task easier than learning an entirely new language. Even so, their changes still occur at a separate level from their ordinary use of the system.

In this context of programming, the separation between the product and the source is particularly lamentable, as it makes it very hard for programmers to improve their tools^[This is true in a global sense, but even more important in the sense of local adaptations for their own purposes.]. If the language ecosystem is not created using itself, a programmer's mastery of the language is worthless for adapting it. Even if they get lucky as described, the burden of getting the source code, recompiling and deploying it, and maintaining a fork would prevent many from succeeding.

An alternative to this arrangement is *self-sustainable* systems which dissolve the distinction between the product, source, and producer. A single environment provides not only for using and re-programming the *product*, but also for re-programming the *producer*, \ie{} the system itself that is used for the programming. These systems are carefully designed to avoid "baking in" any of their behaviour. Instead, they expose as much of their own implementation as possible for modification at the user level. The advantage of such an approach is that innovation and improvement in the system can feed back into its own development.

For example, consider mathematical notation. It involves many font styles and symbols as infix operators. Yet to express this in code we are limited to ASCII characters and, in many languages, prefix functional notation for custom operators. There are numerous domains where an improvement to this notation would make code easier to follow, such as rendering 3D graphics or computing text layout. If we implement a user interface for entering expressions in mathematical notation, not only would it help us program an end-user application such as a 3D game, but the same notation also becomes instantly available for our own use in-system. We could re-express parts of the code for the system's own user interface, such as its algorithms for text layout. In fact, we could even use our mathematical notation to re-implement the very user interface for the mathematical notation! This would not be the case if the end-user code existed in a different world than the programming system.

This "innovation feedback" encourages a virtuous cycle of improvement regarding notations and beyond. The same can happen when we develop better debugging and testing tools, user interface builders, provenance tracking or performance optimisations. In other words, the development of the system itself will benefit from any improvement made while using the system. This is the key advantage self-sustainable systems have over others.
}
