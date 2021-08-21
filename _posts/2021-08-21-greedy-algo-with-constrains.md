---
layout: post
title: "[greedy] greedy question with ddl as constrain"
description: "leetcode 630"
comments: true
keywords: "greedy, algoithm"
---

       // greedy question;
        // without ddl, sort and talk the course with less time first
        // with ddl, we should consider the course with earlier ddl first
        // if we can take this course, take it first
        // if we cannot this course (miss ddl);
        //      whether we can find a better course
        //      if it it better, than the most time expensive course: replace it
        //      if not, discord this course.

```C++
class Solution {
public:
    int scheduleCourse(vector<vector<int>>& courses) {
        // greedy question;
        // without ddl, sort and talk the course with less time first
        // with ddl, we should consider the course with earlier ddl first
        // if we can take this course, take it first
        // if we cannot this course (miss ddl);
        //      whether we can find a better course
        //      if it it better, than the most time expensive course: replace it
        //      if not, discord this course.
        sort(std::begin(courses), std::end(courses), 
             [](const auto& c1, const auto& c2)
             {return c1[1] < c2[1];});
        priority_queue<int> q;
        int cur_time = 0;
        for(auto& course : courses){
            if(cur_time + course[0] <= course[1]){
                cur_time += course[0];
                q.push(course[0]);
            }else if(!q.empty() && q.top() > course[0]){
                cur_time -= (q.top() - course[0]);
                q.pop();
                q.push(course[0]);
            }
        }
        return q.size();
        
    }
};
```