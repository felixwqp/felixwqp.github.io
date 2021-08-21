---
layout: post
title: "[graph] BFS/DFS for directed graph question[WIP]"
description: "leetcode 207"
comments: true
keywords: "graph, algoithm"
---



apply in-degree as the key to judge whether the course has been taken.
```C++
    bool canFinishBFS(int numCourses, vector<vector<int>>& prerequisites) {
        // basic topo sorting;
        // edge: vec[0] <- vec[1]
        // construct graph, with [pointing_idx] = {pointed_idx}
        vector<list<int>> graph(numCourses);
        // for(auto& list: graph){
        //     list.reserve(numCourses);
        // }
        // construct indegree counter vector
        vector<int> in_degrees(numCourses, 0);
        for(const auto& edge: prerequisites){
            const int& pointing_idx = edge[1];
            const int& pointed_idx = edge[0];
            graph[poin$$ting_idx].push_back(pointed_idx);
            in_degrees[pointed_idx]++;
        }
        
        // push the first course to be taken to the bfs queue
        queue<int> q;
        int count = 0;
        for(int idx = 0; idx < numCourses; idx++ ){
            if(in_degrees[idx] == 0){ q.push(idx); count++;}
        }
        // take course from the queue;
        
        while(!q.empty()){
            auto taken_idx = q.front();
            q.pop();
            for(auto& take_idx: graph[taken_idx]){
                if(--in_degrees[take_idx] == 0) {q.push(take_idx); count++;}
            }
        }
        
        return (count == numCourses);
        
    }
```