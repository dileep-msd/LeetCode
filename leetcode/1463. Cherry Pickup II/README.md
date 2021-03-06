# [1463. Cherry Pickup II (Hard)](https://leetcode.com/problems/cherry-pickup-ii/)

<p>Given a <code>rows x cols</code> matrix <code>grid</code> representing a field of cherries.&nbsp;Each cell in&nbsp;<code>grid</code>&nbsp;represents the number of cherries that you can collect.</p>

<p>You have two&nbsp;robots that can collect cherries for you, Robot #1 is located at the top-left corner (0,0) , and Robot #2 is located at the top-right corner (0, cols-1) of the grid.</p>

<p>Return the maximum number of cherries collection using both robots&nbsp; by following the rules below:</p>

<ul>
	<li>From a cell (i,j), robots can move to cell (i+1, j-1) , (i+1, j) or (i+1, j+1).</li>
	<li>When any robot is passing through a cell, It picks it up all cherries, and the cell becomes an empty cell (0).</li>
	<li>When both robots stay on the same cell, only one of them takes the cherries.</li>
	<li>Both robots cannot move outside of the grid at&nbsp;any moment.</li>
	<li>Both robots should reach the bottom row in the <code>grid</code>.</li>
</ul>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<p><strong><img alt="" src="https://assets.leetcode.com/uploads/2020/04/29/sample_1_1802.png" style="width: 139px; height: 182px;"></strong></p>

<pre><strong>Input:</strong> grid = [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]
<strong>Output:</strong> 24
<strong>Explanation:</strong>&nbsp;Path of robot #1 and #2 are described in color green and blue respectively.
Cherries taken by Robot #1, (3 + 2 + 5 + 2) = 12.
Cherries taken by Robot #2, (1 + 5 + 5 + 1) = 12.
Total of cherries: 12 + 12 = 24.
</pre>

<p><strong>Example 2:</strong></p>

<p><strong><img alt="" src="https://assets.leetcode.com/uploads/2020/04/23/sample_2_1802.png" style="width: 284px; height: 257px;"></strong></p>

<pre><strong>Input:</strong> grid = [[1,0,0,0,0,0,1],[2,0,0,0,0,3,0],[2,0,9,0,0,0,0],[0,3,0,5,4,0,0],[1,0,2,3,0,0,6]]
<strong>Output:</strong> 28
<strong>Explanation:</strong>&nbsp;Path of robot #1 and #2 are described in color green and blue respectively.
Cherries taken by Robot #1, (1 + 9 + 5 + 2) = 17.
Cherries taken by Robot #2, (1 + 3 + 4 + 3) = 11.
Total of cherries: 17 + 11 = 28.
</pre>

<p><strong>Example 3:</strong></p>

<pre><strong>Input:</strong> grid = [[1,0,0,3],[0,0,0,3],[0,0,3,3],[9,0,3,3]]
<strong>Output:</strong> 22
</pre>

<p><strong>Example 4:</strong></p>

<pre><strong>Input:</strong> grid = [[1,1],[1,1]]
<strong>Output:</strong> 4
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>rows == grid.length</code></li>
	<li><code>cols == grid[i].length</code></li>
	<li><code>2 &lt;= rows, cols &lt;= 70</code></li>
	<li><code>0 &lt;= grid[i][j] &lt;= 100&nbsp;</code></li>
</ul>


**Related Topics**:  
[Dynamic Programming](https://leetcode.com/tag/dynamic-programming/)

## Solution 1. DP Top-down

It's a typical DP problem. The best answer for a given row index `i`, and column index `a` for the robot #1 and `b` for the robot #2 is fixed, but there are multiple ways to get to this state `i, a, b`, so we can use `dp` to memorize the corresponding answer.

Let `dp(i, a, b)` be the max number of cherries we can get given state `i, a, b`.

```
dp(i, a, b) = sum( dp(i + 1, p, q) | (i+1, p), (i+1, q) are the points reachable from (i, a) and (i, b) )
```

Since there are some states unreacheable at the upper part of the `grid`, using top-down DP can easily skip the computation for those states.

```cpp
// OJ: https://leetcode.com/problems/cherry-pickup-ii/
// Author: github.com/lzl124631x
// Time: O(M * N^2)
// Space: O(M * N^2)
class Solution {
    int m[70][70][70], M, N;
    int dp(vector<vector<int>> & A, int i, int a, int b) {
        if (i == M) return 0;
        if (m[i][a][b] != -1) return m[i][a][b];
        int v = a == b ? A[i][a] : (A[i][a] + A[i][b]);
        int ans = 0;
        for (int x = -1; x <= 1; ++x) {
            for (int y = -1; y <= 1; ++y) {
                int p = a + x, q = b + y;
                if (p < 0 || q < 0 || p >= N || q >= N) continue;
                ans = max(ans, dp(A, i + 1, p, q));
            }
        }
        return m[i][a][b] = v + ans;
    }
public:
    int cherryPickup(vector<vector<int>>& A) {
        M = A.size(), N = A[0].size();
        memset(m, -1, sizeof(m));
        return dp(A, 0, 0, N - 1);
    }
};
```

## Solution 2. DP

```cpp
// OJ: https://leetcode.com/problems/cherry-pickup-ii/
// Author: github.com/lzl124631x
// Time: O(M * N^2)
// Space: O(N^2)
class Solution {
public:
    int cherryPickup(vector<vector<int>>& A) {
        int M = A.size(), N = A[0].size(), ans = 0;
        vector<vector<int>> d(N, vector<int>(N, -1)), e(N, vector<int>(N));
        d[0][N - 1] = A[0][0] + A[0][N - 1];
        for (int i = 1; i < M; ++i) {
            for (int j = 0; j < N; ++j) {
                for (int k = j + 1; k < N; ++k) e[j][k] = -1;
            }
            for (int j = 0; j < N; ++j) {
                for (int k = j + 1; k < N; ++k) {
                    if (d[j][k] < 0) continue;
                    for (int a = -1; a <= 1; ++a) {
                        int s = j + a;
                        if (s < 0 || s >= N) continue;
                        for (int b = -1; b <= 1; ++b) {
                            int t = k + b;
                            if (t < 0 || t >= N) continue;
                            int val = s == t ? A[i][s] : (A[i][s] + A[i][t]);
                            e[s][t] = max(e[s][t], d[j][k] + val);
                        }
                    }
                }
            }
            swap(d, e);
        }
        for (int j = 0; j < N; ++j) {
            for (int k = j + 1; k < N; ++k) ans = max(ans, d[j][k]);
        }
        return ans;
    }
};
```