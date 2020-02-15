# Morris Traversal

Tree traversal may be one of the most common topics. However, the recursive method is trivial, can we do it iterative?

Intuitively, since the recursion is naturally linked to stack, so we can use stack to do an iterative tree traversal.



#### inorder

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> st;
        
        TreeNode* cur = root;
        
        while(cur || !st.empty()){
            if(cur){
                st.push(cur);
                cur = cur->left;
            }
            else{
                TreeNode* parent = st.top();
                res.push_back(parent->val);
                st.pop();
                if(parent->right){
                    cur = parent->right;
                }
            }
            
        }
        return res;
    }
};
```

#### post-order

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        TreeNode* cur = root;
        stack<TreeNode*> st;
        TreeNode* last = NULL;
        vector<int> res;
        
        while(cur || !st.empty()){
            if(cur){
                st.push(cur);
                cur = cur->left;
            }
            else{
               TreeNode* parent = st.top();
                // if has right child and right has not been explored
                if(parent->right && parent->right != last){
                    cur = parent->right;
                }
                else{
                    // both left and right has been explored
                    // cur node is finished
                    res.push_back(parent->val);
                    last = parent;
                    st.pop();
                }
            }
        }
        return res;
    }
};
```

The complexity of such iteration is:

> Space: $$O(N)$$ 
>
> Time: $$O(N)$$

Now, can we do better by using only $$O(1)$$ time? The answer is yes and the algorithm is the Morris traversal.



The essence of Morris traversal is to build the link. For example, in in-order traversal, the previous node for a node `cur` is the rightmost node of `cur's`left-sub-tree. So we can build this link as this, for each node:

> If it has left child, we all find the rightmost node of its left sub-tree, and make the current node as the rightmost node's right child;
>
> If it has no left child, means this node can be pressed, and we continue to process its right child.

The are some invariants that might be tricky:

> If a visited node is being linked, means it is marked as the right child of its left-sub-tree's rightmost node, then it means its left-sub-tree is already been traversed, we can process this node and go to its right sub-tree.

#### in-order

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        TreeNode* cur = root;
        while(cur){
            if(!cur->left){
                res.push_back(cur->val);
                cur = cur->right;
            }
            else{
                TreeNode* pre = cur->left;
                while(pre->right && pre->right != cur){
                    pre = pre->right;
                }
                // if not linked
                if(!pre->right){
                    pre->right = cur;
                    cur = cur->left;
                }
                else{
                    res.push_back(cur->val);
                    pre->right = NULL;
                    cur = cur->right;
                }
            }
        }
        return res;
    }
};
```

The post-order case is even more complicated than the in-order case. The "finding rightmost node in one-node's left-sub-tree" is the same, but the process order should be different. So the changed part is actually the invariants:

> If a visited node is been linked, then it means only its left child's rightmost path has not been visited. So we shall only add the reverse order of that path to the results.

#### Post-order

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    vector<int> res;
    
    void getRightPathNodes(TreeNode* s, TreeNode* t){
        TreeNode* prev = NULL;
        TreeNode* cur = s;
        while(cur != NULL){
            TreeNode* next = cur->right;
            cur->right = prev;
            prev = cur;
            cur = next;
        }
        while(prev != NULL){
            res.push_back(prev->val);
            prev = prev->right;
        }
    }
    
public:
    vector<int> postorderTraversal(TreeNode* root) {
        TreeNode* dummy = new TreeNode(0);
        dummy->left = root;
        
        TreeNode* cur = dummy;
        
        while(cur != NULL){
            if(!cur->left){
                cur = cur->right;
                continue;
            }
            TreeNode* pre = cur->left;
            // find the rightmost node of cur's left subtree
            while(pre->right != NULL && pre->right != cur)
                pre = pre->right;
            // if this pre node is not linked before, do the link
            // link the cur node as the pre node's right child
            // for tracing back
            if(!pre->right){
                pre->right = cur;
                cur = cur->left; // cur goes to left to do iteration
            }
            // if such link is already done, do the traversal output
            else {
                pre->right = NULL; // release link
                // goes back to cur means cur's left child's left substree is already traversed
                // get the traverse path for cur's left child's right subtree
                getRightPathNodes(cur->left, pre);
                // done with cur's left subtree, to right
                cur = cur->right;
                
            }
            
        }
        return res;
    }
};
```

