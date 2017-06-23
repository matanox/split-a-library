# __Turning a (clojure) codebase into a Library__

There's a major difference in creating and maintaining a library, v.s. a code base for self or team usage. Whereas technical differences like exposing a safe API and public publishing exist, the key differences lie in making the code one level more usable (as in usability) by a wider audience.

This list starts off with a list of activities needed for splitting up a regular codebase into a library, gradually addressing the larger implications of opening up a codebase as a library.

This guide is clojure-specific, but the underlying concepts may be sufficiently universal.

# Approach and initial, mostly technical, steps to be taken

Firstly make two copies of your codebase on separate directories:

+ `lib`
+ `user`

Then (re)initialize git for `user`, `lib` or both, depending on whether you have any proprietary commit history in your codebase. Obviously, keeping the full git history for `lib` will be handy. The point of doing this first, is only to avoid inadvertently pushing to the same git target from the two diverging code bases `user` and `lib`; You may choose a different workflow.

Now proceed morphing `lib` into a library:

1. Remove any `main` entry point from the library ― you want the library to be used as an API.  
One nice way to go about it, is make a copy of your project directory, naming one of the copies as something like `lib`, the other `user`.
1. Remove any main clause from `project.clj` in `lib`.
2. Extract some of your original codebase to live only in `user`.

Now `user` is going on a path to become either or both:

+ The project you run instead of the original code base.
+ A sample codebase for how to use your library (if you aggressively cleanse & sterilize it.

Since this guide is opinionated around first keeping what currently works for you working, it focuses on the first of these two concerns. If your situation is different, this might be a good point to depart from this guide, only skimming for opportunistic reuse.

So proceeding with keeping everything working the same as before you started off, only separated to a `user` and a `lib`:

1. in `user`, remove all code you're sure to keep only in the `lib` codebase.

1. If the codebase has no clear separation of user-ness v.s. library-ness in its namespacing, make this distinction now by picking some new namespace for your `user` codebase, go ahead and apply it to all `ns`, as well as `require` definitions, in the sources remaining in `user`

1. make sure the `defproject` name in the `project.clj` of `lib` is one that you like for your now-going-to-be library, and require by that name (and version) in the `project.clj` of `user`.

1. Favorably consider using the [local repository linking feature](https://github.com/technomancy/leiningen/blob/master/doc/TUTORIAL.md#checkout-dependencies) of leiningen, if you're going to hack in parallel on both of the two (`user` and `lib`). But you may also suffice with issuing `lein install` on every change to `lib`. Either the way the goal here is to make `user` pick up any change to `lib` when working them both locally.

If you opt for lein's repository linking feature, get the necessary symlink syntax from [the man page](http://man7.org/linux/man-pages/man7/symlink.7.html). It's entirely fine to just use `lein install` every time instead, till longstanding fatigue settles in.


# Beyond the Technicalities
Now you are ready to conjointly optimize the most minimal API facade for your library vis-a-vis keeping your sample project/s in check. This might involve iteratively modifying your library codebase (if you followed above, that's your `lib`) into a cleanly reusable library.

It is possibly easier to iterate this, after you've initially separated your library into two projects like above, yet the order might be flipped on its head as suggested in [aftermath](#aftermath) below.

## Making the Real Leap
When you were writing code to yourself, you did not need the following properties, this is all changing when you're a library:

### introductory explanations for what use cases is your library fit for

You should spend a little time on this, to avoid user frustration. This should optimally appear at (or very near) the top of your __github readme__ ― unless you already have some dedicated website, which you should very probably not yet have ― until your library is mature enough to go "higher profile".

Make to really dwell on what use cases your library is not a good fit for. You should really disambiguate this to avoid frustrating your immediate as well as random audience who stumble on your repo.

Favorably consider adding a "work-in-progress" notice at the very top and/or in github's one-liner project description box, till you feel your library is sufficiently modular and tested by few others, to be fit for use by total strangers.

### API, modularity, naming lingo

Dwell on how usable is your API, how modular are the key parts of your library, and even the names and argument passing conventions of your public functions. Code that simply worked, might need a thorough facelift now that it is serving a more generic role; prior function names might have been domain-specific (if not proprietary) whereas the library you are exposing might deserve more generic lingo, if only to avoid sporting idiosyncratic terminology.

### API, API and API

While last here, this is the most important. Even in the clojure community, nobody wants down-deep error messages when bad input is supplied to your API. Even if in the clojure ecosystem people are more accepting ― or tend towards the down-digging personality ― relative to some other language communities.

I personally believe your API should validate its input. We'll soon have spec on clojure 1.9 for that, and you can _"barehanded"_ at a basic level even if you do not backport clojure.spec until then.

Point being to apply a fair amount of intuition to what might be a good (and mostly library-specific) error message for the salient bad inputs you may expect from strangers using your API.

And while last but not least here, you should seriously dwell on what novel input validations ― one's you may not have needed as a small team ― are important to add which your codebase hadn't sported before. The need may exponentially intensify along with the complexity of the allowed inputs, and/or the implicit constraints expected by your algorithms!

# Aftermath

Technically, you finally publish your library to clojars or Maven. I assume you can google for that.

Arguably, an __alternative path__ might be to start with only taking care of library separation through _a dedicated namespace_ in your project. In that case you might still use the technical workflows above, once you've matured your codebase and ready for the final technical splice :wink:

In that alternative path, you might easily dwell more on library tests ― which is a very great thing!

In which case, you might, if at all, skim this writeup bottom-up

:blush:
