---
layout: post
title:  "Keep looking and you shall find"
date:   2021-10-24
categories: jekyll update
---

### Remember, polymorphic instance must not be stored by value.
ControlGroup receives rvalue's of Control, where control can and will often be merely a base class of something bigger.
The collection of child control was stored by value, very nice and modern right?. But this break polymorphism.

This is to be expected, how can the real object size be known if all there is available is sizeof(Control), but it really was a ColorControl?

Instead of going for something completely unreasonable like adding cbsize variables and using a custom map, we'll just store pointers to Control as it is designed to be.

Control can still be passed by rvalue, but memory is going to be allocated and deallocated inside the ControlGroup class.

### Problems and solutions
Over the last few days the ColorControl would not be gently added as a child control, but adding a system control such as EDIT or BUTTON was just fine.
The desire to get to the bottom of it before applying the previously mentioned redesign proved strong enough to keep investigating.

Each win32 window handle is given a pointer to its corresponding class instance, but that pointer was not updated when moving around the data with move semantics.
This resolved by adding this single line to the Window move constructor:

    SetWindowLongPtr(hwnd, GWLP_USERDATA, (LONG_PTR)this);

There was also a separate issue waiting to cause trouble, with ControlGroup parent_window not being updated.
But now ControlGroup is inherited by Control, with private constructors and mutual friendship ensuring that no other class can inherit from ControlGroup.

This exclusivity allows to safely cast ControlGroup* as Control* in order to find out about his parent, and remove the need for a parent_control variable.
