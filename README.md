# The Infinitely Printable Maze

Tired of drawing mazes by hand? Wouldn't you rather have a [single printable file](infinitely_printable_maze.ps) that is magically printed as a new and randomly generated maze every time you send it to your printer? If you are a fervent supporter of printable mazes like [Kicks Condor](https://www.kickscondor.com/) or just dabble in the study of printable mazes, the _Infinitely Printable Maze_ is all you ever wanted in terms of printable mazes in a single self-contained PostScript file that doubles as a quine.

## Wait, what is this?

- a PostScript file that randomly generates a new maze every time it is printed (or opened)
- a [quine](https://en.wikipedia.org/wiki/Quine_(computing)), containing and printing its own source code
- an attempt to fit as many printable maze magic into roughly 80 lines of 80 characters of PostScript
- inspired by the FBI takeover of [Kicks Condor's](https://www.kickscondor.com/) national treasure of a blog

## How does it look like?

Every maze is randomly generated, but might look like the following (see also the [example pdf](infinitely_printable_maze.pdf)):

<img width="500" alt="Infinitely Printable Maze as PDF" src="https://user-images.githubusercontent.com/358580/127328785-03e20a1d-6db0-4d33-a704-50c1eab425a0.png">

## A quine?

Yeah, you can copy the content of the displayed PostScript file (you might have to convert it to a PDF first to get your viewer to extract any text from it) and then paste it as a `.ps` to get a file that contains exactly the same code as the original PostScript file. In other words, the original file contains not just the code required to draw a randomly generated maze, but also contains code to display its own code (including the code to display its own code). The formatting only matters for the short footer that contains the bootstrap sequence for the whole quine, while the bulk of the code is printed inside the maze and is whitespace-insensitive (see the [unformatted file](infinitely_printable_maze_unformatted.ps) for a reformatted example that contains exactly the same code as the more cleanly formatted [infinitely_printable_maze.ps](infinitely_printable_maze.ps)).

In a nutshell, the quine pushes several strings as data onto the stack and then proceeds with a bootstrap sequence:

First of all, `dup cvx exec 5 array astore dup {cvx exec} map quine` is executed, which begins by executing the last of the pushed strings (`(/map {mark 3 1 roll forall counttomark array astore exch pop} def)`), which defines a `map` operator (to map another operator over an array) and uses it to execute the other pushed strings (by mapping `{cvx exec}` over these strings):

```
(/! {def} def /~ {exch}! /? {~ !}! /+ {add}! /$ {putinterval}! /len {length}!)
(/str {/s ? /i ? /pre ? s i pre $ s i pre len + ( ) $ /i i pre len + 1 + !}!)
(/rep {/t ? /s ? /i 0 ! s {t search {i s str pop} {pop s exit} ifelse} loop}!)
(/quine {pop ~ <3b> rep <0d> rep <0a> rep dup cvx exec}!)
```

These strings contain a few redefinitions of basic PostScript operators (first line) and then define the new operators `str` (a helper operator for `rep`), `rep` (a string replace operator) and the main operator `quine` (which first transforms the bulk of the code using `rep` into a valid PostScript source and then executes it). All of the lines are duplicated and pushed back onto the stack before being executed, so that after the transformation of the bulk of the code by the `quine` operator the program is executed with all of the code (except the bootstrap line `dup cvx exec 5 array astore dup {cvx exec} map quine`) already pushed onto the stack, which allows the program to read and display it later on.

## What's with the colors inside the maze?

Each cell is colored from light gray to dark gray depending on when it was drawn by the algorithm, with light gray cells being drawn first. This doesn't serve any purpose, but neither does a PostScript quine in the first place, does it?
