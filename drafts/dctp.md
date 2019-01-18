---
title: The Misunderstood Roots of FRP Will Save Programming
---

<h1>The Misunderstood Roots of FRP Will Save Programming</h1>

* TOC
{:toc}

## Abstract

[TODO]

## The Curse of FRP

I fell in love with ReactJS in late 2014. The view is a pure function of state. 🤯 It was so obviously *right*.

But of course it wasn't perfect. For one, it wasn't clear how that state changed over time. Redux seemed to make sense -- and hot reloading and time travel debugging are awesome -- but I over time I began to sense that React didn't solve all my problems. I eagerly slurped up each new React-inspired frameworks, such as VueJS and CycleJS, to see if they could finally be the "full solution" to interface development, but always felt something was missing.

[Paul Chiusano](https://pchiusano.github.io/) suggested I read [Conal Elliott](http://conal.net). "You know," he said, "Conal is the original guy behind FRP. React isn't even 'real' FRP. You should go back and read him."

I didn't like to hear this. I loved React and the FRP I knew. I was reluctant to read [a stodgy paper from the 90s](http://conal.net/papers/icfp97/). But I trusted and respected Paul so I gave it a go.

My eyes immediately glazed over. I wasn't able to make sense of almost any part of it, so I gave up.

However, I couldn't escape HN comments alluding to [real](https://hn.algolia.com/?sort=byPopularity&prefix&page=0&dateRange=all&type=comment&query=real%20frp) or [original](https://hn.algolia.com/?sort=byPopularity&prefix&page=0&dateRange=all&type=comment&query=original%20frp) FRP.

It was annoying enough to send me back to Conal for another try. I wanted to show these pompous pricks that their precious old FRP was *worse* than my modern FRP. 

Yet I couldn't make heads of Conal, even [these](https://www.youtube.com/watch?v=j3Q32brCUAI) [two](https://www.youtube.com/watch?v=teRC_Lf61Gw) videos, so I gave up again. Over the course of a year, I tried on-and-off again to read Conal's papers, but they never made sense. And so I decided to give it up for nonsense. There was no there there.

And then someone suggested I listen to [Conal's interview on the Haskellcast podcast](http://www.haskellcast.com/episode/009-conal-elliott-on-frp-and-denotational-design) and things started to click. I went back to his papers one last time and BANG. It hit me. It all fell into place. I saw the light.

I saw how the beautify of React could be joined with a solution to state management without the annoying parts of Redux. I saw how we could program user interfaces without mutable variables anywhere, just `const` statements as far as the eye could see... 

And now I am cursed to be one of those condescending assholes, a Conal zombie, doomed to roam the land, pleading other to "go back and read Conal", while knowing full well how hard it will be for them to see the light. This essay is how I hope to give you my disease.

## The Curse of Coining a Programming Paradigm

When a programming language designer defines a word in their language, that's the end of the story. But when a programming language designer coins a phrase in English, that's only the beginning of their ordeal. Alan Kay coined the phrase "object-oriented programming" (OOP) in the 70s, but OOP has taken on a life of its own, [which has caused much confusion and heartache to its creator](http://wiki.c2.com/?AlanKaysDefinitionOfObjectOriented). 

Functional reactive programming (FRP) has [a similarly confused and contested etymology](https://medium.com/@andrestaltz/why-i-cannot-say-frp-but-i-just-did-d5ffaa23973b). In the 90s, Conal Elliott pioneered a new software paradigm for programming interactive animations and dubbed it Functional Reactive Programming (FRP). Like Kay, Elliot then watched others use his own term to describe things totally opposed to his original vision. While he initially fought to maintain the integrity of his phrase, Conal eventually conceded defeat. Like OOP, FRP now is a bastardized term that refers to work *inspired* by the "original FRP." Conal has retreated to coining a new, less-sexy (maybe on purpose?) phrase to describe his original vision: Denotative Continuous Time Programming (DCTP). From here on out I will use DCTP to refer to "original FRP".

## Continuity

The [inspiration for DCTP came in the form of a question from John Reynolds](http://conal.net/blog/posts/early-inspirations-and-new-directions-in-functional-reactive-programming). It roughly amounted to: 

What would programming look like if time were continuous?

This is a wonderful question, but it's hard to answer it directly, partly because time is hard to visualize. Let's come at this via a slightly different question and then circle back to time:

What's the difference between bitmap graphics and SVG? 

### Continuous Space

In layman's terms: if you zoom in a bitmap graphic, it will get all pixelated. But if you zoom into an SVG, it will remain crystal clear. 

This is because a circle in a bitmap graphic is just a collection of points, so if you zoom in, the computer has no choice but to "blow up" each pixel. However, if you add a circle in an SVG, the computer always remembers it's a circle and thus will be able to show it at whatever resolution you want. That's why we say an SVG is "resolution-independent".

SVG graphics retains all the relevant info about a graphic until the last moment when the computer finally has to put the picture on the screen; then it turns your circle into a bunch of pixels.  On the other hand, a bitmap is a bag of pixels always, and thus when you want to zoom in, it's stuck with too little information (it doesn't know those pixels started out as a circle) to give you what you want. It's gone to pixels "too early".

#### What is a graphic?

Let's get a bit more mathematical with our definitions. A graphic is something that relates x- and y- coordinates to colors. You give it an x-y pair and it'll give you a color. 

Here's the difference between bitmaps and SVGs: what kind of numbers make up the x-y pairs? 

With bitmaps, it's a pair of integers `(Int, Int) -> Color`, while with SVGs it's a pair of real numbers `(Real, Real) -> Color`.

A bitmap is a graphic over discrete space. This means that you can count the pixels one by one. There are no pixels in between two adjacent pixels. This is why we have trouble zooming in - we must create more pixels in between where no pixels yet exist.

An SVG is a graphic over continuous space. Between any two points in an SVG there are an INFINITY of points. It wouldn't even make sense to discuss adjacent points, because you could always find more points in between any two points. This is why SVGs scale so well. SVG graphics know the values of infinitely many points, so they are infinitely flexible.

If you're not used to dealing with the infinite every day, you may wonder how we can store an infinity of points. It's easy: just use functions.

Here's a very simple graphic that describes an infinity of points. For all points, the color is black: `(x, y) -> Black`. Boom. Infinity conquered. 

[todo pic]

Here's another: a green circle with radius 1, centered at (0,0): `(x, y) -> x^2 + y^2 < 1 ? Green : Clear`.

[todo pic]

### Continuous Time

In 99.99% of programming, time is discrete. That is to say that time can be represented by integers. We can count each point of time one-by-one, and there are no points in time in between adjacent points. For example, JavaScript's `requestAnimationFrame` has discrete time ticking along at about 60 times per second.

Now to return to the original question that inspired FRP: can we make time continuous?

#### What is an animation?

An animation is a graphic that changes over time: `(x, y, t) -> Color`. But again, what kinds of numbers are `x`, `y` and `t`? If they are integers, we're in discrete space and time. Let's make them all reals and enter continuous space and time.

We already know about discrete space. So how could we extend our circle from above with animation in mind? 

Let's have him drift off the screen to the right: `(x, y, t) -> (x, y) -> (x-t)^2 + y^2 < 1 ? Green : Clear`.  Or we can simplify this math:

```haskell
circle centerX centerY radius color = (x, y) -> (x - centerX)^2 + (y - centerY)^2 < radius^2 ? color : Clear

rightDriftCircle t = circle t 0 1 Green
```

[todo pic]

Let's have our circle move in a circle: `circle cos(t) sin(t) 1 Green`

#### Why Program With Continuous Time?

Of course, for the computer to render the above graphics on the screen, it must at some point convert the continuous time and space to discretized pixels and ticks of time. So what's the point of programming in continuous time if it will eventually be discretized? 

There are a [lot](https://github.com/conal/talk-2014-bayhac-denotational-design#why-continuous-time-matters) of [reasons](http://conal.net/blog/posts/why-program-with-continuous-time).  The most compelling argument for me is for the same reason we use SVGs: resolution independence. We want to be able to transform our programs "in time and space with ease and without propagating and amplifying sampling artifacts." We want to discretize at the last possible moment, and stay as abstract as possible for as long as possible.

Explain why continuous time (and laziness) are key for compositionality and modularity.

### Denotational Semantics

So know you know the CT of DCTP: continuous time. But what of the Denotative D?

We've already been doing it. The original name for denotational semantics was "mathematical semantics". It was pioneered by Chris Strachey and Dana Scott in the early 1970s. It is an approach to model a programming language with mathematical objects. So above, when we modeled a graphic as a function from x and y, that was denotational semantics.

In his 1977 Turing Award Lecture, [Can Programming Be Liberated from the von Neumann Style? A functional style and its algebra of programs](http://www.thocp.net/biographies/papers/backus_turingaward_lecture.pdf), John Backus explains how denotational semantics uncovers the hidden complexities in imperative, von-Neumann-based languages:

> When applied to a von Neumann language ... the complexity of the language is mirrored in the complexity of the description, which is a bewildering collection of productions, domains, functions, and equations that is only slightly more helpful in proving facts about programs than the reference manual of the language...

In other words, most programming languages lack powerful mathematical properties. For example, the lack of equational reasoning (being able to replace expressions with equal ones) it makes it difficult to reason about such programs, let alone *prove* things about them. Additionally, lacking in mathematical properties prevents composability and modularity of programs.

You can learn more about denotational design and semantics from Conal's [video](https://www.youtube.com/watch?v=bmKYiUOEo2A) and [paper](http://conal.net/papers/type-class-morphisms/).

### Flows

Let's get to the coding already! Enough metaphors and semantics!

There are two main types in DCTP: Behaviors and Events. I will refer to them both as flows because they both resembles timeline-y things.

Behaviors are continuous functions of time: 

* the x-y-position of your mouse
* the constant function `t -> 1`
* the Boolean valued function: `t -> t > 10`

Events are discrete occurrences in time:

* the click event of your mouse
* key press events of your keyboard
* an interval of 3 seconds

#### Flow combinators

[TODO turbine]

#### Higher-Order Flows

It's very often useful for a flow to contain other flow:

[TODO examples]

However, it can be unwieldy to work with such flows. Usually, one manages by collapsing them down to more manageable types, such as via the following chart:

[TODO chart]

#### Cyclical/Recursive Flows

It's also often useful to have cyclically/recursively defined flows:

[TODO examples]

Denotationally, cyclical or recursive flows are very similar to recursive functions, which are semantically a fixpoint. This is a very confusing point I barely understand myself. Suffice it to say that this magic works for sound mathematical reasons.

#### Performance

It's well known that FRP has suffered space and time leaks. People often complain about the performance of mathematical or abstract language like Haskell. 

[asked conal for help here]


## "A denotationally simple model for whole systems"

Now that you understand DCTP, you are ready to see why I think it can save programming. 

Isn't Excel wonderful? One of the reasons that allows Excel to be so wonderful is that it's entirely free from control flow. There's no sequencing of instructions. There's only data flow, which is directed via mathematical expressions.

In [The Next 700 Programming Languages](https://www.cs.cmu.edu/~crary/819-f09/Landin66.pdf), Peter Landin calls such languages that only ... "expression languages."

http://conal.net/blog/posts/can-functional-programming-be-liberated-from-the-von-neumann-paradigm

### Monads aren't the answer

### Expressions only

### FRP everywhere...

#### HTTP = JQuery

## Common gotchas

* DAG or graphs
* mutation
* discrete time

## Further reading

* http://vindum.io/blog/lets-reinvent-frp/
* https://github.com/conal/talk-2015-essence-and-origins-of-frp
* https://github.com/conal/talk-2014-bayhac-denotational-design#why-continuous-time-matters


## Notes

https://stackoverflow.com/questions/5875929/specification-for-a-functional-reactive-programming-language/5878525

https://stackoverflow.com/questions/5385377/the-difference-between-reactive-and-functional-reactive-programming/5386908#5386908

https://stackoverflow.com/questions/1028250/what-is-functional-reactive-programming/1030631#1030631

https://futureofcoding.org/notes/conal-elliott/


