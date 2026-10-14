<!-- solve-problem:ed56271e-4dc9-4d63-a96e-045e022cb0ec -->
# Sublogarithmic dynamic forest orientation

A [Tangle Solve problem](https://solve.tangle.tools/problems/ed56271e-4dc9-4d63-a96e-045e022cb0ec).

## What is the problem?

Maintain a forest F = (V, E), |V| = n, under a sequence of updates — edge deletions, edge insertions (keeping the graph a forest), and vertex insertions/deletions — while explicitly maintaining an **orientation** of every edge such that:

1. **Bounded out-degree:** every vertex has out-degree at most c for some constant c (independent of n). Since a forest has arboricity 1, an orientation with out-degree ≤ 1 exists statically (orient each tree away from a root); the question is whether one can be maintained dynamically.
2. **Update time:** each update is handled in **o(log n) amortized time**, beating the O(log n) barrier. The orientation itself must be output/updatable explicitly: the data structure must be able to report, for any vertex, its out-neighbors, and must physically flip edges when needed.

The precise target: a data structure supporting `insert(e)`, `delete(e)`, `delete(v)` (deleting a vertex deletes its incident edges, possibly in O(deg(v)) time), maintaining a valid orientation, with amortized time per update o(log n) — ideally O(log n / log log n), polyloglog n, or O(1). An **impossibility result** (a matching lower bound, e.g., via cell-probe or reduction) resolving the negative side would also count as progress.

Ambiguity note: the census does not pin down whether deletions of vertices are supported or whether amortization is global vs. per-update; the standard interpretation (following Brodal–Fagerberg) is amortized over an intermixed sequence with vertex deletion charged to degree.

## Why does it matter?

A bounded-out-degree orientation is the canonical dynamic representation for arboricity-c ≤ graphs; it immediately yields O(c) query time for adjacency and neighborhood listing, and is the bottleneck primitive in dynamic algorithms for sparse graphs (matching, coloring, sparsification). Beating log n for the simplest case — forests, arboricity 1 — would be the first evidence that sublogarithmic orientations are possible, likely unlocking o(log n) dynamic algorithms for all bounded-arboricity graph classes. It is on Korhonen's open problem list precisely because the O(log n) barrier is unexplained.

## What's already known?

- **Static:** every forest can be oriented with max out-degree 1 (trivially), but this orientation can be fragile under updates — an adversarial sequence of insert/delete may force many flips per update.
- **Brodal & Fagerberg (1999), "Dynamic Representations of Sparse Graphs"**: give a randomized data structure maintaining orientations of graphs with arboricity c with O(log n) expected amortized update time and out-degree O(c). Their approach uses a "move-to-root"-style / random-blocking argument: when an edge insertion creates a vertex with out-degree exceeding c + something, they search for a short flipping path; the analysis shows O(log n) expected length. This is the state of the art cited for the forest case.
- **Karpinski & Rokicki (—to check; related work on dynamic forest orientation) (verify)**: reported O(log n) amortized results for forest orientation; the census does not name them, so verify before relying on this.
- **Lower bounds:** none known that rule out o(log n) for forests; the O(log n) bound appears to be an artifact of the flipping-path analysis, not a fundamental barrier.
- Related problems solved or nearly solved: dynamic trees (link-cut trees do O(log n) but for path queries, not orientation); dynamic connectivity in forests is trivial (union-find). The orientation-with-bounded-out-degree requirement is what makes this distinct.

## What would count as solving it?

Positive: a (randomized or deterministic) data structure maintaining an orientation with out-degree ≤ c (constant, ideally 1 or 2) of a dynamic forest, with amortized update time o(log n) — e.g., O(log n / log log n) or expected O(polyloglog n) — with a proof of correctness and the amortized bound against an adaptive adversary (or specify the adversarial model). Partial credit: any upper bound of the form O(log n / f(n)) for unbounded f, or o(log n) for the deletion-only or insertion-only case, or for out-degree ≤ c > 1.

Negative: a lower bound in the cell-probe or pointer-machine model showing Ω(log n) amortized flips are necessary for any constant out-degree, closing the problem. A weaker Ω(log n / log log n)-type bound matching an upper bound would also be a resolution.

## How to start

1. Read Brodal & Fagerberg (1999) carefully, focusing on the forest/arboricity-1 specialization of their analysis: identify exactly where the O(log n) expected path length arises (it is a geometric-decay argument over random vertex levels). The question is whether that step is tight.
2. Check Korhonen's open problems document for the exact phrasing and any partial results he mentions (verify the current version — the census lists it as a 2026 list).
3. Natural approach A: de-amortize/derandomize differently — e.g., maintain a rooted forest with Euler-tour machinery and show that "re-rooting" costs can be bounded by o(log n) using a potential function that counts inversions relative to a slowly-updated reference orientation.
4. Natural approach B (lower bound): try to build an adversarial update sequence forcing Ω(log n) flips, or reduce from a problem with a known cell-probe lower bound (e.g., marked-ancestor / prefix-sum style). Main obstacle: forests are structurally simple, so most lower-bound machinery for dynamic graphs needs cycles.

## Key references

- Brodal, G. S., & Fagerberg, R. (1999). *Dynamic Representations of Sparse Graphs*. Proc. WADS/WADS '99, LNCS 1663, pp. 342–351. Maintains an orientation of a dynamic graph with arboricity c with O(c) out-degree and O(log n) expected amortized update time via random blocking levels. https://dl.acm.org/doi/10.1007/3-540-48447-5_29
- Korhonen, T. *Open Problems (dynamic graph algorithms list)*. Lists sublogarithmic dynamic forest orientation as open; source is a 2026 list per the census — verify availability and exact wording. (no stable link recorded)
- (verify) Karpinski, M., & Rokicki, Ł. — *The dynamic forest orientation problem* — reportedly studies exactly this problem with logarithmic bounds; confirm citation details before use.

## Warnings

- The problem is specifically about **forests** (arboricity 1); do not conflate with general bounded-arboricity graphs, where the question is strictly harder.
- "Orientation" must be explicit and maintained — a structure that can only answer "which way does edge e point" in o(log n) but requires Θ(log n) work to report out-neighbors or perform physical flips does not count.
- The naive orient-toward-a-root scheme fails: adversarial edge swaps force Θ(n) flips per update; any solution must handle the re-rooting cascades.
- Amortization model matters: state whether your bound holds against adaptive adversaries (if randomized) and whether vertex deletions are included.
- The census is machine-generated (2026); at least one "key work" beyond Brodal–Fagerberg is unverified — re-derive the state of the art from the literature before claiming novelty.

---
*`ALGO-000125` · unverified*

## Acceptance guidance

Positive: a (randomized or deterministic) data structure maintaining an orientation with out-degree ≤ c (constant, ideally 1 or 2) of a dynamic forest, with amortized update time o(log n) — e.g., O(log n / log log n) or expected O(polyloglog n) — with a proof of correctness and the amortized bound against an adaptive adversary (or specify the adversarial model). Partial credit: any upper bound of the form O(log n / f(n)) for unbounded f, or o(log n) for the deletion-only or insertion-only case, or for out-degree ≤ c > 1.

Negative: a lower bound in the cell-probe or pointer-machine model showing Ω(log n) amortized flips are necessary for any constant out-degree, closing the problem. A weaker Ω(log n / log log n)-type bound matching an upper bound would also be a resolution.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/ed56271e-4dc9-4d63-a96e-045e022cb0ec?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000125.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000125)
