# Number of paths with length-K

Using fast exponent.

Assuming the adjacent matrix of this graph is A, and a path with length K, by obtaining $$X = A^K$$ , the number of path from node i to node j is $$X[i][j].$$ 

The calculation of the exponent of a matrix needs $$logK$$ times of multiplication. And the multiplication of a matrix with dimension $$V$$ needs $$V^3$$ times multiplication of numbers. So the total time complexity is $$V^3logK$$ .



```cpp
class Solution {
private:
    int MOD, m;
    vector<vector<int>> mul(vector<vector<int>>& A, vector<vector<int>>& B){
        vector<vector<int>> cur = vector<vector<int>>(m, vector<int>(m, 0));
        for(int i = 0; i < m; i++){
            for(int j = 0; j < m; j++){
                for(int k = 0; k < m; k++)
                    cur[i][j] = (cur[i][j] + (long)A[i][k] * B[k][j] % MOD) % MOD;
            }
        }
        return cur;
    }
public:
    int knightDialer(int N) {
        if(N == 1)
            return 10;
        MOD = pow(10, 9) + 7;
        m = 10;
       vector<vector<int>> mat = {
           {0, 0, 0, 0, 1, 0, 1, 0, 0, 0},
           {0, 0, 0, 0, 0, 0, 1, 0, 1, 0},
           {0, 0, 0, 0, 0, 0, 0, 1, 0, 1},
           {0, 0, 0, 0, 1, 0, 0, 0, 1, 0},
           {1, 0, 0, 1, 0, 0, 0, 0, 0, 1},
           {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
           {1, 1, 0, 0, 0, 0, 0, 1, 0, 0},
           {0, 0, 1, 0, 0, 0, 1, 0, 0, 0},
           {0, 1, 0, 1, 0, 0, 0, 0, 0, 0},
           {0, 0, 1, 0, 1, 0, 0, 0, 0, 0}
       };
        vector<vector<int>> res(m, vector<int>(m, 0));
        for(int i = 0; i < m; i++) res[i][i] = 1;
        // vector<vector<int>> cur = mat;
        // bool isResInit = false;
        --N;
        while(N){
            if(N & 1){
                res = mul(res, mat);
            }
                
            mat = mul(mat, mat);
            N >>= 1;
        }
        int paths = 0;
        for(auto& v : res)
            for(auto& num : v)
                paths = (paths + num) % MOD;
        return paths;
       } 
};
```

