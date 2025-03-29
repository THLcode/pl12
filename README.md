[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/-tWzwZLV)
# Assignment 1: Syntax-Guided Synthesis

For this assignment, you will be synthesizing and analyzing strategies to solve the following problem.

Suppose you have two copiers in a building `copier[0]` and `copier[1]`. Each copier can hold `capacity` units of paper. Every morning, you can go to one of the copiers and add up to `maxfill` units of paper to it, but you don’t have time to go to both of them, so you have to choose which one to go to. However, the printers are online, so before choosing one, you can look online to see how much paper each printer has left in it. Finally, because of printing quotas, you know that the maximum amount of paper used on both printers on a given day is `maxuse` units.

Your goal is to find a strategy that guarantees that none of the copiers ever runs out of paper. For all the exercises you should use as a staring point the definitions provided in [copier.sk](copier.sk). You should submit one sketch for each question below.

*Hint 1:* If you make the number of steps a parameter to the harness, the synthesizer will first look for counterexamples with short horizon, and will only consider the full 14 steps when it is close to a correct solution, improving efficiency significantly. You can use assume to ensure that this input number of steps is less than or equal to 14.

*Hint 2:* The inlining factor (`--bnd-inline-amnt`) controls (exponentially) the search space of predicates and has a huge impact on performance. You may want to tune it to the minimal number of inlining needed to generate the desired solution.

*Hint 3:* Sketch operates with an element of randomness. Run your sketch multiple times and examine the generated solutions. If any solution is clearly incorrect, review and adjust your encoding to ensure such solutions are eliminated. You may run the generated C++ code with randomly generated test cases. The [script](script) is for your reference.

## Question 1

Let `maxfill=20`, `capacity=27` and `maxuse=13`. Find a strategy within the space of programs shown below that guarantees that the copiers never run out of paper within a bounded horizon of 14 steps. We model the strategy as a boolean predicate on the state. If true, we refill copier 0; if false, we refill copier 1.
```
pred ::= exp+?? > exp+?? | !pred | pred || pred | pred && pred 
exp ::= copier[??] | 0
```
You will find it useful to restrict the range of `??` to a smaller number of bits. If you use the syntax `??(N)`, this indicates an `N`-bit unknown. This notation, however, cannot be used inside a regex generator.

Name your Sketch file for this question `copier1.sk`.

## Question 2

Now we want to check if your solution for Question 1 generalizes to an unbounded number of steps. In order to do that, you need to synthesize the set of safe states. Use the same space of predicates above to find a predicate that characterizes the set of safe states. Similar to inductive loop invariants for program verification, the set of safe states must be weak enough be satisfied by the initialization; and strong enough to guarantee that the printers are nonempty; and indeed an invariant if we follow the strategy found for Question 1. You may want to write three separate harness functions for these conditions.

Name your Sketch file for this question `copier2.sk`.

## Question 3

Let `maxfill = 27`, `capacity=27` and `maxuse=10`. Suppose that every Wednesday (`day%7==2`) the quotas are extended so that in addition to the maxuse units of paper users are allowed to use, they can collectively use an additional 8 units from copier 0, and every Saturday (`day%7==5`), they can use an additional 8 units from copier 1. Find a strategy within the extended space of programs shown below that guarantees that the copiers never run out of paper *within a bounded horizon of 14 steps (two weeks)*.
```
pred ::= exp+?? > exp+?? | !pred | pred || pred | pred && pred 
exp ::= copier[??] | day%7 | 0
```
You may need a relatively high depth in order to synthesize this from scratch from the grammar above. However, if you explicitly tell the system that your strategy will be of the form
```
if (i%7 < ??) { 
    return pred (...);
} else {
    return pred (...);
}
```
you can have much smaller bounds for those preds and it will find a solution much faster.

Name your Sketch file for this question `copier3.sk`.
