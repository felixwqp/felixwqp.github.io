---
layout: post
title: "emplace_back v.s. push_back"
description: ""
comments: true
keywords: ""
---


what's the difference between emplace_back and push_back?

the real C++0x form of emplace_back is really useful: void emplace_back(Args&&...);

it takes a variadic list of arguments, so that means that you can now perfectly forward the arguments and construct directly an object into a container without a temporary at all.

