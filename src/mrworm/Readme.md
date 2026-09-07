# Mr Worm Postmortem

My initial plan was to port one of the many TI-83 or TI-89 games to Chip8. Sighnoceros' shooter suggested Phoenix.

I did some [experiments](http://johnearnest.github.io/Octo/index.html?gist=ec34b56c96d3b0a42ea9) with smooth movement using xor-masked sprites. The trick to moving a sprite smoothly is to xor the sprite in the source and destination positions together. This is pretty easy to do in Paint.net -- make two layers, set the blend mode to 'XOR', draw the sprite in different colors on both of them, and move the layers around.

Here's the Phoenix ship on top, with the xor-mask for vertical movement below it:

![Phoenix mask](http://i.imgur.com/lFlGC0k.png)

The movement was pretty smooth, but porting the whole game was less exciting. Searching for another game that could use the smooth movement, I remembered Mr Worm:

[MrWorm on ticalc.org](https://ticalc.org/archives/files/fileinfo/326/32621.html) [Mr Worm gameplay video](https://www.youtube.com/watch?v=ONzSyj1HeFk)

Snake, with 12 possible directions (the "o"s in the diagram, "x" is the center):

```text
 ooo
o   o
o x o
o   o
 ooo
```

## [Milestone 1](http://johnearnest.github.io/Octo/index.html?gist=7a3190977516d7cbed5c)

get the sprites and basic drawing/movement code done. This snake just wanders randomly until it hits itself.

[Endless version](http://johnearnest.github.io/Octo/index.html?gist=a9a6f01fe704e84b6a65)

![Endless version](http://i.imgur.com/fA5A1Tp.png)

## [Milestone 2](http://johnearnest.github.io/Octo/index.html?gist=4c38be1d63b0ec950f35): get the tail working. 

The following logic is very wrong, so the game turns into a short lightcycle game with a bad AI where you always lose. The plan is a circular queue of past snake directions, so to erase the tail there's a 'second snake' that just duplicates the draw calls that the head made so many steps ago. In this version, the circular queue is of variable size, and there's a significant amount of complexity managing the concepts of queuelen/queuecap/queuehead/queuetail.

This took a while to debug:

![Debugging the tail](http://i.imgur.com/K5na40F.png)

[Better](http://johnearnest.github.io/Octo/index.html?gist=73742fc1d3132566b7d4):

![A better tail implementation](http://i.imgur.com/kcbu40T.png)

[Working](http://johnearnest.github.io/Octo/index.html?gist=dcbc659a6947ec930699)

![The working tail](http://i.imgur.com/HgxEF0F.png)

## Milestone 3: a snake that grows when it eats things.

This is where things got hairy. The circular queue code worked, but it was written to only use 'queuecap' bytes -- the current maximum length of the tail.

The queue might be like this (where 6 is the last head position, and 0 is the tail that's following):

```text
4 5 6 0 1 2 3
```

Growing it, the queue needs to become something like:

```text
4 5 6 _ 0 1 2 3
(or)
0 1 2 3 4 5 6 _
```

This is annoying-- offsets needs to be juggled and memory copied, and Chip8 makes it harder. After banging my head against the wall for a few hours, I realized that the entire problem was irrelevant: there's no reason to constrain the queue to a minimal size in RAM. Once I realized this, I made the circular queue have a maximum size of 256 bytes. This eliminated the wraparound checks for queuehead/queuetail (since they'll overflow and wrap around naturally), and made growing the queue trivial.

## Milestone 4: Title, Score

Compared to the nightmare of attempting to get a dynamic queue working with no debugger, implementing the last few features to polish off the game was easy.
