# Trie

### 212. Word search

```cpp
class TrieNode{
public:
    bool is_end;
    vector<TrieNode*> children;
    TrieNode(){
        is_end = false;
        children = vector<TrieNode*>(26, NULL);
    }
    
};

class Trie{
private:
    TrieNode* root;
    void addWord(string& word){
        auto node = root;
        for(auto& ch : word){
            int idx = ch - 'a';
            if(!node->children[idx])
                node->children[idx] = new TrieNode();
            node = node->children[idx];
        }
        node->is_end = true;
    }
public:
    
    Trie(vector<string>& words){
        root = new TrieNode();
        for(auto& word : words){
            addWord(word);
        }
    }
    ~Trie() { delete root; }
    TrieNode* getRoot(){ return root; }
    
};
class Solution {
private:
    int R, C;
    int d[4][2] = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
    bool inArea(int x, int y){
        return (x >=0 && x < R && y >= 0 && y < C);
    }
    set<string> res;
    void dfs(vector<vector<char>>& board, int x, int y, TrieNode* node, string word){
        char cur = board[x][y];
        if(!isalpha(cur))
            return;
        if(node->children[cur - 'a']){
            node = node->children[cur - 'a'];
            word += cur;
            if(node->is_end)
                res.insert(word);
            board[x][y] ^= 255;
            for(int i = 0; i < 4; i++){
                int newX = x + d[i][0];
                int newY = y + d[i][1];
                if(inArea(newX, newY))
                    dfs(board, newX, newY, node, word);
            }
            board[x][y] ^= 255;    
        }
    }
    
public:
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        
        auto trie = new Trie(words);
        auto root = trie->getRoot();
        R = board.size();
        C = board[0].size();
        for(int i = 0; i < R; i++){
            for(int j = 0; j < C; j++){
                dfs(board, i, j, root, "");
            }
        }
        return vector<string>(res.begin(), res.end());
    }
};
```

