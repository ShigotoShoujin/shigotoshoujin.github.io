---
layout: post
title:  "On events and templates"
date:   2021-10-30
categories: jekyll update
---

### Event Class
Testing the assertions is not nice when the code call ExitProcess during the test execution. Assertions must raise events to allow for custom behavior.

A first version of an event class was made and posted here [C++ Event class](https://codereview.stackexchange.com/questions/269531/generic-event-class)

Also the following events are now exposed from the assertion namespace:

    extern Event<LPCTSTR, LPCTSTR, int, LPCTSTR, bool&> GlobalOnError;
    extern Event<tstring, bool&> GlobalOnErrorOutput;
    extern Event<bool&> GlobalOnExitProcess;

Clearly a key feature is missing; To allow the listener to supply his own object, so that the listener's function can receive `userdata`.  
This goal is answered with the `UserDataEvent` class, which offer a templated `TUserData` which is constrained to be a pointer, using a custom c++20 concept.

Yet it's still broken, because the event template parameters are defined in the event source, the assertions, and the various `TUserData` are known only to the listeners.

Which is why we often see `void* userdata`.

In the C language, this solution is nice because void* can be implicitly cast to another type. So a function such as

    void OnClick(int x, int y, SomeClass* userdata)

can respond to an event defined as

    void (*)(int x, int y, void* userdata)

But in C++, `void*` cannot be implicitly cast to a different pointer type, and templates cannot be used because the type is not known at the time of the event definition.
Obscure solutions might exists within the knowledge of the universe; if so hopes are that they will be shared.

This reasoning strenghen the case for a templated event class having a hardcoded `void* userdata` as the last parameter. Less *modern* but very efficient;

#### Ref 02a9ad0af467ece2f151dc3477d133fe1f56375e

### New Event Class
RIP *UserDataEvent*

Changed Event class to always have a `void* userdata` argument.

This is what it looks like now:

    template<typename ...TArguments>
    class Event {
    	using TFunc = void (*)(TArguments..., void* userdata);
    	TFunc _func;
    	void* _userdata;
    
    public:
    	Event();
    	Event(TFunc func, void* userdata = nullptr);
    	Event& operator=(const Event& rhs);
    	~Event();
    
    	void operator()(TArguments... args) const;
    	operator bool() const;
    };
    

#### Ref 0ec51e94f3ec1ffc4e26f9e0a4f7a3e10052f78f
