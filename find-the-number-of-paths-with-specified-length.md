---
description: graph and dynamic programming
---

# Find the number of paths with specified length

This problem is not from the leetcode, but I think it is pretty difficult. It combines the graph theory and dynamic programming. The follow up is about how to obtain all the paths. Currently, my solution can only deal with graph \(normally non-directed graph without loops\).



### 1. Description

### 2. codes

```cpp
void dfs(int cur, int from){
        dp[cur][0] = 1;
        path_records[cur][0].push_back(vector<int>{cur});
        for(auto& to : G->adjVertex(cur)){
            if(to != from){
                dfs(to, cur);
                for(int j = 0; j < num; j++) {
                    res += dp[to][j] * dp[cur][num - 1 - j];
                    if(dp[to][j] > 0 && dp[cur][num - 1 - j] > 0){
                        for(auto& fromNext : path_records[to][j]){
                            for(auto& fromCur : path_records[cur][num - 1 - j]){
                                results.push_back(joinPath(fromCur, fromNext));
                            }
                        }
                    }

                }
                for(int j = 1; j <= num; j++) {
                    dp[cur][j] += dp[to][j-1];
                    for(auto& path : path_records[to][j - 1]){
                        vector<int> newPath = path;
                        newPath.push_back(cur);
                        path_records[cur][j].push_back(newPath);
                    }
                }
            }
        }
    }
```



