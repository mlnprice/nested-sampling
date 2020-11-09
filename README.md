# Nested Sampling

A simple look at nested sampling using molecular dynamics in 2 dimensions.


#### Table of Contents
[The Goals of this Tutorial](#the-goals-of-this-tutorial)

[How to Use Julia](#how-to-use-julia)

[Molecular Dynamics](#molecular-dynamics)

[The Partition Function](#the-partition-function)

[Nested Sampling](#nested-sampling)


## The Goals of this Tutorial

Phase diagrams are important! It sure would be cool if we could make good phase diagrams using computers.
The nested sampling algorithm holds promise because it has an easier time dealing with phase transitions than 
some other computational methods, but is not too complicated to use. The main goal of this guide is to help you 
get a more intuitive understanding of the nested sampling algoritm. Along the way, you'll learn about molecular
dynamics, the partition function, and the Julia coding language.

## How to Use Julia

This tutorial is simple enough for someone with little coding experience. You can follow this tutorial using any 
coding language you'd like, but the examples will use the Julia coding language. Whatever language you choose, 
you'll need to be able to do some basic things:
* Print values
* Create, modify, and operate on array lists
* Use for loops, while loops, and if statements 
* Create and use functions
* Make plots and animations using array lists

## Molecular Dynamics

The most important component of the nested sampling algorithm is the method for simulating samples. The method
we use here is called molecular dynamics, or MD for short. Read GIORDANO CHAPTER 9 to gain an understanding of 
what molecular dynamics is and follow the examples to create your own molecular dynamics simulations. Make plots
and animations of your simulations to see if things are acting correctly.

I found that the trickiest part of coding my first MD simulations was implementing the periodic boundary conditions.
I had trouble making it so the particles could see each other across the boundary. There were many situations where
a pair of particles would be crossing the boundary together, but lose sight of each other and drift apart as they 
crossed. Something else that sometimes happened is that crossing a boundary could cause particlee to go totally
haywire and leave the box entirely. I found that the most helpful thing for troubleshooting boundary issues was to
initialize the particles with a velocity toward the boundaries and watch the animations my code made.

## The Partition Function

## Nested Sampling
