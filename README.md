# Maximum-Score-of-Non-overlapping-Intervals

You are given a 2D integer array intervals, where intervals[i] = [li, ri, weighti]. Interval i starts at position li and ends at ri, and has a weight of weighti. You can choose up to 4 non-overlapping intervals. The score of the chosen intervals is defined as the total sum of their weights.

Return the lexicographically smallest array of at most 4 indices from intervals with maximum score, representing your choice of non-overlapping intervals.

Two intervals are said to be non-overlapping if they do not share any points. In particular, intervals sharing a left or right boundary are considered overlapping.

Constraints:

1 <= intevals.length <= 5 * 10^4
intervals[i].length == 3
intervals[i] = [li, ri, weighti]
1 <= li <= ri <= 10^9
1 <= weighti <= 10^9

Intuition
We need to pick up to 4 non-overlapping intervals to maximize the sum of their weights. Since two intervals cannot share any points (even boundaries), an interval starting at li can only follow an interval ending at r j<li.

Because we need to pick at most 4 items and tie-break by the lexicographically smallest tuple of original indices, standard dynamic programming must track both the maximum weight and the lexicographically smallest sequence of original indices that produces that weight.

Approach
Sort & Index Mapping:
Sort intervals primarily by their start position li.
Keep track of their original indices before sorting to reconstruct the result.

Binary Search (Precomputation):
For each interval i, find the first interval j>i such that l 
j>ri. We can use binary search (std::upper_bound / bisect_right) on the sorted start times.

Dynamic Programming
State:Define dp[i][k] as a pair (max_weight, index_list) representing the best choice picking at most k intervals from the suffix starting at index i.

Option 1 (Skip interval i): dp[i+1][k]
Option 2 (Take interval i): weight i +dp[next_j][k−1], adding orig_indexi to the list.

Tie-Breaking Rule:
When comparing options, prefer the higher weight. If weights are equal, prefer the lexicographically smaller sequence of original indices (sorted).

Suffix DP Evaluation:
Compute dp[i][k] from right to left (i=N−1…0) for k=1…4. The final answer is found at dp[0][4].

Complexity
Time complexity:
O(NlogN) - Sorting takes O(NlogN) and binary search for each interval takes O(logN). DP transitions take O(1) time per state since k≤4.

Space complexity:
O(N) - Storing the DP table and intervals structure.


import bisect

class Solution(object):
    def maximumWeight(self, I):
        A = sorted((l, r, w, i) for i, (l, r, w) in enumerate(I))
        S, n = [x[0] for x in A], len(A)
        dp = [[(0, ())] * 5 for _ in range(n + 1)]

        for i in range(n - 1, -1, -1):
            l, r, w, id = A[i]
            nxt = bisect.bisect_right(S, r)
            for k in range(1, 5):
                bw, bids = dp[i + 1][k]
                pw, pids = dp[nxt][k - 1]
                tw, tids = pw + w, tuple(sorted(pids + (id,)))
                dp[i][k] = (tw, tids) if tw > bw or (tw == bw and (not bids or tids < bids)) else (bw, bids)

        return list(dp[0][4][1])

