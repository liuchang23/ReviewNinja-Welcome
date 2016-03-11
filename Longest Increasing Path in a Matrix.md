# Question

Given an integer matrix, find the length of the longest increasing path.

From each cell, you can either move to four directions: left, right, up or down. You may NOT move diagonally or move outside of the boundary (i.e. wrap-around is not allowed).

Example 1:

```
nums = [
  [9,9,4],
  [6,6,8],
  [2,1,1]
]
```

Return 4

The longest increasing path is [1, 2, 6, 9].

Example 2:
```
nums = [
  [3,4,5],
  [3,2,6],
  [2,2,1]
]
```

Return 4

The longest increasing path is [3, 4, 5, 6]. Moving diagonally is not allowed.

# Solution

> DFS:O(4^n) time, O(1) space

*note*: this solution is TLE.

We could use two-dimensions array `dp` to storage that the maxmium length of path start from node(i,j). For example1, dp[0][0] is 1, dp[2][2] is 4. It's easy to know that: `dp[i][j]` will be `dp[i+1][j] + 1` if nums[i][j] < nums[i+1][j]. this rule is also satisfied with nums[i-1][j], nums[i][j-1], nums[i][j+1]（they are adjacent nodes)。
.

So, We could use DFS algorithm as following:

```cpp
class Solution {
public:
    int dfs(const vector<vector<int> >& matrix, int x, int y)
    {
        static int dx[] = {0, 0, -1, 1};
        static int dy[] = {-1, 1, 0, 0};

        int ret = 0;
        for (int i=0; i<4; ++i)
        {
            int px = x + dx[i];
            int py = y + dy[i];
            if (px < 0 || px >= matrix.size() || py < 0 || py >= matrix[0].size()) continue;
            if (matrix[x][y] < matrix[px][py])
                ret = max(ret, dfs(matrix, px, py));
        }
        return ret + 1;
    }

    int longestIncreasingPath(vector<vector<int> >& matrix) {
        int ans = 0;

        for (int i=0; i< matrix.size(); ++i)
            for (int j=0; j<matrix.size(); ++j)
            {
                ans = max(ans, dfs(matrix, i, j));
            }
        return ans;
    }
};

```

> memorize search: O(n) time, O(n) space, n is nodes number.

In DFS solution, program has a lot of duplicated useless function call. For example2, dfs(0,0) and dfs(1,1) are call dfs(0, 1). Then, We could cache the result of dfs(0,1), and just return the result directly. Accrodingly we only need one function call for each node.

simple solution for references:

```cpp
#include <vector>
#include <iostream>
#include <algorithm>
using namespace std;

class Solution {
public:
    int dfs(const vector<vector<int> >& matrix, int x, int y, vector<vector<int> >& cached)
    {
        if (cached[x][y] != -1)
            return cached[x][y];

        static int dx[] = {0, 0, -1, 1};
        static int dy[] = {-1, 1, 0, 0};

        int ret = 0;

        for (int i=0; i<4; ++i)
        {
            int px = x + dx[i];
            int py = y + dy[i];

            if (px < 0 || px >= matrix.size() || py < 0 || py >= matrix[0].size()) continue;
            if (matrix[x][y] < matrix[px][py])
                ret = max(ret, dfs(matrix, px, py, cached));
        }
        cached[x][y] = ret + 1;

        return ret + 1;
    }

    int longestIncreasingPath(vector<vector<int> >& matrix) {
        int ans = 0;

        vector<vector<int> > cached(matrix);
        for (int i=0; i<cached.size(); ++i)
            for (int j=0; j<cached[0].size(); ++j)
                cached[i][j] = -1;

        for (int i=0; i< matrix.size(); ++i)
            for (int j=0; j<matrix[0].size(); ++j)
            {
                ans = max(ans, dfs(matrix, i, j, cached));
            }

        return ans;
    }
};
```
