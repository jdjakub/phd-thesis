in html/dom - you can open object browser and edit user interface, but this does not work for editing code in the same way - that is bulk JS with some substrings you would have to edit; what if everything was visible accessible (shareable) and editable, including structured code?

if I want to draw GUI, I can either write code or use GUI editor like VB or Hypercard - but the GUIs exist as separate to the programming system - could they be integrated like DSLs?

third - maybe COLA or Smalltalk - do self-sustainability but not quite.

1. what is your quest - for a non-computer person
2. many pieces of the puzzle exist - web, hypercard, smalltalk
3. why hasn't anyone done this? PL paradigm is wrong - we need systems
4. systems allow better thinking! here is more specifically what I'm working on

#### TP: Is everything in the text for some reason (understandable to the reader)?

When we have an idea for some computer software, and try and make this idea a reality, we are forced to confront two types of complexity: the *essential* and the *accidental*. We realise there's no such thing as a free lunch, so we're able to accept the burden of whatever complexity is actually intrinsic to our idea. If we have a simple idea, we are prepared to do a little work; if it is more ambitious, we will accept having to do more work. Yet this *essential* complexity is often swamped by unwelcome incursions of tedious busy-work. Concepts that appear simple to us must be spelled out in great detail for a computer. This is the *accidental complexity* that is widespread in programming.

#### TP: Emphasize that often the essential complexity is small? (this is in contrast to Brooks)

#### TP: Maybe some accessibility example (font size)? with footnote ref to Antranig's work?

It is worth striving for a world where this accidental complexity is as reduced as possible. This involves a mix of *removing* barriers that we are forced to work around, and *constructing* tools that help us work more effectively. Imagine a world where the average computer user can patch or improve their software the same way they might change a lightbulb or perform DIY in their home. This clearly relies on the ability to make small piecemeal changes to their home, without having to demolish the place and re-build it anew. Furthermore, the tools for carrying out the repairs are of an appropriate scale. They can thus reside within the house itself in a familiar place and always be available.

#### TP: How exactly is this vision connected to reducing accidental complexity?

Programming largely works as a progress of bulk fabrication according to a blueprint. To change something, one must change the blueprint, destroy the program and re-create it anew. This re-fabrication happens by means of a separate tool.

Here's how programming could or should work instead. A software tool is designed to be adapted and modified by its users. By performing an explicit action (e.g. switching to "edit mode") the user can inspect the visible surface of the application to find the causes of its appearance in the form of code and data. They can also inspect a map of the non-visible implementation of the software's functionality and navigate to the relevant parts. There may be a common programming notation as a default, but where possible, parts of the implementation are presented in local notations or interfaces that are more easily understood. And of course, these interfaces can also be traced to *their* implementations and modified if desired. The user can then change any aspect of the software while it is running, without having to edit an external blueprint and destroy the running instance.

#### TP: Not obvious why "while it is running"

Several pieces of this vision do exist, but not in an integrated whole.

#### TP: For each of the following, which point from the above does it link to? (You could even have a bullet list with "design principles D1, D2, D3...")

* D1 - state and logic structurally visible and editable
* D2 - authoring tool is the viewing tool aka the "edit" button
* D3 - domain-appropriate tools? "Personal dynamic media" vision (in theory only?)
  (LISP DSLs may be another example - still LISP but the idea is there)

#### TP: Maybe also immediate feedback?
#### TP: Where does "explicit structure" and "self-sustainability" fit in here?


*Web pages, apps, and browsers.* The *web browser* displays web pages, but also has a powerful set of *developer tools*. This includes the "element inspector" which can be used to edit the web page's underlying elements. For example, an ad can be vanished by locating and deleting its element. Note that the "state" here is mostly visible, since it is directly responsible for visual elements appearing on the page. Some of the state may have no visual effects (e.g. an element's ID attribute) and is thus "hidden" from an ordinary user. However, this state *is* visible from the inspector in the developer tools. From this perspective, all of the "structural" state is *potentially* visible in the web browser---and importantly, it's also editable.

All of this is worth contrasting with the case of the "behavioural" side of a web page involving the JavaScript programming language. Alongside the "structural" state of the web page, there is also the *hidden* state of JavaScript objects. The JavaScript console accepts commands which may read or change this state, but there is nothing like the element inspector for it.^[This may be because the "DOM" is a tree structure while the JavaScript state is a general graph, and it's harder to build an editor for the latter.] What *is* visible in the dev tools is the *source code* of the scripts loaded by the page.

#### TP: I think it is also DOM is more obviously a tree - and it needs to be manipulated by program for existing reasons; JS not so much

#### TP: Could say "this satisfies D1 for structure but not D1 for change"

Many changes to the "behavioural" state can be accomplished at the console; for example, updating part of the state to a new object. However, this will not work if the source declares the variable as `const`, and more importantly, this does not work for fine-grained changes to code in general. The main unit of code organisation, the `function`, is an opaque object in the runtime environment; one cannot simply replace a particular line or expression within it. Instead, a complete new definition must be entered into the console to replace it wholesale, yet this can be prohibited by the source just like the redefinition of `const` variables.

#### TP: Discussion about const is perhaps too technical for intro.

In these cases, there is no choice but to edit the source files somehow. The browser might provide for local edits to be made to these files, or else they must be made using a text editor. Regardless, these changes do not take effect until the scripts are reloaded by refreshing the page. Compare this situation with the "structural" state of the web page; we are free to make arbitrarily fine-grained changes using the element inspector, and our changes take effect immediately without having to reset anything. There are, in fact, HTML text files backing the page structure, but they are irrelevant given the inspector tool.

#### TP: Fun fact - if you click View source in IE5, it opens it in notepad - if it is local, you can edit directly. Not possible today!

Actually, there is one caveat: since the HTML and JavaScript files are the "ground truth", any changes made via the inspector or the console will vanish when the page is closed or refreshed. Only changes to the underlying files are persistent, and websites typically do not allow random individuals to change the files on their servers! All this is sad news for our user deleting their ad, as they will have to repeat it each time they access the page (or use sophisticated programmatic middleware, such as an ad-blocking extension, to do this automatically.)

#### TP: Footnote reference to Webstrates?

*HyperCard.*

#### TP: Satisfies D2 - "edit mode"?

*Smalltalk.*

#### TP: Satisfies D3 - "???"

It is clear that, at present, we can "pick any two" from: ...

Why has this synthesis so far not been achieved? Part of the difficulty is that programming is framed in terms of *languages* with debates about syntax and parsing.
