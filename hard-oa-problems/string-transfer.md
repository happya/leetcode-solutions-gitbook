# String transfer

### 1. Description

This problem is collected from OA problems, so the description may not be the exactly same as the original problems.

> Given two strings, a and b, with same length, calculate the steps need to convert from a to b.
>
> 1. A conversion step means covert all the same characters in a to anther characters, for example, from "aab" to "ccb", all the "a"s are converted to "c".
> 2. Intermediate steps are allowed. What's that mean? For example, if source="abb" and target="baa", we can use a intermediate character "c", to first change "abb" into "cbb", then change "b" to "a" and get "caa", and the final step is to change "c" to "b".
> 3. The third requirements is from myself's comprehension, that is, the intermediate characters can only use a-z.

### 2. Solutions

Configure a graph, which maps the characters in the source to that in the target. 

> 1-to-multiple: If one character are mapped to multiple characters, the conversion is impossible.
>
> No circle. The conversion steps is just the number of paths in the graph. 
>
> Circles. Each circle needs a intermediate step. If the circle numbers are too many, then there will not be enough intermediate characters, then the conversion is impossible. If there are multiple connected components in the graph, those who have sink nodes can use these nodes as intermediate characters.
>
> Continue the last part. Since no nodes with out-degree larger than 1 are allowed, the component with sink node must be acyclic. This can be easily verified.
>
> If one circle can be break, then it have sink node and is able to break other circles. So if all components are cyclic, one new character is needed; else, no more new character is needed.
>
> If the number of unique characters in source is equal to 26 \(used all the characters\), there must exist circle. In other cases, there must be enough intermediate characters to do the conversion, because it is either used less characters or acyclic. So we only have to check whether the source has used all the characters.

### 3. Codes

```cpp
//
// Created by yyi on 2020/2/14.
//

#ifndef GRAPHTRAVERSE_STRINGTRANSFER_H
#define GRAPHTRAVERSE_STRINGTRANSFER_H

#include <iostream>
#include <set>
#include <unordered_map>
#include <cassert>
#include <vector>

using namespace std;
class StringTransfer {
private:
    string source; // to transfer
    string target; // transfer target
    unordered_map<char, set<char>> G;
    unordered_map<char, int> visited;
    int circles; // number of circles
    int paths; // number of paths
    int numOfCharacters;
    int uniqueCharSource;

    void createGraph(){
        int len = source.length();
        for(int i = 0; i < len; i++){
            G[source[i]].insert(target[i]);
        }
        uniqueCharSource = G.size();
        for(auto& c : source)
            visited[c] = -1;
        for(auto& c : target)
            visited[c] = -1;
    }

    void dfs(char cur){
       if(visited[cur] == 0){
           // find circle
           circles++;
           return;
       }
       if(visited[cur] == -1){
           visited[cur] = 0;
           for(auto& w : G[cur]){
               paths++;
               dfs(w);
           }
           visited[cur] = 1;
       }
    }
public:
    StringTransfer(string& _source, string& _target) : source(_source), target(_target){
        assert(source.length() == target.length());
        createGraph();
        circles = 0;
        paths = 0;
        numOfCharacters = 4;

        for(auto iter = G.begin(); iter != G.end(); iter++){
            if(visited[iter->first] == -1){
                dfs(iter->first);
            }

        }
    }
    int findCircles(){
        return circles;
    }
    int findPaths(){
        return paths;
    }
    int conversionTimes(){
        // if exists nodes with out-degree > 1, impossible to convert
        for(auto iter = G.begin(); iter != G.end(); iter++){
            if(iter->second.size() > 1)
                return -1;
        }
        if(uniqueCharSource >= numOfCharacters)
            return -1;
        return circles + paths;
    }

};


#endif //GRAPHTRAVERSE_STRINGTRANSFER_H

```

