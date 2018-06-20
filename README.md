# The number of numerical semigroups with given genus

by *P.A. García-Sánchez, D. Marín-Aragón, and A.M. Robles-Pérez*

**Case study of the tree of numerical semigroups with multiplicity three**

*You can play with this in [![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/pedritomelenas/tree-numerical-semigroups/master?filepath=francy-monoids-mult-three.ipynb)*

A *numerical semigroup* is a submonoid of the set of nonnegative integers with respect to addition, with the special property that only finitely many positive integers do not belong to the semigroup. These integers not in the numerical semigroup are called *gaps*, and their cardinality, the *genus* of the numerical semigroup.

The number of numerical semigroups with a given genus seems to grow as the genus grows. It was [conjectured](https://link.springer.com/article/10.1007/s00233-007-9014-8) (and later [solved](https://link.springer.com/article/10.1007/s00233-012-9456-5)) that this sequence grows as the Fibonacci series.

[GAP](https://gap-system.org) offers a package for working with numerical semigroups ([NumericalSgps](https://gap-packages.github.io/numericalsgps)), and a package for displaying data in a jupyter notebook is under development: [francy](https://github.com/mcmartins/francy). [FranyMonoids](https://http://gap-packages.github.io/FrancyMonoids) takes profit of them both. In order to use it in GAP we load it with `LoadPackage`.

```
LoadPackage("FrancyMonoids");
```

>    #I  Please load package NormalizInterface or 4ti2Interface
>    #I  to have extended functionalities.
>    #I  Please load package SingularInterface or singular (not both)
>    #I  or GradedModules to have extended functionalities.
>
>    true


FrancyMonoids loads both NumericalSgps and francy, so for instance we may have a look at the first 15 elements of our sequence.


```
List([1..15], g->Length(NumericalSemigroupsWithGenus(g)));
```

 >   [ 1, 2, 4, 7, 12, 23, 39, 67, 118, 204, 343, 592, 1001, 1693, 2857 ]

The maximum genus for which we know the number of numerical semigroups with this genus is 70. Just the set of numerical semigroups with genus less than or equal to 57 is worth 14TB of memory. In this [repository](https://github.com) you will find the sequence up to 70.

Even though it has been shown that the number of numerical semigroups with given genus grows like the Fibonacci sequence, we still do not know if there are more numerical semigroups with genus $g+1$ than numerical semigroups with genus $g$. This is true asymptotically, and for genus less than 71.

## The tree of numerical semigroups

For a given numerical semigroup $S$, if $n$ is such that it cannot be written as a sum of two nonzero elements of $S$, then $S\setminus\{n\}$ is a numerical semigroup with genus the genus of $S$ plus one. The elements $n$ with this condition are known as *atoms*, *irreducibles* or *minimal generators* of $S$, and it is easy to show that there are finitely many of them. Also if $S$ is a numerical semigroup and $F$ is the largest integer not in $S$ (known as the *Frobenius number* of $S$), then $S\cup\{F\}$ is again a numerical semigroup with genus equal to the genus of $S$ minus one. One can easily see that we can arrange the set of all numerical semigroup with genus up to $g$ in a tree rooted in $\mathbb{N}$ and where each level $i$ contains all numerical semigroups with genus $i$. In order to construct the next level one removes minimal generators greater than the Frobenius number (in this way we do not get repetitions). The way this tree is constructed gives different algorithms to compute all numerical semigroups with a given genus.

Let us have a look at the tree of numerical semigroups with genus up to 6. We start with $\mathbb{N}$, whose unique minimal generator is $1$ and Frobenius number -1. After removing it, we obtain $\{0,2,3,\to\}$ with minimal generators $2$ and $3$, and Frobenius number 1. Then we can remover either $2$ or $3$, and keep repeating this process until we reach genus $6$.

Francy gives the possibility of drawing [d3js](https://d3js.org) [interactive trees](http://bl.ocks.org/d3noob/8375092). Methods to construct trees and draw them using GAP are provided in francy.

```
DrawTreeOfSonsOfNumericalSemigroup(NumericalSemigroup(1),6,MinimalGenerators);
```

![tree-sons-N](figures/diagram.png)

Dark blue dots in levels over the last level are leafs, that is, numerical semigroups with no irreducibles greater than the Frobenius number. Non-leaves are "collapsable": by clicking on them the subtree rooted in that node collapses to the node. Also zoom and dragging is possible.

It would be nice to control how many leaves do we have in a certain level, and how many numerical semigroups with more than one descendant.

# Fixing the multiplicity

The multiplicity of a numerical semigroup is the least positive integer in the semigroup. A simplification of the above problem would be to see if for a given multiplicity $m$ the number of numerical semigroups with genus $g$ and multiplicity $m$ grow as $g$ grows. 

Let us have a look at how the tree restricted to multiplicity three looks like.

First, we must change `MinimalGenerators` in the above example to compute those generators that are not the multiplicity, and then replace $\mathbb{N}$ (the numerical semigroup generated by 1) by $\{0\}\cup(3+\mathbb{N})$, which has minimal generators $3$, $4$ and $5$. 

```
gens:=s->Difference(MinimalGenerators(s), [Multiplicity(s)]);
```

>    function( s ) ... end

```
s:=NumericalSemigroup(3,4,5);
```

>   Numerical semigroup with 3 generators


```
DrawTreeOfSonsOfNumericalSemigroup(s,5,gens);
```

![tree-sons-N](figures/diagram-2.png)

Observe that the labels of the nodes are the set of minimal generators of the semigroups without the multiplicity. 

Let us go a bit further.

```
DrawTreeOfSonsOfNumericalSemigroup(s,15,gens);
```

![tree-sons-N](figures/diagram-3.png)

And a couple of steps beyond.

```
DrawTreeOfSonsOfNumericalSemigroup(s,25,gens);
```

![tree-sons-N](figures/diagram-3.png)

It looks like a pattern arises. And indeed is the case. There are several places where numerical semigroups with multiplicity 3 and genus $g$ has been counted, but this drawing suggests that one can even which of these are leaves, and those having just one son or two sons (there will be no more than two sons, since multiplicity three implies that at most we have three minimal generators, and we cannot remove the smallest one, the multiplicity).

If you want to know more, please have a look at  this [preprint](https://arxiv.org/abs/1803.06879). You will find there references and results for multiplicity up to five. This is a quite easy problem to state, and it has produced a nice bunch of beautiful papers.

An introduction to numerical semigroups can be found [here](https://www.springer.com/la/book/9781441901590) and some applications in this [monograph](https://www.springer.com/us/book/9783319413297).
