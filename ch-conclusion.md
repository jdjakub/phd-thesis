\chapter{Review and Conclusion}

By carefully following the development of a specific programming system, BootstrapLab, we have uncovered the basic steps involved in creating a self-sustainable system.

# Limitations and Future Work

## Improving the Technical Dimensions

We will aim at *conceptual* clarity, putting practical issues to one side and asking what is *really* going on with the dimensions, even if our answer is practically infeasible to work with. Then we will add the practical concerns back in. In the end, future work consists both of improving the theoretical concepts *and* developing practical methods of using them.

### Scoping The Dimensions
Even if some property fails to hold for an entire programming system, it may well still hold for some part of the system. For example, take our *persistence effort* dimension from Section\ \ref{dimensions-constituting-self-sustainability} and imagine a Smalltalk-like system where everything is automatically persisted except for a single, special global called `x`. It would be unhelpful to characterise this system as having infinite persistence effort simply because it is technically impossible to persist the entire state. Informally, we see that it has *mostly* minimal persistence effort with the sole exception of `x`, for which it is infinite. This example was deliberately extreme, but a more realistic one is the Web platform whose JavaScript stack cannot be referenced or traversed by JavaScript code.

The true scope of a dimension like "persistence effort" is more of a *field* in the physical sense, defined at every atomic piece of state in the system (the *field points*). Similarly, "custom syntax effort" from Section\ ref{dimensions-constituting-notational-freedom} is defined for individual syntaxes that a user may wish to use in the system, whether they already exist or merely potentially could exist. This highlights the additional difficulty that we may wish to characterise a system not only by how it happens to be right now, but by how it would perform across many *potential* use cases. We mentioned the complexity of considering "actual" vs. "potential" field points when defining *string wrangling effort* in Section\ \ref{dimensions-constituting-explicit-structure}; this dimension properly applies per "situation" consisting of a specific user, programming against a specific string format, in a specific program. These terms (user, string format, program created with the programming system) all invite further definition.

Making all this more precise, and establishing the minimal scope of the other dimensions we have proposed, is an open problem. As the next best option, we gave scores for BootstrapLab as a whole in Section \ref{Evaluation} while elaborating on any relevant complications in prose.

### Aggregation Functions and Weights
Having realised that the dimensions apply as more of a "field", we could recover the simpler coarser-level score that we want as some sort of *aggregation* of the finer-level scores. We mentioned this in Section\ \ref{aggregation-and-simplification} to pre-empt any concerns about our simplified approach to the dimensions: in the absence of a rigorous treatment of the "scoping problem" just discussed, we were forced to do the aggregation *intuitively* based on our understanding of BootstrapLab.

Future work would consider what the aggregation function should be: a simple addition (even an integral) over scores, an average, or something else? Where *potential* field points (users, programs, notations, etc) are concerned, the infinite possibilities mean some sort of weighted aggregation would be necessary. We might compress the infinite range into a finite number of categories, one of which is a catch-all "other" category, and assign a weight for the probability or relevance of each category. This introduces a further question of how these weights are established or justified; intuitively, we know that programming systems have strengths and weaknesses and are built to cater to different problem domains or types of user, but how could this be made more rigorous? We must leave this, and the full development of the other ideas we have sketched here, as open questions.

### Defining Quantitative Measures or Resolution Criteria
So far, our concerns have been conceptual; we have been talking about hypothetical "scores" and "weights". In our evaluation of BootstrapLab, we remained at this "almost-quantitative" level: we scored using the terms "minimal", "moderate", and so on (or "present" / "absent" for boolean criteria). We justified these scores by means of argument and intuition. This is suboptimal from a research perspective; a fully developed dimensions framework should enable researchers to agree or at least productively disagree (perhaps leading to new dimensions or definitions on which they *do* agree).

One improvement would be to further define *resolution criteria* for our score terms to the point that two parties, following the same definitions, would independently converge on the same scores. Alternatively, we might pursue *real* quantitative scores with concrete numbers. The challenge here would be ensuring the relevance 

### Actually measuring the quantities / obtaining consensus

### The Circumscription Problem of Systems

## Improving BootstrapLab

* Bootstrap on other plafs
* Substrate capabilities
* Paying off debts
* Brittle / fragile / versioning
* Bring insights from COLA / MPS / Eco to reduce NF efforts

# Future Work
\tomas{Possibly draw a diagram of what are all the things that have to match? Like code-data in substrate, substrate-highLevelLanguage etc.}

The final two steps of the journey described in Sections\ \ref{pay-off-outstanding-substrate-debt}--\ref{provide-for-domain-specific-notations} constitute our own future work. There is, however, plenty of opportunity for follow-up work in the spirit of this chapter and the project it has documented. At every step of the journey, there were choice points where we naturally could only move forward with one of the options. The obvious task for interested parties would be to explore the other branches. We can't provide an exhaustive listing here, but will give some important examples.

At the first step (Choose A Platform), all sorts of other platforms could be chosen. While COLA built on top of one "slice" of Unix---files, build tools and process memory---we see another possibility in focusing on the hierarchical *file system* as a state model to inherit through to a substrate. This is one obvious *structured* substrate lurking within Unix and some of our work here is no doubt applicable to it: directories act as maps, filenames as keys and file contents as values. Symlinks could add graph structure to this tree where needed. Similar ideas can found in \parencite{Hull}.

We acknowledge that it might feel perverse to have files contain "primitive" values, such as a single number, or to represent instructions as directory trees, since files are normally used as "large" objects. However, it must be noted that there is precedent for using them more generally for data large and small, such as in Plan 9 and `procfs`. If this was still too much to stomach, the default option for "code", \ie{} shell scripts, could simply be inherited (with the caveat that this would impose a text dependency at the core of the system). What is most unclear is how graphics would be displayed and interacted with---possibly requiring a special binary as part of the substrate, for opening and synchronising a main window.

If we accept our chosen web-based platform, we can consider alternative substrates. One obvious possibility is inheriting the DOM as the state model. This is the choice made by Webstrates\ \parencite{Webstrates}, which stores textual JavaScript code for programmatic change. Following our approach, we might want a lower-level and structured instruction set instead. This would, at the very least, need to be capable of changing parent/child/sibling relationships, node attributes, and inner textual content. One warning is that the rest of the DOM API that would need to be exposed, in order to be able to produce a functional modern web page or web app, is somewhat daunting in scope. It would also be necessary to have some way of listening for changes to DOM nodes so that any constraints can be maintained or dependencies can be updated. Webstrates does provide synchronisation between networked clients on the same page, so perhaps its methods could be adapted.

As mentioned, we would also recommend going for an instruction set that is convenient enough to *use* that immediately building programs in-system is a worthwhile endeavour. Our own wild adventure in minimality was a mistake in this regard, causing us to stay in JavaScript, implement the high-level language there and port it later. It would be interesting to see the process of gradually building each component of a high-level language engine interactively in-system. Out of the four possibilities in Section\ \ref{choosing-an-appropriate-implementation}, we chose the *platform interpreter*, so exploring the others would be illuminating---particularly the *platform compiler*, which could self-host relatively quickly.

Finally, it would certainly be interesting to forego any temporary infrastructure at all, or build up entirely in-system without using platform tools. This would require more careful substrate design to get this process going effectively. While this could give some insight or appreciation for the hardships of early computing, its practical value in the modern environment is unclear and may be best considered a challenge for hacker wizardry.

# Conclusion
