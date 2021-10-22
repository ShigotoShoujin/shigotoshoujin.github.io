---
layout: post
title:  "Copy and swap idiom"
date:   2021-10-21
categories: jekyll update
---

### Further lore sought
The quest for knowledge continues with the discovery of copy and swap.

From the desire to avoid duplicated code, from the experiments on creating member move functions to be called from both the move constructor and the move assignment, from the code review on stackexchange, further lore is sought, and the following is uncovered.

- [Copy and swap explained](https://stackoverflow.com/a/3279550/17172063)
- [ADL - Argument Dependent Name Lookup](https://en.m.wikipedia.org/wiki/Argument-dependent_name_lookup)
- [Why make the swap function friend](https://stackoverflow.com/questions/5695548/public-friend-swap-member-function)

### Copy and swap with move assignment
Copy constructor and copy assignment are solved using the copy and swap idiom. Move constructor is trivial if reusing the copy constructor.  
But what of the move assignment, and what of when the class is to be not copyable?  
Further research is in order, but it should be easy enough, given the recently acquired intelligence.

### Supplementary stumbling block with win32 window system
The current implementation have the Window class used for both application classes and system classes, in the context of [window classes](https://docs.microsoft.com/en-us/windows/win32/winmsg/about-window-classes)  
Because system classes are by definition already registered, messages are sent to their own system default WndProc and not to this Window class WndProcStatic function.

One theoritical solution is to systematically subclass the system classes and force them to call WndProcStatic, allowing for common behavior between all types of windows.  
This solution is now disqualified, following the discovery that the modern win32 subclassing function are asking for the hwnd, and so the call to CreateWindowEx has to return before the subclassing can be done, which would make this solution become even more complex.

Taking a few steps back, the whole idea of creating the window handle right in the constructor, with no parent window and no WS_CHILD style, to right after add the window to a parent using AddControl, change its parent and reset its size with the new style, is becoming too unreasonable.  
As much as the desire not to copy the .Net forms and create the handle on the fly when it's needed, the current design will not allow for a nice, simple library.

Instead let's try for this design next
- The Window constructor will record the CreateInfo passed to it, but will not yet create the window handle.
- A top level window being represented with the Control class is not nice.
- ControlGroup being a friend with Control is a code smell
- Probably not use Window for both application class and system class
