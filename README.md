# VLSI-Partitioning

Partitioning is the process of dividing a complex VLSI system into smaller, manageable subsystems so that each unit can be designed, optimized, and implemented efficiently. It follows a divide-and-conquer approach, improving design productivity through parallel development while minimizing interconnections (cuts) between partitions to reduce complexity, delay, and power consumption. A cell is a basic unit, such as a logic gate, and a partition is a group of such cells. Dividing a design into two blocks is called bipartitioning, while dividing into more than two blocks is referred to as K-way partitioning. The primary objective is to minimize the cut set, subject to constraints such as terminal limits, area bounds, and the number of allowable partitions. Partitioning can be applied at the system, board, or chip level, each considering objectives like reducing off-board delays, chip-to-chip connections, or critical path disruptions.

Partitioning algorithms use the set of components and their netlist as inputs and aim to reduce the number of nets crossing partition boundaries. They can be constructive, producing initial partitions quickly, or iterative, refining existing partitions for better optimality. They may also be deterministic or probabilistic, and differ in method, such as group migration or randomized methods like simulated annealing. Performance-driven algorithms emphasize delay or speed, while metric-allocation algorithms rely on quantitative cost functions. To formally model the problem, a circuit can be represented as a graph 
𝐺
=
(
𝑉
,
𝐸
)
G=(V,E), where 
𝑉
V is the set of nodes (cells) and 
𝐸
E is the set of edges connecting two nodes. For hypergraph modeling, a circuit is represented as 
𝐻
=
(
𝑉
,
𝑁
)
H=(V,N), where 
𝑁
N is a set of nets, each of which can connect multiple cells.

The Kernighan–Lin (KL) algorithm is an iterative, graph-based bipartitioning method that divides 
2
𝑁
2N nodes into two equal-sized sets while minimizing the cut cost. Each node 
𝑣
v is assigned a D-value representing the difference between its external and internal connections: 
𝐷
(
𝑣
)
=
𝐸
external
(
𝑣
)
−
𝐸
internal
(
𝑣
)
D(v)=E
external
	​

(v)−E
internal
	​

(v). The gain of swapping two nodes 
𝑎
a and 
𝑏
b is calculated as 
Δ
𝐺
(
𝑎
,
𝑏
)
=
𝐷
(
𝑎
)
+
𝐷
(
𝑏
)
−
2
𝐶
𝑎
𝑏
ΔG(a,b)=D(a)+D(b)−2C
ab
	​

, where 
𝐶
𝑎
𝑏
C
ab
	​

 is the edge weight between nodes 
𝑎
a and 
𝑏
b (0 if no edge exists). The algorithm repeatedly selects the pair with the maximum gain, marks them as fixed, updates D-values for affected nodes, and continues until all nodes are fixed. The cumulative gain over a pass is computed as 
𝐺
𝑚
=
∑
𝑘
=
1
𝑚
Δ
𝐺
𝑘
G
m
	​

=∑
k=1
m
	​

ΔG
k
	​

. If the maximum cumulative gain 
𝐺
𝑚
>
0
G
m
	​

>0, the corresponding swaps are applied, and a new pass begins; if 
𝐺
𝑚
≤
0
G
m
	​

≤0, the algorithm terminates. Node D-value updates occur 
𝑂
(
𝑛
2
)
O(n
2
) times per pass, and gain evaluations occur 
𝑂
(
𝑛
3
)
O(n
3
) times, giving KL an overall time complexity of 
𝑂
(
𝑛
3
)
O(n
3
). The balance constraint can be formally expressed as 
∣
∣
𝐴
∣
−
∣
𝐵
∣
∣
≤
1
∣∣A∣−∣B∣∣≤1, where 
𝐴
A and 
𝐵
B are the two partitions.

The Fiduccia–Mattheyses (FM) algorithm improves upon KL by allowing single-cell moves and incorporating area-based balance constraints, making it suitable for hypergraphs where nets can connect multiple cells. For each cell 
𝑣
v, the moving force 
𝐹
𝑆
(
𝑣
)
FS(v) is the number of nets connected to 
𝑣
v that would be cut if 
𝑣
v moves to the other partition, and the retention force 
𝑇
𝐸
(
𝑣
)
TE(v) is the number of nets that remain uncut if 
𝑣
v stays in its current partition. The gain of moving a cell is then 
Δ
𝐺
(
𝑣
)
=
𝐹
𝑆
(
𝑣
)
−
𝑇
𝐸
(
𝑣
)
ΔG(v)=FS(v)−TE(v). FM selects the free cell with the highest gain that satisfies the balance constraint, determined by the ratio 
𝑅
=
Area of Partition A
Area of Partition A + Area of Partition B
R=
Area of Partition A + Area of Partition B
Area of Partition A
	​

, moves it, marks it as fixed, updates FS, TE, and gains for affected cells, and repeats until all cells are fixed. The cumulative gain over a pass is 
𝐺
𝑀
=
∑
𝑖
=
1
𝑀
Δ
𝐺
𝑖
G
M
	​

=∑
i=1
M
	​

ΔG
i
	​

, and the prefix of moves giving the maximum positive cumulative gain is applied before starting a new pass. The algorithm terminates when no positive cumulative gain remains. FM supports unequal-sized partitions, hypergraphs, and efficient single-cell moves, and its runtime is generally proportional to the number of pins in the design, 
𝑂
(
𝑃
)
O(P).

Overall, VLSI partitioning integrates graph- or hypergraph-based modeling, gain functions, cumulative gains, and constraints to iteratively optimize partitions while minimizing cuts, reducing delays, and maintaining balanced areas. KL provides a foundational method for balanced bipartitioning with node swaps, while FM extends it to single-cell moves, hypergraph structures, and area-based constraints, making it a versatile and practical solution for modern VLSI circuit design.
