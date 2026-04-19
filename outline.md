# Cursed Code Comedy Outline

Joke fillers:

* quip - 1 liner, must take minimal time
* bit - Performance of funny situation
* joke - 3 part set up, subversion, punchline. Can be split out over the course.

1. Intro - 3 min target
   1. We are - 2m preferred (This is a lot to get through so we'll need to minimize it)
      1. Piper
         1. quip
      2. Jamie
         1. quip
      3. As a Team
         1. ppb
         2. Teahouse Hosting
         3. Python's poly power partners
   2. Setup 1m
      1. Good ideas vs bad ideas
         * Most of this could be a good idea, if you have the right problem
            * And the right people around you to not say no
         * Joke?: Setuptools [2006] vs Setuptools [2026]
      2. consider setting up a meta joke here. (talk about the maturity of the python tooling space?)
         * Type hinting
2. The Set
   1. Bags 2-3 min
      1. What we mean by Bags
         1. joke about JS here "idea so good all of js runs on it"
            * "oh, so js got their bag" "yes, but not financially"
      2. The implementation
         1. "If the implementation is easy to explain"
         2. Explanation
      3. The problems
         1. The inversion of our tooling joke starts here. We should mention the problem, but not call attention to it.
         2. "It might be a bad idea."
         * "Type hinting is just a conspiracy by big type to sell more type, just like uppercase"
   2. Abstractions - 1m or less
      1. self -> my
         * "With PPB, we wanted to help people getting into Python for game development to stop thinking of themselves in such dissociated terms, so we moved from self to my, which will help them take ownership of their code."
      2. Alternate timeline of ppb (Sets, Actors, Stage, Crew)
         1. Space for quips
         * Outrageous Fortune project with Sling and Arrow actors
      * "Probably best it went the way of Yorick"
   3. Magic Field Descriptors - 6m
      1. ppb details - 1m
         1. inheritance
         2. some calculated properties
      2. The Problem to Solve - 1m
      3. The Solution - ?m
         1. Rushed explanation bit
            * "Imma show the code, but it's... you'll see"
            * Progressively faster and more code-per-slide
            * interruption
            * "but i have 4 more slides"
            * 4 perfect slides
         2. Reference the simplicity of bags
         3. Rushed explanation bit
      4. The Solution's Problems 1m
         1. So this was a lot
         2. Departure from the data model
         3. Education problems
         4. Affects class declarations
         5. Also, the tooling (start to rub it in) segue to the auto import madness
   4. Autoload 5m
      1. The Problem to Solve
      2. The Solution
      3. The Problems
         1. The layers of deep magic
         2. The "simple" ways to break it
         3. C Python is like your overly strict mother who won't let you drive the car.
   5. CLI from a Struct - 5m
      1. "So working on dams and water infrastructure--wait, no, this isn't excavacon"
         * "Yeah, but wine country is right there, i bet there's something like cava"
      2. The Problem
         1. Argparse is great
         2. It has a Bag of a kind (simple namespace)
         3. Complicated
         4. And tooling is only so-so
      3. The Solution?
         1. It started as a shitpost (https://gist.github.com/pathunstrom/986592a078c3208514797b6a8c7aabf0)
            1. click joke?
         2. Structs and type hints can hold a lot of information
         3. But there's lots of edge cases
            * "oops all edges"
            * Enough edge cases to make a graph theorist cry.
      4. The Problems?
         1. It's hard to design an API for complicated clis
            * API design is a lot like scaling a web service: you don't know how you got it wrong until it's too late.  - aurynn
         2. The implementation is reasonable?
         3. The tooling? (this is the payoff to the "tooling hates us" joke)
      5. Actually Published!
         * logo & link
3. Outro - 2m
   1. Reach us
   2. Use our code
   3. Try our service
   4. Hire Us
