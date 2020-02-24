# Quick partition

### 215. K-th largest in an array

```cpp
class Solution {
private:
    int partition(vector<int>& nums, int left, int right){
        if(left >= right)
            return left;
        //[left, i] <= pivot
        //[i+1, right] > pivot
        int r = rand() % (right - left + 1) + left;
        swap(nums[r], nums[left]);
        int i(left), j(left + 1), pivot(nums[left]);
        for(;j <= right; j++){
            if(nums[j] <= pivot)
                swap(nums[++i], nums[j]);
        }
        swap(nums[i],nums[left]);
        return i;
    }
    int quickselect(vector<int>& nums, int i, int j, int k){
        // if(i == j)
        //     return nums[i];
        int pvt = partition(nums, i, j);
        if(pvt == k)
            return nums[pvt];
        if(pvt < k)
            return quickselect(nums, pvt + 1, j, k);
        return
            quickselect(nums, i, pvt - 1, k);
    }
public:
    int findKthLargest(vector<int>& nums, int k) {
        return quickselect(nums, 0, nums.size() - 1, nums.size() - k);
    }
};
```

