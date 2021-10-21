---
layout: post
title:  "Complex is simple once understood"
date:   2021-10-20
categories: jekyll update
---

### Followup on rvalues
A refresh on rvalue and move semantics over a few days, improved the understanding enough to make them comfortable to use.
- The default move implementation will copy each member, but not clear them in the instance being moved.  
This is very fine as long as no members are raw pointers. In that case, only the raw pointers need to be cleared in the moved instance.

- An rvalue argument to a function such as "Control&& other", will be considered to be an lvalue, and so must be cast back to && if being passed along another move function.  
std::move will cast only, and not call any move ctor/assigment just yet.  
Therefore, this code is perfectly fine and efficient


    ButtonControl::ButtonControl(ButtonControl&& other) noexcept :
    	Control{std::move(other)} {}

- With the improved understanding, storing objects by values can now be done comfortably.

### Code Review on StackExchange
This very nice review helped a lot, and the suggested improvements are to be implemented.  
[https://codereview.stackexchange.com/questions/269056/win32-ui-with-tabs-and-buttons](https://codereview.stackexchange.com/questions/269056/win32-ui-with-tabs-and-buttons)
