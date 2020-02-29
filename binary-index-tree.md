# binary index tree

#### 1. prefix sum

The binary index tree\(BIT\) allows for solving prefix sum problems efficiently. 

Originally, the prefix sum is:

```cpp
sum[i] = a[0] + a[1] + ... + a[i]
```

If we want to update a\[i\], all the sum\[j\] for j &gt;= i has to be updated. Now we take the advantage of bit manipulation, and configure a new type of prefix sum:

```cpp
// i = 0010100b
tree[i] = a[0010100b] + a[0010011b] + a[0010010b] + a[0010001b]
```

Now the tree\[i\] represent the sum with respect to the binary representation of i, it is the sum of a\[i\] to a\[j\]. How to define j? 

> j is the number which set i's lowest bit 1 to zero.

How to get j?

```cpp
j = x - (x & (-x));
```

#### 2. update and query

If we update a\[i\], how does it affect the array of `tree`? Let's check the binary representation of i, say it has the following binary representation

```text
i = 0010101b;
```

It would only the following tree elements:

```text
tree[0010101b]; // i
tree[0010110b]; // isolate the last bit 1 and add to index
tree[0011000b]; // isolate the last bit 1 and add to index
tree[0100000b]; // if this value is in the valid range
```

Therefore, if we update a\[i\],

```cpp
void update(int i, int val){
    for(; i <= n; i += (i & (-i)))
        tree[i] += val;
}
    
```

If we want to query sum\[i\],

```cpp
int query(int i){
    int sum = 0;
    for(; i > 0; i -= (i & (-i)))
        sum += tree[i];
}
```

