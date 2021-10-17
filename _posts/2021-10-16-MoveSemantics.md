---
layout: post
title:  "Move semantics and rvalue"
date:   2021-10-16
categories: jekyll update
---

### Move semantics and rvalue

The reasoning is, since this is C++ and not C, then move semantics and rvalue references might as well be used.  
Operator new will not have to be used, and the control collection (std::map) will store entire objects instead of pointers.

*As this blog is written, a question comes to mind; Is the std::map internal storage even linear?  
It seems like it is not, unlike the std::vector.

And so rvalue are in place, with all extra for the move constructors and move assignment operators.  
Adding child controls can be be done like so
```C++
		//Notice that there are no 'new' operator used.
		control.AddChild(EditControl{{10, y += 30}, {200, 20}, TEXT("New Edit Control")});
```

In the end is it really worth it?  
Compared to simply using pointers, a lot more code is needed with a lot more opportunities for errors.  
What are the benefits? No worries about deleting instances and the controls would be stored linearly in memory is they were not in a map, a they are in a map.  
This might not be a good scenario for move semantics.

More reading on C++ rvalue is needed to answer those questions.

### Why even use C++ over C?

When using C, the time is spent on writing code to implement ideas.  
When using C++, the time is spent on wondering about which language features to use, or not.  
Programming C++ seems to result more into forever learning more about C++ rather than focusing on results.

But then using C, the lack of inheritance, destructor, *this* pointers, containers and templates is disappointing.

That list is quite long, so maybe it's good to use minimal C++ and not worry too much about it not being *modern C++*
