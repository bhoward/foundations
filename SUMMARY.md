# Summary

* [Functional Programming](fp.md)
   * [Equational Reasoning](fp-equational.md)
Declarative programs, without side-effects. Substitution of equals for equals.

   * [Drawings](fp-drawing.md)
Alternative to turtle commands; compositional. Definition by cases and pattern matching. Recursion as decomposition.

   * [Lists](fp-list.md)
Lists of points, lists of drawings. Visualize lists of numbers. Common list operations. MapReduce.

* [Properties of Programs](pp.md)
   * [Sorting Algorithms, Functionally](pp-sorting.md)
Recursion and proofs by induction. Insertion and Selection sort.

   * [Running Time and Recurrences](pp-recurrence.md)
Estimating the work of a recursive program. Common patterns of recurrence relations. Tabulation, repeated substitution, guess-and-check. Amortized running time? Fibonacci; Towers of Hanoi (exponential). Work vs Span, and Brent's Theorem.

   * [Logarithmic Behavior](pp-logarithmic.md)
Quicksort, Merge Sort. Binary Search. Quickselect.

   * [Verification](pp-verification.md)
Preconditions, postconditions, invariants.

* [Linked Structures](link.md)
   * [Trees](link-tree.md)
Binary Search Trees. Traversals. Tree Sort. Heaps (Skew) and Heap Sort. Parallel (tree-based) Merge Sort? Depth-first/Breadth-first; stacks, queues, and priority queues. Expression Trees. Tries. Huffman Codes. Dags and sharing.

   * [Sets and Relations](link-set.md)
Sets. Properties of functions (injection, surjection, bijection) and relations (equivalence and partial order -- relate to conditions on equals, hashcode, and compareTo). Implementation of sets and relations. Maps.
Include relational algebra? Boolean algebra?

   * [Graphs](link-graph.md)
Graph representations.
DFS (forest): cycle or lexicographic order. BFS: shortest path, Dijkstra \&\ Prim. Floyd/Warshall on adjacency matrix. PERT charts. Call tree, series-parallel graph (for scheduling).

* [Formal Languages](lang.md)
   * [Finite State Machines](lang-fsm.md)
DFA, NFA, RE. Lexical analysis, network protocol specs, string searching. Geometric (FA) vs algebraic (RE) descriptions; spaghetti vs structured code.

   * [Grammars](lang-grammar.md)
Regular, Context-Free. Parse Trees. Parsing the Drawing Language.

* [Digital Logic](logic.md)
Propositional Logic; identities and truth tables.
Combinational Circuits (expression dag). Sequential Circuits (add feedback). Circuit delay; divide-and-conquer design. Sequential design with DFA.

* [Beyond](beyond.md)
Turing Machines; Halting Problem.

