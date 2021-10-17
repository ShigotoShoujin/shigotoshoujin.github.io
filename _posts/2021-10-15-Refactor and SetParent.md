---
layout: post
title:  "Refactor and SetParent"
date:   2021-10-15
categories: jekyll update
---

### Refactor
Looking at the Window.cpp constructor code leaves a bad smell. It's not self-documenting, and very hard to understand.  
By adding more tests and then refactoring the class, the outcome smells much better.  

Leaving bad smell would accumulate over time, and affect the motivation to go on.

It can be hard to write the code nicely on the first try, especially when experimenting.  
This is why the test / refactor cycle is good.

If the existing code is an obstacle to the refactor, move it aside.  
It's faster to write from scratch while looking at the previous version, than to change the existing trying not to break it.

### At which point to create the child window
- **First solution was to create the window handle in the constructor, and pass along the parent handle.**  
And then to pass that instance to the parent collection.  
This requires too much dependency, reading the parent handle, creating the child, then adding the child to the parent. Not nice.

- **Second solution, do as in .Net as discovered using ILSpy.**  
Use complicated logic to create the window handle as needed when adding to the parent collection.  
Too many features, too much hidden complexity, good for a framework like .Net.

- **Current solution**
Create handle in Ctor without knowing parent handle.  
Then set the parent handle later if/when adding to a parent control collection  
