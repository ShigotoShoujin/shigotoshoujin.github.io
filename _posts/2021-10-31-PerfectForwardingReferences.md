---
layout: post
title:  "Perfect forwarding references"
date:   2021-10-31
categories: jekyll update
---

### Perfect Forwarding References with class functions
Given that "beauty is in the eye of the beholder", and having just learned about std::forward,
let's proceed to make our *beautiful* event class perfectly forward the `operator()` arguments to its _func pointer.

    template<typename... TArguments>
    void Event<TArguments...>::operator()(TArguments&&... args) const
    {
    	if(_func)
    		_func(std::forward<TArguments>(args)..., _userdata);
    }

Yes, very good. But then what is this error?
> cannot convert argument 1 from 'int' to 'int &&'

After too much time isolating the issue, it can be minimally reproduced with

    template<typename T>
    struct PerfectForwardingProblem {
    	void ClassFunc(T&& arg) {}
    };
    
    template<typename T>
    void Func(T&& arg) {}
    
    void Demo()
    {
    	PerfectForwardingProblem<int> pfp;
    	int x = 1;
    
    	pfp.ClassFunc(1); // OK
    	Func(1); // OK
    
    	pfp.ClassFunc(x); //cannot convert argument 1 from 'int' to 'T &&'
    	Func(x); // OK
    
    	pfp.ClassFunc(std::move(x)); // OK
    	Func(std::move(x)); // OK
    }

Why? Because, to quote cppreference.com:
> [std::forward](https://en.cppreference.com/w/cpp/utility/forward)  
> [forwarding references](https://en.cppreference.com/w/cpp/language/reference#Forwarding_references)  
> a function argument that is declared as an rvalue reference to a cv-unqualified function template parameter

So does not work on member function using types from the class template.  
It seems that many have tried, as seen from [Perfect forwarding with class template argument deduction](https://stackoverflow.com/a/45791389/17172063)
