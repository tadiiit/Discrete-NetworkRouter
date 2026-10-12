# Discrete-NetworkRouter

Discrete-NetworkRouter is a high-performance, low-latency graph evaluation engine engineered in modern C++20. Traditional graph structures rely heavily on node pointers (Node*) and nested vectors (std::vector<std::vector<int>>), which introduce significant memory overhead, pointer-chasing, and severe CPU cache misses.

This project maps directed graph topologies into contiguous, bit-packed adjacency matrices. By shifting from pointer operations to hardware-accelerated bitwise instructions (AND, OR, POPCNT, CTZ), Discrete-NetworkRouter achieves $O(1)$ edge verification and sub-microsecond multi-hop reachability evaluation.

+-----------------------------------------------------------------------------------+
|                            GRAPH ADJACENCY DATA FLOW                              |
+-----------------------------------------------------------------------------------+

 Traditional Pointer Graph (Cache-Unfriendly)
 [Node A] ---> Pointer ---> [Node B] ---> Pointer ---> [Node C]  (Memory Fragmentation)

 Discrete-NetworkRouter Bit-Packed Matrix (Contiguous L1/L2 Cache Block)
 +---------------------------------------------------------------------------------+
 | Row A (64-bit uint64_t mask): [0 | 1 | 0 | 1 | ... | 0]                          |
 | Row B (64-bit uint64_t mask): [1 | 0 | 0 | 0 | ... | 1]                          |
 +---------------------------------------------------------------------------------+
                                      |
                                      v
                   SIMD / Bitwise Hardware Intrinsics
              [__builtin_ctzll() / __builtin_popcountll()]
                                      |
                                      v
                     Sub-6 Microsecond Path Evaluation
