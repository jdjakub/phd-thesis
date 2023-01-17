\chapter{Introduction}

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

When we have an idea for some computer software, and try and make this idea a reality, we are forced to confront two types of complexity: the *essential* and the *accidental*. We realise there's no such thing as a free lunch, so we're able to accept the burden of whatever complexity is actually intrinsic to our idea. If we have a simple idea, we are prepared to do a little work; if it is more ambitious, we will accept having to do more work. Yet this *essential* complexity is often swamped by unwelcome incursions of tedious busy-work. Concepts that appear simple to us must be spelled out in great detail for a computer. This is the *accidental complexity* that is widespread in programming.

This is particularly egregious when the "idea" is merely to change or fix some small issue. Suppose we're using an app, but the text is too small for us to read. Worse, the designers have not included a feature for increasing the text size (perhaps it is a special message in a separate part of the app which is unaffected by the normal controls.) As programmers, we know that there is some API being called to render the text, and this API will be told the font size via some number in the app's memory. If we could just find this single number and change it, we might at least be able to read the text (even if the app is not prepared to lay out the larger text correctly in response to such a "surgical" intervention.)

So, what does it take to find and change a number? Immediately, the app itself provides no way to scratch through the surface to any sort of internals. This means we must face at least the accidental complexity of working with some external tool that can crack it open. We could attach an assembly-level debugger to the app process and stare at hex dumps for a long time, eventually figuring out which address holds the font size. Obviously, this is an expert task that would take an extremely long time even for someone with the relevant experience. It does let us make a change to the running app, at least.

Alternatively, we could hope that the app is open-source, download the code, setup the build system, locate the relevant code, re-build the app, and re-install it. Each of these steps is also an expert task which would be incredibly lengthy, even for programmers, on a novel codebase. Furthermore, this approach entails destroying the running instance of the app and re-initialising it, possibly losing unsaved work.

In the worst case, both of these approaches could be blocked; run-time tampering could be prevented by security policy (especially on a mobile device) and re-building from source fails without access *to* the source. Suffice to say, none of this is suitable for an average user. Even a seasoned programmer would consider it not worth the bother. Our task of changing a number, while technically possible in principle, has a punishingly *disproportionate* accidental complexity cost.

It may well be the case that in this or an analogous situation, the app's authors have a good reason to restrict access like this. There is good rationale for forbidding this sort of tinkering in a game, so as to prevent cheating. Unfortunately, we are not so lucky as to have a *choice* in the matter---this is the only software we know how to make. Even if *we* wrote the app and desire to support adaptation beyond what we anticipated, it is unclear how to do this. We are resigned to the fact that our tools can only output *hermetically sealed* software. The task of "supporting unanticipated modification" is itself a *feature* that we must somehow figure out and implement on top, and it is unclear how to achieve such a feature. Nevertheless, it is worth striving for a world where this accidental complexity is as reduced as possible. This involves a mix of *removing* barriers that we are forced to work around, and *constructing* tools that help us work more effectively.

Imagine a world where the average computer user can patch or improve their software the same way they might change a lightbulb or perform DIY in their home. This clearly relies on the ability to make small *piecemeal* changes to their home, without having to demolish the place and re-build it anew. Let's call this *naïve pokeability*:

> A change has *naïve pokeability* if it is possible to make the change while the software is *running* without having to consider the implications of restarting it.

It would be absurd if, when they go to bed at night and wake up the next morning, the house magically reset to the way it was before. The common-sense expectation is that the changes *persist* into the future:

> The result of a change is *persistent* if it remains until the next change. It is *transient* if it is doomed to be undone after a short timeframe, or if special measures need to be taken to avoid this.

Furthermore, the tools for carrying out the repairs are of an appropriate scale. They can thus reside within the house itself in a familiar place and always be available. Changes are *self-supplied* and, if this covers all possible changes, we have a *self-sustainable* environment.

> A change is *self-supplied* by the software if you can achieve the change by only using the software.
> A software system is *self-sustainable* if arbitrary changes to it are self-supplied.

The house has a workshop where new tools can be fashioned using existing tools as needed. They can be big or small, and this ensures that we can use the "right tool for the job", no matter the scale.

> A *domain-specific adaptation* is a small or large part of a software system which provides its own custom interface for change.

Traditional programming largely works as a progress of bulk fabrication according to a blueprint. To change something, one must change the blueprint, destroy the program and re-create it anew. This re-fabrication happens by means of a separate tool. There is a limited notion of "use the right tool for the job" in that there are different programming languages. However, languages enforce a single syntax and semantics without permitting *smaller-scale* adaptation, and variation in these respects is restricted to textual notations.

Here's how programming could or should work instead. A software system is *designed* to be adapted and modified by its users. By performing an explicit action (e.g. switching to "edit mode") the user can inspect the visible surface of the application to find the causes of its appearance in the form of code and data. They can also inspect a map of the non-visible implementation of the software's functionality and navigate to the relevant parts. There may be a common programming notation as a default, but where possible, parts of the implementation are presented in local notations or interfaces that are more easily understood. And of course, these interfaces can also be traced to *their* implementations and modified if desired. The user can then change any aspect of the software while it is running, without having to edit an external blueprint and destroy the running instance.

Several pieces of this vision do exist, but not in an integrated whole.

*Web pages, web apps, and browsers.* The *web browser* displays web pages, but also has a powerful set of *developer tools*. This includes the "element inspector" which can be used to edit the web page's underlying elements. For example, an ad can be vanished by locating and deleting its element. Note that the "state" here is mostly visible, since it is directly responsible for visual elements appearing on the page. Some of the state may have no visual effects (e.g. an element's ID attribute) and is thus "hidden" from an ordinary user. However, this state *is* visible from the inspector in the developer tools. From this perspective, all of the "structural" state is *potentially* visible in the web browser---and importantly, it's also editable.

All of this is worth contrasting with the case of the "behavioural" side of a web page involving the JavaScript programming language. Alongside the "structural" state of the web page, there is also the *hidden* state of JavaScript objects. The JavaScript console accepts commands which may read or change this state, but there is nothing like the element inspector for it.^[This may be because the "DOM" is a tree structure while the JavaScript state is a general graph, and it's harder to build an editor for the latter.] What *is* visible in the dev tools is the *source code* of the scripts loaded by the page.

Many changes to the "behavioural" state can be accomplished at the console; for example, updating part of the state to a new object. However, this will not work if the source declares the variable as `const`, and more importantly, this does not work for fine-grained changes to code in general. The main unit of code organisation, the `function`, is an opaque object in the runtime environment; one cannot simply replace a particular line or expression within it. Instead, a complete new definition must be entered into the console to replace it wholesale, yet this can be prohibited by the source just like the redefinition of `const` variables.

In these cases, there is no choice but to edit the source files somehow. The browser might provide for local edits to be made to these files, or else they must be made using a text editor. Regardless, these changes do not take effect until the scripts are reloaded by refreshing the page. Compare this situation with the "structural" state of the web page; we are free to make arbitrarily fine-grained changes using the element inspector, and our changes take effect immediately without having to reset anything. There are, in fact, HTML text files backing the page structure, but they are irrelevant given the inspector tool. In short, the state of a web page has *naïve pokeability* while its dynamic behaviour does not reliably have this property.

Actually, there is one caveat: since the HTML and JavaScript files are the "ground truth", any changes made via the inspector or the console will vanish when the page is closed or refreshed. Only changes to the underlying files are *persistent*, and websites typically do not allow random individuals to change the files on their servers! All this is sad news for our user deleting their ad, as they will have to repeat it each time they access the page (or use sophisticated programmatic middleware, such as an ad-blocking extension, to do this automatically.)

*HyperCard.* Before the Web, "hypertext" was regularly created and distributed by people in the form of HpyerCard stacks. Alan Kay criticised the web for having a browser that doesn't include an *authoring* tool, instantly limiting the *creation* of web pages to people who can code in a text editor. In HyperCard, the viewer and editor exist integrated together. Furthermore, there is an "edit" mode whereby a user can remix content from someone else, even reprogramming the dynamic behaviour.

These aspects of HyperCard's design encouraged a community of producer-consumers for hypertext content. The web's higher cost of authoring led to a lower producer-to-consumer ratio, restricting the kind of medium that it would become. Note that the naïve pokeability of the element inspector does not amount to authoring a web page; such an interface is designed for fine-grained change rather than coarse-grained creation. It is also oriented towards programmers, being part of the "developer tools", compared to HyperCard's presentation of authoring as a primary use of the software.

*Smalltalk and COLA.* Smalltalk provides for behaviour editing at a finer granularity than the Web developer tools. Behaviour is separated first by class and then by method; only then is a text editor presented for the code. More importantly, changes to this code take effect once committed, with no "restarting" of the system needing to take place. The state of the system is persisted by default as an "image". In short, Smalltalk provides persistent naïve pokeability for both code and data.

That being said, Smalltalk systems tend to run on VMs that are implemented in a separate lower-level language like C++. Fundamental infrastructure such as object layout and memory management is available only as opaque primitives from the point of view of Smalltalk. Thus, to change these aspects one must still switch to a different programming system and re-compile.

The COLA architecture makes this basic infrastructure self-supplied so as to approximate a truly self-sustainable system. It is also designed to encourage domain-specific adaptations down to a small scale of "mood-specific languages" beyond the coarse-grained variation found with ordinary programming languages. However, the architecture as described does not have much to say about the user interface or graphics,  taking place instead in the world of batch-mode transformations of streams.

The situation is that we can pick at most two from:

1. GUIs with the potential for domain-specific graphical adaptation (Web, Smalltalk)
2. Reliable, persistent naïve pokeability of state and behaviour (Smalltalk, COLA)
3. Full self-sustainability with domain-specific languages (COLA)

Why has a synthesis of all three not been achieved? Part of the difficulty is that programming is framed in terms of *languages* with a focus on parsed syntax and batch-mode transformations. This makes it an uphill battle to achieve even one of these three properties. 

It’s unfortunate that we conflate “coding” in a programming language with programming itself. It makes it hard to talk about the more general thing that contains alternative possibilities. Truth be told, *programming* is the ill-defined act of making a computer do things by itself. Coding, visual programming, programming by example and deep learning are some specific *means* by which to program.

If we see programming as coding, then we unwittingly limit the scope of innovative ideas.

* Instead of seeking the right notation, interface or representation for the job---we seek the right *textual syntax* for the job. If we can't find one, the real reason may simply be that text is not well-suited to the job! Yet if text is all we know, it looks like an intrinsically hard job.
* Instead of being able to make changes to a running program, we are stuck changing its blueprint and re-creating it. It is easy to make hermetically-sealed programs this way and hard to make open ones. 
* Instead of seeking a software *system* open to unanticipated changes as it runs, we might seek intricate *language* features that give flexibility only for *compiling* a program.

This last point is the crux of the matter: we need a more general programming *systems* approach instead. In this thesis, I will discuss this approach in great detail and make an initial offering of a systematic framework by which to analyse programming systems. I will then present my contribution towards the vision of open, malleable software: a prototype programming system called BootstrapLab. It constitutes a fusion of the three desirable properties above, and as far as I am aware is the first attempt to do this.
