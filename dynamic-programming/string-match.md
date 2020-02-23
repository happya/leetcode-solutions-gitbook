---
description: dynamic programming
---

# String match

One common problem for string match is the **regular expression**.

The idea is to use dynamic programming, for each target pattern, calculate the match cases for all the source strings. That is

> dp\[i\]\[j\]: the match results for the first i characters in source to match the first j characters in the target.

To simplify:

> dp\[i\]\[j\]: the match results for s\[0, i\] and t\[0,j\]

There are two tricky parts:

> initialization of dp
>
> state transfer of dp







### 1. All strings match \(no wildcard\)

#### 115. Distinct sub-sequences



```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int slen = s.length(), tlen = t.length();
        vector<vector<long>> dp(slen + 1, vector<long>(tlen + 1, 0));
        dp[0][0] = 1;
        for(int i = 1; i <= slen; i++){
            // initialize, any string can match nothing
            dp[i][0] = 1;
            for(int j = 1; j <= tlen; j++){
                // at lease have the results of s[0, i-1] match with t[0, j
                dp[i][j] = dp[i - 1][j];
                // if tail character matches
                // the s[0, i - 1] match to t[0, j-1] also valid
                if(s[i - 1] == t[j - 1])
                    dp[i][j] += dp[i - 1][j - 1];
            }
        }
        return (int)(dp[slen][tlen]);
    }
};
```

compression

```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int slen = s.length(), tlen = t.length();
        
        vector<long> dp(tlen + 1, 0);
        dp[0] = 1;
        
        for(int i = 1; i <= slen; i++){
            
            for(int j = tlen; j > 0; j--){
                if(s[i - 1] == t[j - 1])
                    dp[j] += dp[j - 1];
            }
        }
        return (int)(dp.back());
    }
};
```

### 44. Wildcard matching



This time, the pattern be the outer loop is more sensible.

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int plen = p.length();
        int slen = s.length();
        
        vector<vector<bool>> dp(slen + 1, vector<bool>(plen + 1, false));
        dp[0][0] = true;
        
        for(int j = 1; j <= plen; j++){
            char c = p[j - 1];
            // check if first j in p can match empty
            dp[0][j] = dp[0][j - 1] && (c == '*');
            for(int i = 1; i<= slen; i++){
                // * match nothing: dp[i][j - 1]
                // * match anything: dp[i - 1][j]
                if(c == '*')
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
                else
                    dp[i][j] = dp[i - 1][j - 1] && (c == s[i - 1] || c == '?');
            }
        }
        
        return dp[slen][plen];
    }
};
```

compression

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int slen = s.length();
        int plen = p.length();
        
        vector<bool> dp(slen + 1, false);
        dp[0] = true;
        
        for(int j = 1; j <= plen; j++){
            char c = p[j - 1];
            bool pre = dp[0];
            dp[0] = dp[0] && (c == '*');
            for(int i = 1; i <= slen; i++){
                bool tmp = dp[i];
                if(c == '*')
                    dp[i] = dp[i - 1] || dp[i];
                else
                    dp[i] = pre && (c == '?' || c == s[i - 1]);
                pre = tmp;
            }
        }
        return dp[slen];
    }
};
```

