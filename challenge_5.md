# Challenge 5 - Optimization and Arbitrage

Last week, we've manage to create graph of all the possible paths through pools joining two token of our choice. We are now going to use this data structure to find arbitrage opportunities ! 

Consider any valid path $A_1 \rightarrow .. \rightarrow A_n$ such that $A_1 \neq A_n$ and suppose that we constructed this path with $from = to$.

---

### Exercise 1

How would you decide if there is an arbitrage or not on this path ?

---

Now consider the same path but where $from \neq to$

---

### Exercise 2

How would you decide if there is an arbitrage or not on this path ?

*Hint*: it might require additionnal information.

If it requires external information, is there a way to approximate that information using our graph ?

---

Based on your answers in the exercises above, our objective is to find a vector of weights that maximize the following quantity

$$max_{w}f^{swap}_{A \rightarrow B}(w,x) - P\_{A,B}x$$

subject to

$$w_{i} \in [0;1], \forall i \in \mathbb{N}$$

$$\sum_{i \in Next(Pool)}w_i = 1, \forall Pool \in RouteGraph $$

Where $f^{swap}\_{A \rightarrow B}$ is the total swap function generated by the corresponding $RouteGraph$ and $P_{A,B}$ is the estimated price of B in terms of A. The weight vector $w$ represents how much of the amount in we send through a given path. Now let's see how to implement it !

First, we need to define the evaluation function that, given a weight vector $w$ and an amount $x$, returns the amount out resulting from the swap.

---

### Exercise 3

Implement two recursive functions on $RouteGraph$ that takes as input the weight vector and an amount in and return the amount out, potentially mutating the pool along the way.
```
swap(weights, amount_in) -> f64 // compute the total swap function
apply_swap(weights, amount_in) -> f64 // compute the total swap function, mutating each pool along the way
```

You should use the same methods that we've implemented on the pool in the previous challenges.

---

Now that we have a way of evaluating the route graph at a given configuration, let's write the optimisation algorithm.

---

### Exercise 4

Convince yourself that for a given $w$, $f^{swap}_{A \rightarrow B}(w, x)$ is a convex non-linear function 

*Hint*: How is $f^{swap}_{A \rightarrow B}$ constructed ? What can be said about about addition and composition of convex function ?

---

The fact that $f^{swap}_{A \rightarrow B}$ is convex is a really nice property because every maxima is a global maxima and there exists very efficient algorithm to solve it. Actually, the way we perform evaluation is very close to how we evaluate Neural Network and we can also use the backpropagation to compute the derivative allowing us to use gradient descent.

In anycase, here you are free to implement any optimisation algorithm you want !

---

### Exercise 5

Implement an optimisation algorithm that solve the maximisation problem defined above

```
solve(RouteGraph, amount_in) -> Option<Weights> // return the set of weight that yield an arbitrage or None if there is no arbitrage
```

---

The final step is to interpolate the solution from the weights. We will implement a function which going to produce the optimal graph that only contains the pool used along with the percentage (between 0-1) of the amount in we swap.

### Exercise 6

Implement the following function

```
interpolate_solution(RouteGraph, weights, amount_in) -> Solution
```

Where we have the following type signature

```
OptimalRouteGraph = (From, To, Pool, Weight, OptimalRouteGraph)
Solution = (OptimalRouteGraph, amount_in, amount_out)
```

---



