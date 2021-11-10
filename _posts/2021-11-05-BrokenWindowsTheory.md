---
layout: post
title:  "Broken windows theory"
date:   2021-11-05
categories: jekyll update
---

### The broken windows theory
Wikipedia on the [Broken windows theory](https://en.wikipedia.org/wiki/Broken_windows_theory)  
[Richard Tuin relating it to software development](https://www.rtuin.nl/2012/08/software-development-and-the-broken-windows-theory/#:~:text=Software%20Development%20and%20the%20Broken%20Windows%20Theory%20There,of%20a%20broken%20window%20in%20an%20abandoned%20building.).

The theory state that leaving a window broken on a building will show signs of neglect and encourage further neglect to occur.
The same can be applied to software development.

When a code smell is smelt, if it is accepted or kept for later, it may make it easier to accept the next code smell to come, since it was already smelling bad anyways. 
This bad smell can cascade and let the code become so unsatisfactory that the motivation to further develop it is negatively affected.

While fixing every broken windows along the way can slow development in the short term, it should make it faster in the long term.

Is you dislike something, better fix it sooner rather than later, less you accept the state of things are they are and lower your standards.

### Assert

#### Remember, in Release mode assert() does nothing
SHOUJIN_ASSERT functions are meant to run always, since they encapsulate the whole expression and not just a return code.
This is done in order to capture the expression string and include it in the error detail.

The NDEBUG preprocessor condition replacing them for assert() in release mode is longer present.
To add it back, the expression will have to be evaluated rather then removed.

#### Code duplication is not good, therefore it is bad.
- Made __SHOUJIN_ASSERT__ sub macros
- Made struct ErrorInfo

### Window_IsNotCopyConstructible
is_copy_constructible_v does not behave as expected, and the corresponding unit test fails.

This is the question on stackoverflow  
[is-copy-constructible-typetrait-gives-wrong-answer](https://stackoverflow.com/questions/69848029/is-copy-constructible-typetrait-gives-wrong-answer)

From the answers, a const specified is missing from copy constructor and copy assignment declarations.  
`Window(Window&) = delete;` is wrong  
`Window(const Window&) = delete;` is good
