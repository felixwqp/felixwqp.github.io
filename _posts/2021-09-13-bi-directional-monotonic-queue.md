---
layout: post
title: "[monotonic queue]  bidirection"
description: "simple implementation of monotonic queue"
comments: true
keywords: "leetcode, monotonic queue"
---

Find the minimal amplitude after consecutive K removal?
simple implementation of monotonic queue
```C++
template<typename T>
class MQ{
public: 
    void push(T val){
        while(!maxDq.empty() && val > maxDq.back()){
            maxDq.pop_back();
        }
        maxDq.push_back(val);

        while(!minDq.empty() && val < minDq.back()){
            minDq.pop_back();
        }
        minDq.push_back(val);
    }
    T min(){
        // require not empty
        return minDq.front();
    }
    T max(){
        // require not empty
        return maxDq.front();
    }
    void pop_max(){
        maxDq.pop_front();
    }
    void pop_min(){
        minDq.pop_front();
    }
private:
    deque<T> maxDq;
    deque<T> minDq;
    // can be same; 
};

int solve(vector<int>& nums, int k) {
    int res = INT_MAX;
    // k == 0; 
    MQ<int> q;
    for(int i = 0; i < nums.size() - k; i++){
        q.push(nums[i]);
        cout << q.max() << ": "<< q.min() << endl;
        // res = min(res, q.max() - q.min());
    }
    res = min(res, q.max() - q.min());
    for(int i = 0; i < k; i++){
        q.push(nums[i + nums.size() - k]);
        int tmp_max = q.max();
        int tmp_min = q.min();
        cout <<"before: "<< q.max() << ": "<< q.min() << endl;
        
        if(tmp_max == nums[i]) q.pop_max();
        if(tmp_min == nums[i]) q.pop_min();
        cout <<"after: "<<  q.max() << ": "<< q.min() << endl;
        res = min(res, q.max() - q.min());
    }
    return res;
}

```