---
layout: post
title:  "Tedious brainstorm"
date:   2021-11-10
categories: jekyll update
---

### Event Class Revisited
Discerning a satisfactorily fair class architecture for a gui is very tedious. But daily contemplation result in enduring evolution.

The need for Events returning bool instead of void is growing more crucial, and even though the previous interation concluded that having the need to have, or not to have, a return statement with a value in the Event class `operator()` function requires too much obscure complexity, it *should* be possible to do so nicely with c++20, so let's take another look.

**Given this class definition**  
accepting a `TResult` template parameter
```
template<typename TResult, typename... TArguments>
class Event {
	using TFunc = TResult (*)(TArguments..., void* userdata);
	TFunc _func;
	void* _userdata;
```

**The `operator()` function should be**  
using `std::is__void_v_` type trait

```
template<typename TResult, typename... TArguments>
TResult Event<TResult, TArguments...>::operator()(TArguments... args) const
{
	if(_func)
		if (std::is_void_v<TResult>) {
			_func(args..., _userdata);
			return;
		}
		else
			return _func(args..., _userdata);
	else if (std::is_void_v<TResult>)
		return;
	else
		return TResult{};
}
```

**But as expected it does not compile**  
Error	C2561	'shoujin::BoolEvent<int,int,int>::operator ()': function must return a value

**if constexpr - static if**  
Since c++17 constexpr can be applied to conditions.  
Now the return statement can be included or excluded at compile time!

And this is how Event can now support TResult :)
```
template<typename TResult, typename... TArguments>
TResult Event<TResult, TArguments...>::operator()(TArguments... args) const
{
	if(_func)
		if constexpr(std::is_void_v<TResult>)
			_func(args..., _userdata);
		else
			return _func(args..., _userdata);
	else if constexpr(!std::is_void_v<TResult>)
		return TResult{};
}
```
