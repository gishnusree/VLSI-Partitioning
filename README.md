#  VLSI Partitioning

Partitioning is the process of dividing a complex VLSI system into smaller, manageable subsystems so that each unit can be designed, optimized, and implemented efficiently. It follows a divide-and-conquer approach, improving design productivity through parallel development while minimizing interconnections (cuts) between partitions to reduce complexity, delay, and power consumption. A cell is a basic unit, such as a logic gate, and a partition is a group of such cells. Dividing a design into two blocks is called bipartitioning, while dividing into more than two blocks is referred to as K-way partitioning. The primary objective is to minimize the cut set, subject to constraints such as terminal limits, area bounds, and the number of allowable partitions. Partitioning can be applied at the system, board, or chip level, each considering objectives like reducing off-board delays, chip-to-chip connections, or critical path disruptions.

Partitioning algorithms use the set of components and their netlist as inputs and aim to reduce the number of nets crossing partition boundaries. They can be constructive, producing initial partitions quickly, or iterative, refining existing partitions for better optimality. They may also be deterministic or probabilistic, and differ in method, such as group migration or randomized methods like simulated annealing. Performance-driven algorithms emphasize delay or speed, while metric-allocation algorithms rely on quantitative cost functions. To formally model the problem, a circuit can be represented as a graph 

$$
G = (V, E)
$$

where $V$ is the set of nodes (cells) and $E$ is the set of edges connecting two nodes. For hypergraph modeling, a circuit is represented as 

$$
H = (V, N)
$$

where $N$ is a set of nets, each of which can connect multiple cells.

The Kernighan–Lin (KL) algorithm is an iterative, graph-based bipartitioning method that divides $2N$ nodes into two equal-sized sets while minimizing the cut cost. Each node $v$ is assigned a D-value representing the difference between its external and internal connections:

$$
D(v) = E_{\text{external}}(v) - E_{\text{internal}}(v)
$$

The gain of swapping two nodes $a$ and $b$ is calculated as:

$$
\Delta G(a,b) = D(a) + D(b) - 2 C_{ab}
$$

where $C_{ab}$ is the edge weight between nodes $a$ and $b$ (0 if no edge exists). The algorithm repeatedly selects the pair with the maximum gain, marks them as fixed, updates D-values for affected nodes, and continues until all nodes are fixed. The cumulative gain over a pass is computed as:

$$
G_m = \sum_{k=1}^{m} \Delta G_k
$$

If the maximum cumulative gain $G_m > 0$, the corresponding swaps are applied and a new pass begins; if $G_m \le 0$, the algorithm terminates. Node D-value updates occur $O(n^2)$ times per pass, and gain evaluations occur $O(n^3)$ times, giving KL an overall time complexity of $O(n^3)$. The balance constraint can be formally expressed as:

$$
| |A| - |B| | \le 1
$$

where $A$ and $B$ are the two partitions.

The Fiduccia–Mattheyses (FM) algorithm improves upon KL by allowing single-cell moves and incorporating area-based balance constraints, making it suitable for hypergraphs where nets can connect multiple cells. For each cell $v$, the moving force $FS(v)$ is the number of nets connected to $v$ that would be cut if $v$ moves to the other partition, and the retention force $TE(v)$ is the number of nets that remain uncut if $v$ stays in its current partition. The gain of moving a cell is:

$$
\Delta G(v) = FS(v) - TE(v)
$$

FM selects the free cell with the highest gain that satisfies the balance constraint, determined by the ratio:

$$
R = \frac{\text{Area of Partition A}}{\text{Area of Partition A + Area of Partition B}}
$$

The cell is moved, marked as fixed, and FS, TE, and gains for affected cells are updated. This repeats until all cells are fixed. The cumulative gain over a pass is:

$$
G_M = \sum_{i=1}^{M} \Delta G_i
$$

and the prefix of moves giving the maximum positive cumulative gain is applied before starting a new pass. The algorithm terminates when no positive cumulative gain remains. FM supports unequal-sized partitions, hypergraphs, and efficient single-cell moves, and its runtime is generally proportional to the number of pins in the design, $O(P)$.

Overall, VLSI partitioning integrates graph- or hypergraph-based modeling, gain functions, cumulative gains, and constraints to iteratively optimize partitions while minimizing cuts, reducing delays, and maintaining balanced areas. KL provides a foundational method for balanced bipartitioning with node swaps, while FM extends it to single-cell moves, hypergraph structures, and area-based constraints, making it a versatile and practical solution for modern VLSI circuit design.

# Clock Tree Synthesis (CTS)

Clock Tree Synthesis (CTS) is a critical step in the VLSI physical design flow that comes after block placement and focuses on distributing the clock signal efficiently to all sequential elements in a synchronous digital system. The clock signal, often generated outside the chip, must reach every flip-flop and latch with precise timing to ensure correct synchronization, as the chip performance directly depends on the clock frequency. Routing the clock requires careful consideration of interconnect resistance ($R$), capacitance ($C$), noise, crosstalk, and the load presented by the number of flip-flops driven. As the chip size increases, interconnect capacitance dominates over gate capacitance, leading to RC delay, which scales approximately with the square of the scaling factor in advanced technology nodes. Simply widening the wire does not effectively reduce RC delay, so buffers are inserted to restore signal integrity and reduce transition times, though they add intrinsic delay, consume area, and increase power. Buffers can be used in centralized or distributed approaches, where the former uses a single large buffer with branching, and the latter uses multiple smaller buffers distributed along the clock tree.

The interconnect delay of a clock tree can be approximated using the Elmore delay model, which assumes a single input node with capacitances to ground and no resistive loops. The delay to a node $i$ is calculated as:

$$
t_{d,i} = \sum_{k \in \text{nodes}} C_k \cdot R_{ik}
$$

where $R_{ik}$ is the shared path resistance from the source to nodes $i$ and $k$. For unbuffered trees, all capacitances in parallel are summed to compute the total node capacitance. In buffered trees, buffers isolate RC segments, reduce effective capacitance, improve slew, and add their own delay $D_b$, output resistance $R_b$, and input capacitance $C_b$.

Another key metric is clock skew, the maximum difference in clock arrival time between any two sinks. For nodes $i$ and $j$, it is defined as:

$$
\text{Clock Skew} = \max \left| t_{s0 \rightarrow i} - t_{s0 \rightarrow j} \right|
$$

A balanced clock tree minimizes skew, ensuring all nodes receive the clock simultaneously, which improves circuit performance and allows higher operating frequencies. The main objectives of CTS are therefore to reduce clock skew and interconnect delay, enabling fast and reliable operation of high-performance chips.
