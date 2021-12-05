---
layout: post
title:  "Tedious abnormal iterators"
date:   2021-12-04
categories: jekyll update
---

### Progress on UI
#### GUI class design
The gui class design seems to be working well, supporting windows / user controls / common controls nicely enough.  

#### Sizing and anchoring
Windows can now be sized and controls are anchoring as specified.  
This is enough to continue working on the color control

#### Color Control
The next fun step is to reproduce what the html 5 control does.  
https://www.w3schools.com/TAGS/tryit.asp?filename=tryhtml5_input_type_color

### Templated 2d Grid featuring iterating on rows / cells
The need to read and write bits from a bitmap lead to a desire to iterate though the pixels by rows then by single columns.  
This can be done, and was done in a POC, making it nice enough and simply requires a lot investigation, leading to much learning and time consumed.

Learning and making it nice is part of not leaving broken windows, and so it is worth it.

Here is a rough but working version, which does not support const iteration:  
https://codereview.stackexchange.com/questions/270281/2d-grid-iterating-by-rows-cells

Much understanding is learned though definitely not painless efforts.

The new version is posted here:  
https://codereview.stackexchange.com/questions/270693/2d-grid-iterating-by-rows-cells-take-2

### A note on accessing templated base classes
Given `class Grid : public Iterable<T*>` where `Iterable` have a public function `Reset`,  
that function cannot be simply called with `Reset();` since it depends on the template argument.  
It needs to be either called from `this->Reset();`, or from `Iterable<T*>::Reset();`
