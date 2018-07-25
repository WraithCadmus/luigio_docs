# How does LuigI/O work?

## What is a neural net
First we have to look at the neural networks that MarI/O and LuigI/O produce.

### Input
This is what LuigI/O can 'see', it's the 'radar' view on the top left. It's a view of all the blocks within 8 blocks of Luigi's current position, and is produced by reading memory addresses.

Tiles are white squares, and sprites are black, this means that Luigi can't tell the different between a regular brick and an item box, nor between a platform and a goomba.

### Output
Up, Down, Left, Right, A, and B. These correspond to the buttons on the virtual NES controller, Select does nothing in SMB, and allowing Start would allow Luigi to pause the game, which won't help him reach the end.

### The crazy stuff in the middle

These are **neurons**, and they tell Luigi what output should come from a given input. White squares produce a positive signal, and black squares a negative one.

Green lines show a *positive* action propagating the action as it is, so a green line from a terrain block (white) immediately in front and below Luigi connected to 'Right' would make Luigi run right until he reached the edge of the platform, where he'd stop.

Red Lines are a *negative* action, if one of those links a block to a button it inverts the signal. So a red line from a terrain block (white) above Luigi to A would be 'don't jump' and a red line from a sprite (black) directly in front of Luigi to A would be inverted, 'don't not jump' and Luigi would jump.

In between there are other squares, these are like buttons in that their state is controlled by the world, but rather than being used directly they get used by *other* neurons, allowing for more complex setups.

#### So what about the squares under the input map?

These are called **oscillators**, and part of the network is times when these toggle between black and white. This means Luigi could potentially see the same pattern of blocks, but react differently as it's later in the level.

## Fitness
So how do we know if one neural net is better than another? Well we provide a score, called a Fitness, "how good are you at doing what we want you to do?", for Super Mario Bros it's simple, further to the right is better. For every pixel Luigi makes it he gets a point, note that he only gets the points when his feet are on the ground (a change from MarI/O), this is prevent long but useless jumps into pits getting higher scores.

## The Genetic Algorithm

So where does the 'genetic' part come in? Well our smallest unit is a **genome**, this represents a single neural network, and it gets one attempt at a level, getting a Fitness score. Genomes exist in a collection called a **species**, and several species exist together in a **generation**. Each species started with one genome, many were blank, but random mutations meant some would do things, gaining fitness. Once every genome in every species has a fitness function, those that have the highest fitness are chosen to breed, their networks combined, these would go on to become a species in the next generation with a certain number of genomes. These new genomes would resemble their parents, but each one has small random changes, mutations. Then we start again, seeing if any of these mutations produce more fitness, and we repeat.
