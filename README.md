# Nested Sampling

A simple look at nested sampling using molecular dynamics in 2 dimensions.


#### Table of Contents
[The Goals of this Tutorial](#the-goals-of-this-tutorial)

[Note on Coding](#note-on-coding)

[Molecular Dynamics](#molecular-dynamics)

[Nested Sampling](#nested-sampling)
* [Calculating Sample Energies](#calculating-sample-energies)
* [Replacing and Decorrelating](#replacing-and-decorrelating)

[Heat Capacity](#heat-capacity)
* [The Partition Function](#the-partition-function)

[Further Work](#further-work)

[Resources](#resources)

## The Goals of this Tutorial

The main goal of this guide is to help you 
get a more intuitive understanding of the nested sampling algoritm. Along the way, you'll learn about molecular
dynamics, the partition function, and the Julia coding language. 

The reason for interest in the nested sampling 
algorithm is its potential for creating phase diagrams. The nested sampling algorithm holds promise because it has 
an easier time dealing with phase transitions than other computational methods without being too complicated to use.
In fact, one can use nested sampling to create phase diagrams without any prior knowledge of the phase transitions 
whatsoever. On top of that, temperature doesn't factor into the algorithm at all. After enough sample energies have 
been collected, calculating the heat capacities remains only as a final step at the end of the whole process.


## Note on Coding

This tutorial is simple enough for someone with little coding experience. You can follow this tutorial using any 
coding language you'd like, but the examples will use the Julia coding language. Whatever language you choose, 
these are some basic things that you will need to be able to do:

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
crossed. Something else that sometimes happened is that crossing a boundary could cause particle to go totally
haywire and leave the box entirely. I found that the most helpful thing for troubleshooting boundary issues was to
initialize the particles with a velocity toward the boundaries and watch the animations my code made.


## Nested Sampling

After the molecular dynamics tutorial, you should now be able to create, run, and keep track of simulations, or samples. These 
samples will be key for your nested sampling algorithm. The pattern goes like this: the samples are allowed to run
for some amount of time. Then, the sample energies are calculated. The highest sample energy gets saved to a list for
use in the heat capacity calculation. The sample it belongs to gets replaced by an existing sample, which is "cooled" 
and decorrelated to create a new, lower energy sample. This pattern repeats for as long as you'd like. The more
sample energies you record, the better your heat capacity calculations will be.

### Calculating Sample Energies

At this point, you should now create a way to calculate the energy of a sample. Use the Lennard-Jones potential 
(equation 9.6 in Giordano Chapter 9) to find the potential energy of each pair of particles, similar to how you 
found the forces between each particle for running the MD simulations.You should be able to use the same distances 
between particles as you did in your force calculations for the molecular dynamics simulation. Take care to only 
calculate the potential once for each pair of particles. If you calculate the potential with each individual 
particle in mind rather than each pair, you'll need to divide your total potential energy by 2 in order to 
get the correct value.

If we were using a different method for simulating our particles (such as Monte-Carlo) that would be the end of 
our energy calculations. However, using the method of molecular dynamics to track your samples will require us 
to calculate the kinetic energy of the samples as well. In the same frame that you find
the potential energies, calculate the kinetic energy of each particle using the classical formula (KE = 1/2 m v^2).
The particle velocities can be found using verlet method from your molecular dynamics calculations simply enough,
but you need to take care that the velocities you use are from the same point in timae as the positions you used in
the Lennard-Jones potential calculations.

Lastly, find the total energy of each sample by summing their respective potential and kinetic energies together.

### Replacing and Decorrelating

Now that you've calculated each sample's potential and kinetic energy and recorded the highest total sample energy,
it's time to replace the high-energy sample and decorrelate it. To do that, simply copy another random sample's particle 
locations and trajectories and use them to overwrite those of the high-energy sample.

Decorrelating is a bit weird since we're using MD. Ideally we'd have our samples "cool" by transferring energy to some 
outside heat sink. This isn't something we can easily do with molecular dynamics, but if we do nothing, the law of conservation 
of energy will prevent our sample energies from decreasing over time. The easiest way to leech energy from our samples is to 
slightly decrease the kinetic energy.

Since the molecular dynamics method focuses on position rather than velocity for calculating trajectories, it might seem like 
we're in a tricky spot. However, our method for initializing the samples DOES use velocity information to create a fictitious 
previous position for use by the Verlet method. Our strategy for decorrelating the copied sample then will be to essentially 
reinitialize it using its own particle velocities scaled-down and its current particle positions. After initializing the copied sample with
its newly modified previous position information, run the sample for a little bit. At the end of the run, check that its total energy 
is lower than the most recently recorded highest energy.

At this point, you now have a fresh decorrelated sample with a slightly lower energy, and you can repeat the process of
running your samples before recording, replacing, and decorrelating the next high-energy sample. The more you run through this
algorithm, the more information you'll have for calculating the heat capacity of our Lennard-Jones particles.


## Heat Capacity

We can get information about the heat capacity by using the list of sample energies collected during the nested sampling algorithm. 
In order to do this, there are a couple of things we need to do. First, generate a list of temperatures that we want to know 
the heat capacity for. For each temperature in the list, we'll use our sample energies to calculate an estimate of the partition 
function, and then calculate the heat capacity at that temperature using both the sample energies and the estimate for the partition 
function.

### The Partition Function

The partition function Z is a dimensionless number that contains information about every possible state of a system. It is defined 
as the sum of all the Boltzmann factors for a system, and can be used to find the probability of a specific state by dividing a 
stat'es Boltzmann factor by the partition function. Its property of being able to "see" every possible state of a system makes it 
useful for finding interesting thermodynamic quantities, such as heat capacity.

Most of what I did for this part came from pages 1-8 of "Efficient sampling of atomic configurational spaces". Equation 16 on page 8
is what I used for calculating the heat capacity, but there are a few differences that should be kept in mind. First, our sampled
energies include both kinetic and potential energy. In the paper, the sampled energies En only refer to potential energy, while the 
constant 3Nk/2 accounts for kinetic energy. What I've done so far to get around this is to use Equation 16 with the sampled total 
energies and just leave off the 3Nk/2 term. I haven't been able to figure out if this is correct or not


## Further Work

Phase diagrams!

Other potentials, other simulation methods, 3 dimensions...


## Resources

Most of what I have learned about molecular dynamics, nested sampling, and thermodynamics can be found in these sources.

These rescources can be found in the resources folder of this repository.
* _Giordano Chapter 9_ pdf
  * This is a tutorial for creating a molecular dynamics simulation.
* _Efficient Sampling_ (Pártay, Bartók, Csányi) pdf
  * This has useful information about nested sampling and heat capacity calculations.

This file was too big to add to the repository, but can be easily found using a Google Search.
* _Nested sampling for computational thermodynamics_ (Pártay) pdf
  * This gives a good overview of the application of nested sampling for thermodynamics.

Dr. Hart can help you get access to a copy of this book.
* _An Introduction to Thermal Physics_ (Schroeder) Textbook
  * This was useful to me for understanding the partition function.
