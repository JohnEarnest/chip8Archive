# MINI LIGHTS OUT

This small puzzle game consists of a 4x4 grid of lights that are either turned on or off in a random pattern.  Your goal is to turn off all the lights.

## Instructions

If you're lucky enough to have a Touch Screen board, you can simply press a light on your screen to toggle it.

If you're using an ASCII keyboard rather than a COSMAC VIP hexadecimal keyboard, the following keys are used for the corresponding lights:

```
1 2 3 4
Q W E R
A S D F
Z X C V
```

Pressing a light turns it off, but it also toggles all four adjacent lights above, below, and to the left and right.  If one of these adjacent lights were on, it is turned off, but if it were off already, it gets turned on again.  This adjacency "wraps" around the edges, as if the grid were in fact a torus, meaning that pressing a light always affects five lights.

Note that you're only allowed to press lights that are turned on.

## Tips

This game uses Octo's "screen flash" effect. If you wish to play this game on a COSMAC VIP or another system that has an audible buzzer, you probably want to change address `0204` from `F018` to `8000`.

If you find the game too difficult, you can remove the restriction that disallows pressing lights that are turned off.  To do so, simply jump over this check by changing address `0384` from `A3E1` to `1394`

This modified version of the game is very simple once you learn the following trick.  Please skip this if you don’t want any hints, including for the unmodified version of the game.

1. Press one of the lights to turn it off
2. Press each of that light’s adjacent buttons, in succession (regardless of whether its light is on or off)
3. Repeat for the next light

You can use this strategy on the main, unmodified game as well, although step 2 obviously needs to be amended, since you're not necessarily able to press all the adjacent buttons at this stage.  This amendment is left as an exercise to the player.

## HIstory

In 1977, RCA released the CHIP-8 interpreted programming language for its COSMAC VIP computer. This language made it simple for enthusiasts to create their own computer games. Programs were often distributed as code listings in the community-run magazine [VIPER](http://mattmik.com/retro.html).

In 1995, Tiger Toys released a physical game toy called Lights Out.  This was played on a 5x5 grid, and the lights did not "wrap around" the edges as it does in this version -- ie. the light in the top left corner only toggled itself, the light below it and the light to the right of it.

Several variations of this game were released over the years, including [Mini Lights Out](https://www.jaapsch.net/puzzles/lights.htm#descmini), the version in this game.  The fact that the lights wrap around in this version means that all patterns are solvable, which was not the case for the original Lights Out.  This allows for a procedurally generated sequence of patterns, so you can have nearly endless games to play.  Well, 65,535 of them at least.

In 2014, John Earnest created [Octo](http://johnearnest.github.io/Octo/), an IDE, emulator and high-level assembly language that made it easy to make CHIP-8 games.
