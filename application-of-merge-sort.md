# application of merge-sort

### 315:  Count of smaller number after self

```cpp
class Solution {
private:
    vector<vector<int>> records;
    void sort_counts(int l,int m, int r, vector<int>& res){
        for(int i=l, j=m;i<m;i++){
            while(j < r && records[j][0] < records[i][0])
                j++;
            res[records[i][1]] += (j - m);  
        }
            
        inplace_merge(records.begin() + l, records.begin() +m , records.begin() + r);
    }
public:
    vector<int> countSmaller(vector<int>& nums) {
        int n = nums.size();
        vector<int> res(n, 0);
        
        for(int i = 0; i < n; i++)
            records.push_back(vector<int>({nums[i], i}));
        
        for(int size = 1; size < n; size += size){
            for(int lo = 0; lo + size < n; lo=lo+size + size){
                sort_counts(lo, lo + size, min(n, lo + size * 2), res);
            }
            
        }
        return res;
    }
};
```

### 493. Reverse pairs

```cpp
class Solution {
private:
    int sort_count(int l, int m, int r, vector<int>& nums){
        int count = 0;
        for(int i = l, j = m; i < m; i++){
            while(j < r && nums[i] > 2L * nums[j]) j++;
            count += (j - m);
            // cout<<m-l<<": "<<count<<endl;
        }
        inplace_merge(nums.begin() + l, nums.begin() + m, nums.begin() + r);
        return count;
    }
public:
    int reversePairs(vector<int>& nums) {
        int n = nums.size();
        int count = 0;
        for(int sz=1; sz < n; sz+=sz){
            for(int l = 0; l + sz < n; l += sz*2){
                count += sort_count(l, l+sz,min(n, l + sz + sz), nums);
            }
        }
        return count;
    }
};
```

### 327. Count of range sum

```cpp
class Solution {
private:
    vector<long> sum;
    int sort_count(int l, int m, int r, int lower, int upper){
        int i, j1, j2;
        int count = 0;
        for(i = l, j1 = j2 = m; i < m; i++){
            while(j1 < r && sum[j1] - sum[i] < lower) j1++;
            while(j2 < r && sum[j2] - sum[i] <= upper) j2++;
            count += (j2 - j1);
        }
        inplace_merge(sum.begin() + l, sum.begin() + m, sum.begin() + r);
        return count;
    }
public:
    int countRangeSum(vector<int>& nums, int lower, int upper) {
        int n = nums.size();
        sum = vector<long>(n + 1, 0);
        
        //sum[i]: the sum of nums[0...i-1]
        // sum[j] - sum[i] = sum of nums[i,..., j-1], i.e. [i, j)
        for(int i = 0; i < n; i++)
            sum[i+1] = sum[i] + nums[i];
        
        
        int count = 0;
        for(int sz=1; sz < n + 1; sz+=sz){
            for(int l = 0; l + sz <= n; l += sz*2)
                count += sort_count(l, l + sz, min(l+sz*2, n+1), lower, upper);
        }
        
        return count;
        
    }
};
```

