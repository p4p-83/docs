I figure I'll just do gradient descent or something to minimise a placement badness cost function. Let's see if there's a sufficiently simple cost function available.

# Assumptions and simplifications

Generally, simplifications involve implicit assumptions. Let's see what we can do with the opposite analysis — I'll list out the assumptions that I'm willing to make, and see the simplifications that we can make as a consequence.

| assumption                         | condition                            | consequence                                                                                                                                                                                      |
| ---------------------------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| one lead centroid per pad centroid | decently clear mask (not fragmented) | Voroni diagram sort of thing — can describe placement as a set of pad-lead centroid pairs                                                                                                        |
| big leads get big pads             |                                      | Could weight the pairs' respective influence on cost function based on lead centroid weight. Could include a weighting term in the cost function that depends on closeness of match of the pads. |
|                                    |                                      |                                                                                                                                                                                                  |

# Cost function template v1

Let's just throw something down on paper based on these assumptions.

Let $\hat l_n$ be the centroid location of lead $n$ (as ordered in some internal data structure). We'll start here as the leads of the component being placed can be considered well defined (at least in comparison to literally anything else we're doing).

Let $\hat p_k$ be the position of the $k$th pad on the board (as ordered within some internal data structure).

Let $\hat p_m$ be the position of the pad centroid corresponding to the $n$th lead centroid. This pad position is selected from the set of all pad positions (all $\hat p_k$) such that the quantity $||l_n - p_m||$ (i.e. the distance between them) is as small as possible (minimised). $p_m$ is thus the closest pad to $l_n$ and we can define a pair $(l_n, p_m)$.

Let $p_n$ be the centroid of the closest pad to $l_n$.

---

Eh, I'm going to give up on this approach. I need to just prototype something. I'm not enough of a mathematician to be able to figure it out by just writing some formulae. I need to see what's happening.