---
layout: post
title:  "Anonymous Lambda"
date:   2021-11-01
categories: jekyll update
---

### Anonymous Lambda
In the following code, GetMessage return -1 on error

    while(_hwnd && GetMessage(&msg, _hwnd, 0, 0)) {
    	TranslateMessage(&msg);
    	DispatchMessage(&msg);
    }

In order to use the `SHOUJIN_ASSERT_WIN32` inline, without moving `GetMessage` outside of the `where`, two features are needed:
- The macro must return the GetMessage return value
- There must be a way to tell the macro what values represent an error, since the default win32 *false means error" pattern is not applicable

Since this is happening in a macro, encapsulating any declared variables in a new `{}` scope is mandatory to avoid name conflicts.  
How to have a block return a value? By defining an anonymous lambda and executing it right away!

    #define SHOUJIN_ASSERT_WIN32(fn) \
    	[&]() { \
    		auto ret = (fn); \
    		if(!ret) \
    			shoujin::assert::AbortWin32(TEXT(__FILE__), TEXT(__FUNCTION__), __LINE__, TEXT(#fn)); \
    		return ret; \
    	}()

And for when we have to check for failure using a specific value? Macro do not *simply* support default arguments, and in most cases a specific value is not needed, so let's just make a different macro such as the following, where `isok_func` is a lambda:

    #define SHOUJIN_ASSERT_WIN32_FUNC(fn, isok_func) \
    	[&]() { \
    		auto ret = (fn); \
    		if(!(isok_func(ret))) \
    			shoujin::assert::AbortWin32(TEXT(__FILE__), TEXT(__FUNCTION__), __LINE__, TEXT(#fn)); \
    		return ret; \
    	}()

And the final usage is kind of long, but working fine

    	auto isok_func = [](auto r) { return r != -1 /*GetMessage returns -1 on error*/; };
    	while(_hwnd && SHOUJIN_ASSERT_WIN32_FUNC(GetMessage(&msg, _hwnd, 0, 0), isok_func)) {
    #pragma warning(suppress : 6001) //Warning C6001 Using uninitialized memory - The anonymous lambda in SHOUJIN_ASSERT_WIN32 hides the fact that msg is initialized
    		TranslateMessage(&msg);
    		DispatchMessage(&msg);
    	}
    ...

